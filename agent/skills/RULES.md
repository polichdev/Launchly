# Launchly — Code Rules & Conventions

## General Rules
- Language: Java 21, Spring Boot 4.x (Backend), React 18, TypeScript, Vite (Frontend)
- All code and comments in English
- Keep methods, classes, and components focused on a Single Responsibility (SRP)
- No direct database entity leaks in Controllers (always map to DTOs)
- Decouple layers cleanly: no business logic in Controllers, only payload formatting, validation, and HTTP dispatching.

---

## Backend Layer-by-Layer Rules

### 1. Controllers
- Only handle HTTP mapping, payload parsing, and HTTP status codes.
- Do NOT invoke Repositories directly. Always call Services.
- Return `ResponseEntity<T>` for clarity.
- Validate incoming bodies with `@Valid` or `@Validated`.
- Avoid `try/catch` blocks inside endpoint methods; let exceptions bubble up to `GlobalExceptionHandler`.
- Delegate formatting and content construction (e.g. building dynamic Excel binary files, setting custom download/stream headers) to Services to maintain clean separation of concerns.

### 2. Services
- Always follow the Interface + Implementation pattern (e.g., `CrmService` and `CrmServiceImpl`).
- All core business operations, computations, and logic belong in this layer.
- Retrieve data via Repositories or delegate to other Services.
- Return DTOs, never domain Entities, to upper layers. Map elements using MapStruct.
- Wrap modifications in `@Transactional`. Keep blocking network calls (e.g. external API HTTP requests) out of active database transactions where possible.
- **Polymorphic Configuration Validation**: Map generic JSON fields to strongly-typed records (like `GoogleSheetsConfig`, `WebhookConfig`, `ExcelConfig`) first. Then validate them programmatically using `jakarta.validation.Validator` before processing or saving.

### 3. Repositories
- Extend `JpaRepository<Entity, Long>`.
- Use JPQL (`@Query`) for custom queries. Use native SQL queries ONLY when database-specific syntax (like Postgres JSONB functions) is required.
- Maintain query method name conventions conforming to Spring Data JPA.
- **SQL Injection Safety**: Never construct JPQL or SQL queries via string concatenation (`+`). Always use named parameter bindings (e.g. `:userId`).

### 4. Entities
- Inherit common properties (`id`, `createdAt`, `updatedAt`) by extending `BaseEntity`.
- Avoid business logic; keep fields as pure data structures with getters/setters/builders.
- Specify fetch strategy explicitly (`fetch = FetchType.LAZY` for `@ManyToOne` and `@OneToMany` relationships).

### 5. DTOs (Data Transfer Objects)
- Distinguish between Request and Response structures (located in `dto/request/` and `dto/response/`).
- Use Java `record` classes for immutable, concise DTO declarations.
- Enforce validation constraints directly on request parameters (`@NotBlank`, `@Email`, `@Min`, etc.).
- **Custom Validators**: Use custom constraint annotations (e.g. `@ValidTelegramToken` / `@Constraint`) to validate complex field formats cleanly.
- Polymorphic configuration structures (e.g., integration configs dynamic per type) must be mapped to strongly-typed records (like `GoogleSheetsConfig`, `WebhookConfig`, `ExcelConfig`) before performing field validations.

### 6. MapStruct Mappers
- Every domain has a dedicated mapper interface (e.g., `CrmMapper`, `AuthMapper`).
- Annotate with `@Mapper(componentModel = "spring")`.
- Hand-written mappings should be rare; define conversions declaratively using MapStruct annotations.

### 7. Caching (Redis)
- Use Spring Cache annotations (`@Cacheable`, `@CacheEvict`) for declarative caching.
- Separate cache namespaces and keys cleanly (e.g., separating DTO and Plan keys under `"subscription"` via custom key prefixes) to prevent collisions.
- Use `StringRedisTemplate` / `RedisTemplate` directly for manual, highly custom caching requirements (like rate limit counters or live graph schemas).
- **Serialization Safety**: Never serialize database JPA entities directly to Redis. Wrap objects in lightweight record shell classes (like `CachedSchema`) to protect against Hibernate lazy-loading exceptions and database circular reference failures.

### 8. Input Sanitization
- **Telegram Deliveries**: To prevent formatting breaks (unclosed HTML tags, etc.) or visual styling manipulation in Telegram client chats, always sanitize text strings using `SanitizationUtil.sanitizeForTelegram(text)` in executors and campaign schedulers before dispatching to the Telegram API.

---

## Backend Advanced Rules & Features Conventions

### 1. Asynchronous Execution (`@Async`)
- Use `@Async` for heavy operations, background dispatches, and third-party integrations (e.g., executing Broadcast campaigns).
- Async methods must be defined in separate Spring-managed Beans (calling an `@Async` method from the same class bypasses the Spring AOP proxy).
- Async tasks must run on a configured thread pool executor bean (defined in `AsyncConfig` with limits on max pool size, queue capacity, and execution rejection policies) rather than the default task executor.

### 2. Scheduled Tasks (`@Scheduled`)
- Periodic cron jobs and schedules must ONLY reside in classes named and annotated as `*Scheduler` (e.g., `BroadcastScheduler`).
- Never scatter `@Scheduled` methods across general services or controllers.
- Ensure proper logging and transaction separation for scheduled cycles.

### 3. Telegram Message Dispatching
- **CRITICAL**: Never trigger message dispatches directly from `TelegramBotManager` or OkHttp client inside crm or business services.
- **Always** invoke `TelegramSendService.sendMessage(botId, telegramUserId, text)` to send messages. This protects modularity boundaries between the `crm` and `bot` domains.

### 4. Audience Tags Segmentation
- Tags are used for audience filtering and campaign broadcasts.
- Tags must be managed, fetched, and registered dynamically using `TagService.getOrCreateTag(botId, name)`. This prevents duplicate tag records.

### 5. WebSockets Broker Wrapper
- Do NOT auto-wire or invoke `SimpMessagingTemplate` directly inside controller endpoints or business logic services.
- **Always** use `CrmWebSocketService` methods (e.g. `notifyNewOrder`, `notifyNewMessage`) to push real-time STOMP alerts to subscribers.

### 6. Authentication Context (`SecurityUtil` / `@AuthenticationPrincipal`)
- Resolve the currently authenticated system user or their database ID using `SecurityUtil` or direct parameter injection:
  `@AuthenticationPrincipal CustomUserDetails userDetails`
- Avoid database user lookups from username strings on authenticated routes.

### 7. Integration Sync & Event Callbacks
- **Asynchronous Execution**: External integrations (Google Sheets appends, Webhook calls) must be processed asynchronously using events (e.g. `IntegrationEventService`) to avoid blocking primary database operations in the CRM and Bot flow.
- **Webhook Deduplication**: Implement idempotency filters using entities updates or request hashes to avoid posting duplicate Webhook requests on retries.
- **Secure OAuth redirects**: Google oauth callback endpoints must verify a signed server-generated JWT `state` parameter containing bot and user session details to prevent state forgery (CSRF) attacks.
- **Inject Network Clients**: Do not create inline `HttpClient` or RestTemplate wrappers; always inject a configured `HttpClient` bean via dependency injection for proper connection management.

### 8. AI Assistant Limits & Validation
- **Module Encapsulation**: Keep Groq clients and AI helpers completely inside the `ai` module packages.
- **Daily Request Caching Limits**: Enforce request count limits (e.g. 20 requests per day on FREE plan) by verifying counts in a low-latency cache like Redis with 24-25 hour TTLs before reaching third-party AI APIs.
- **Schema Validation**: Programmatically validate generated visual graph structures to guarantee syntactic correctness (e.g. verifying exactly one `START` node is present) before returning them to client workspaces.

### 9. Media & File Uploads (Cloudinary)
- **File Constraints**: Enforce a strict file size limit of 5MB and restrict allowed content types exclusively to: `image/jpeg`, `image/png`, `image/webp`, `image/gif`.
- **Folder Sanitization**: To prevent directory traversal and access bypasses, validate that the target folder param contains only alphanumeric characters, dashes, or underscores (`^[a-zA-Z0-9_-]+$`).
- **Owner Ownership Checks**: Validate deletion requests by ensuring the targeted `publicId` starts with the owner's prefix pattern `launchly/{userId}/` before forwarding requests to the Cloudinary API.
- **Dynamic Wildcards**: Map the delete path pattern as `/{*publicId}` to correctly capture multi-segmented Cloudinary IDs containing slashes.

---

## Backend Naming & Schema Conventions

### Database Tables (snake_case)
- `users`
- `bots`
- `flow_schemas`
- `bot_users`
- `tags`
- `bot_user_tags`
- `broadcast_campaigns`
- `conversations`
- `messages`
- `orders`
- `leads`
- `plans`
- `subscriptions`
- `integrations`
- `ai_usages`

### REST API Endpoints
- Base Prefix: `/api/v1/`
- Auth: `/api/v1/auth/`
- Bots: `/api/v1/bots/`
- CRM: `/api/v1/crm/`
- Broadcast: `/api/v1/broadcast/`
- Billing: `/api/v1/billing/`
- Integrations: `/api/v1/integrations/`
- AI: `/api/v1/ai/`
- Media: `/api/v1/media/`

### Redis Keys
- Pattern format: `launchly:{domain}:{sub-key}:{id}`
  - Example: `launchly:bot:dialog:1:1234567`
- Set appropriate TTLs on all dynamic keys to avoid cache bloat.

### Liquibase Migrations
- All table adjustments, columns, and indexes must be written in declarative SQL files inside `db/changelog/changes/`.
- Every migration file must start with the header `--liquibase formatted sql` and define unique changesets.
- Named: `V{Index}__descriptive_change.sql` (e.g., `V006__add_order_sequence_to_bots.sql`).

---

## Frontend Coding Conventions

### 1. File Structure

**Layouts** — separate files inside `src/components/layouts/`:
- `AuthLayout.tsx` — wrapper layout for auth-related pages.
- `DashboardLayout.tsx` — sidebar + header wrapper for authenticated users.

**Router** — all routing configuration inside `src/router/index.tsx`.
`App.tsx` contains ONLY context providers (QueryClientProvider, etc.).

**Pages** — each route page resides in `features/<feature>/pages/`.
Do NOT create a general `src/pages/` folder for feature-specific pages.

**Types** — global API and shell types go inside `src/types/`, while feature-specific TypeScript interfaces reside in `features/<feature>/types/`.

### 2. API Hooks — Mandatorily via TanStack Query

```tsx
// [FORBIDDEN] — direct API request call in presentation component
const onSubmit = async (data) => {
  const response = await loginApi(data);
}

// [CORRECT] — wrapped via query/mutation hooks
// features/auth/hooks/useLoginMutation.ts
export const useLoginMutation = () => {
  const login = useAuthStore((state) => state.login);
  return useMutation({
    mutationFn: loginApi,
    onSuccess: (data) => login(data.accessToken, data.refreshToken, data.user),
  });
};

// In presentation page
const { mutate, isPending } = useLoginMutation();
const onSubmit = (data: LoginFields) => mutate(data);
```

### 3. Type Safety — Forbidden any in API clients

```tsx
// [FORBIDDEN]
export const loginApi = async (credentials: any): Promise<AuthResponse>

// [CORRECT]
export const loginApi = async (credentials: LoginRequest): Promise<AuthResponse>
```

### 4. Components — Separation of Logic and UI View

```tsx
// [FORBIDDEN] — logic and UI combined in page component
const LoginPage = () => {
  const [isLoading, setIsLoading] = useState(false);
  const onSubmit = async (data) => {
    setIsLoading(true);
    try { await loginApi(data); }
    ...
  }
  return ...;
}

// [CORRECT] — logic isolated in hook, UI component reads state
// hooks/useLoginForm.ts
export const useLoginForm = () => {
  const { mutate, isPending, error } = useLoginMutation();
  const form = useForm({ resolver: zodResolver(loginSchema) });
  return { form, onSubmit: form.handleSubmit(mutate), isPending, error };
}

// pages/LoginPage.tsx
const LoginPage = () => {
  const { form, onSubmit, isPending } = useLoginForm();
  return ...;
}
```

### 5. Error Handling — Centralized

```tsx
// src/lib/axios.ts — global interceptor handling standard payload format
apiClient.interceptors.response.use(
  (response) => response,
  (error) => {
    const message = error.response?.data?.message || 'Something went wrong';
    return Promise.reject(new Error(message));
  }
);

// In presentation page — simply render alert
const { error } = useLoginMutation();
{error && <Alert>{error.message}</Alert>}
```

### 6. Zustand vs TanStack Query State Division

```tsx
// In Zustand — auth profiles, active settings, UI toggles
useAuthStore — user, tokens
useUIStore   — sidebarOpen, theme, activeBot

// In TanStack Query — server state caching and sync
useBots()           — list of bots
useOrders(botId)    — orders data
useConversations()  — active CRM threads
```

### 7. Zustand Store Synchronisation & References
- Never put side-effects updating a client-side Zustand store directly inside `queryFn` of a React Query hook. Perform synchronization using a `useEffect` inside the custom query hook.
- Persist only entity IDs (e.g. `activeBotId: number` instead of the full `activeBot` object) in Zustand stores to prevent stale data cache issues.

### 8. Forms & Schema Validation
- Handle form validation using `react-hook-form`, `zod` schemas, and `@hookform/resolvers/zod`. Do not use manual, ad-hoc state-based validation checks.
- Display validation error messages next to their respective fields inside the form view.

### 9. Config Modularization
- Extract hardcoded layouts configs, tab options, feature specifications, and pricing plan lists into separate files under `features/<feature>/config/` instead of hardcoding them inside page components.