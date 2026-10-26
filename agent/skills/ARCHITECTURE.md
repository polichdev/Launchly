# Launchly — Architecture

## Overview
Business automation platform with Telegram bot builder and CRM.
Allows business owners to create Telegram bots via a node-based constructor, manage orders/leads, and automate communication in real-time.

---

## Tech Stack

### Backend
- **Core**: Java 21, Spring Boot 4.x
- **Security**: Spring Security, JWT, Google OAuth2
- **Data**: Spring Data JPA (Hibernate), PostgreSQL 16 (relational data), Redis 7 (dialog sessions, counters, locks, caches)
- **Real-Time**: Spring WebSocket + STOMP (for real-time notifications to bot owners)
- **Integration**: OkHttp, MapStruct, Lombok, Liquibase

### Frontend
- **Core**: React 18, TypeScript, Vite
- **UI & Layout**: React Flow (for node-based constructor canvas), TailwindCSS, shadcn/ui
- **State & Sync**: Zustand (client-side state), TanStack Query + Axios (server-side async data sync), Socket.io-client / STOMP WebSocket client

---

## Project Structure & Module Details

### Backend Package Structure
```text
com.launchly/
├── auth/                       # Authentication Domain
│   ├── controller/             # AuthController
│   ├── dto/                    # LoginRequest, RegisterRequest, AuthResponse
│   ├── entity/                 # User, RefreshToken, Role, Provider
│   ├── repository/             # UserRepository, RefreshTokenRepository
│   └── service/                # TokenService, AuthService
│
├── bot/                        # Bot Flow & Telegram Handler Domain
│   ├── controller/             # BotController, TelegramWebhookController
│   ├── dto/                    # BotRequest, BotResponse, FlowSchemaRequest
│   ├── engine/                 # Dialog state evaluation
│   │   ├── executor/           # NodeExecutors (Message, Input, Order, Lead, ApiCall, Tag, etc.)
│   │   └── model/              # FlowSchema, FlowNode, FlowEdge
│   ├── entity/                 # Bot, FlowSchema, BotUser, NodeType
│   ├── repository/             # BotRepository, FlowSchemaRepository, BotUserRepository
│   └── service/                # BotService, FlowEngineService, TelegramSendService
│   └── telegram/               # TelegramBotManager, BotUpdateHandler
│
├── crm/                        # Customer Relationship Management Domain
│   ├── controller/             # CrmController
│   ├── dto/                    # OrderResponse, LeadResponse, ConversationResponse, MessageResponse
│   ├── entity/                 # Order, Lead, Conversation, Message, OrderStatus, LeadStatus, SenderType
│   ├── repository/             # OrderRepository, LeadRepository, ConversationRepository, MessageRepository
│   ├── service/                # CrmService (impl: CrmServiceImpl)
│   └── websocket/              # CrmWebSocketService (STOMP broker wrappers)
│
├── broadcast/                  # Marketing Campaign Domain
│   ├── controller/             # BroadcastController
│   ├── dto/                    # CampaignRequest, CampaignResponse, TagResponse
│   ├── entity/                 # BroadcastCampaign, Tag, CampaignStatus
│   ├── repository/             # CampaignRepository, TagRepository
│   └── service/                # BroadcastService, TagService, BroadcastFilterService, BroadcastScheduler
│
├── billing/                    # Subscription & Payment Domain
│   ├── controller/             # BillingController, StripeWebhookController
│   ├── dto/                    # CheckoutRequest, CheckoutResponse, PlanResponse, SubscriptionResponse
│   ├── entity/                 # Plan, Subscription, SubscriptionStatus
│   ├── mapper/                 # BillingMapper
│   ├── repository/             # PlanRepository, SubscriptionRepository
│   └── service/                # BillingService, PlanLimitService
│
├── integration/                # CRM Third-Party Integration Domain
│   ├── controller/             # IntegrationController
│   ├── dto/                    # ExportDataType, ExcelExportResult, IntegrationResponse, IntegrationCreateRequest, GoogleSheetsConfig, WebhookConfig, ExcelConfig
│   ├── entity/                 # Integration, IntegrationType
│   ├── mapper/                 # IntegrationMapper
│   ├── repository/             # IntegrationRepository
│   └── service/                # IntegrationService, GoogleSheetsService, ExcelExportService, WebhookService, IntegrationEventService
│
├── ai/                         # AI Assistant & Schema Generation Domain
│   ├── client/                 # GroqClient
│   ├── controller/             # AiController
│   ├── dto/                    # GroqMessage, AiChatRequest, AiSchemaRequest, AiChatResponse, AiSchemaResponse, AiUsageResponse
│   ├── entity/                 # AiUsage
│   ├── repository/             # AiUsageRepository
│   └── service/                # AiService, AiUsageService
│
├── media/                      # Central Media & Asset Management Domain
│   ├── config/                 # CloudinaryConfig
│   ├── controller/             # MediaController
│   ├── dto/                    # MediaUploadResponse
│   └── service/                # MediaService
│
└── common/                     # Cross-Cutting Concerns
    ├── config/                 # SecurityConfig, WebSocketConfig, JacksonConfig, CacheConfig
    ├── exception/              # AppException, ErrorResponse, GlobalExceptionHandler
    └── security/               # JwtFilter, OAuth2SuccessHandler, CustomUserDetails
    └── validation/             # ValidTelegramToken, TelegramTokenValidator
    └── utils/                  # EncryptionUtil, SanitizationUtil
```

### Frontend Directory Structure
```text
frontend/src/
├── components/                 # Shared components layer
│   ├── layouts/                # AuthLayout, DashboardLayout
│   ├── ui/                     # shadcn/ui library elements
│   └── shared/                 # PrivateRoute, PublicRoute
│
├── features/                   # Feature-based business domains
│   ├── auth/                   # Login, signup, OAuth callback flows
│   │   ├── api/
│   │   ├── components/
│   │   ├── hooks/              # useLoginForm, useLoginMutation
│   │   ├── pages/              # LoginPage, RegisterPage, OAuth2Callback
│   │   └── types/
│   ├── dashboard/              # Home stats and user profile views
│   │   └── pages/              # DashboardPage
│   ├── bot/                    # Bot Flow nodes, editor canvas, settings pages
│   ├── crm/                    # Orders lists, leads tables, live chats
│   ├── broadcast/              # Newsletters schedulers, marketing logs
│   ├── billing/                # Plans limits, checkout sessions
│   ├── integration/            # Configs forms for sheets/webhooks
│   └── ai/                     # Assistant completion prompts UI
│
├── hooks/                      # Shared React hooks
├── lib/                        # Global configs (axios.ts client, queryClient.ts)
├── store/                      # Zustand stores (useAuthStore.ts)
├── types/                      # Global API DTO schemas (auth.ts)
├── router/                     # Router configurations (index.tsx)
├── App.tsx                     # Providers bootloader
└── main.tsx                    # ReactDOM bootstrap entrypoint
```

---

## Module Architectural Details

### 🤖 Bot Module (Flow Engine & Telegram)
* **Flow Engine**: Parses and executes flow graphs defined by bot owners.
  * **Execution Loop**: Walk starts at the `START` node. When executing, the engine evaluates the current node type, triggers the corresponding `NodeExecutor`, and proceeds along target edges.
  * **State Evaluation**: Evaluates conditional expressions in `CONDITION` nodes, looking up saved variables in the user's Redis session context.
* **NodeExecutors**: Implementations of `FlowNodeExecutor` for each node type:
  * `START`: Entry node.
  * `MESSAGE`: Sends a configured text message (sanitized before sending).
  * `BUTTON`: Sends keyboard buttons or inline buttons.
  * `INPUT`: Stops execution, waits for incoming Telegram message, and saves user response to a session variable in Redis.
  * `CONDITION`: Branches flow dynamically depending on variables or user replies.
  * `ORDER`: Persists a checkout order in the database and fires a CRM alert.
  * `LEAD`: Extracts user contact details (phone, email) and registers a Lead in CRM.
  * `API_CALL`: Fires custom HTTP requests dynamically using template variables.
  * `TAG`: Attaches a label to the user for future campaign filtering.
  * `END`: Terminates active flow.
* **TelegramBotManager**: Manages active Telegram bots.
  * **Polling Mode** (`telegram.mode=polling`): Runs local polling threads using `TelegramBotsLongPollingApplication`.
  * **Webhook Mode** (`telegram.mode=webhook`): Configures endpoints `/api/v1/telegram/webhook/{token}` for high-performance production dispatches.

### 📊 CRM Module (Conversations & Real-Time)
* **Conversations**: Encapsulates a complete chat log between a single `BotUser` and a `Bot`. Conversations can be closed or open.
* **Messages**: Tracks chronological messaging history. Both incoming messages (from bot users) and outgoing replies (from bot owners via CRM workspace) are saved.
* **WebSocket STOMP Integration**: Allows owners to receive updates instantly without API polling.
  * Active owners subscribe to specific channels on the WebSocket broker.
  * Back-end triggers WebSocket pushes on DB updates via `CrmWebSocketService`.

### 📢 Broadcast Module (Campaign Segmentation & Limits)
* **Tags**: Arbitrary labels mapped to `BotUser` profiles (many-to-many relationship) to segment audiences.
* **BroadcastFilterService**: Matches target bot users dynamically based on tag filter groups.
* **BroadcastScheduler & Rate Limiting**:
  * Broadcast campaigns are dispatched asynchronously in background threads.
  * **Telegram Rate Limits Protection**: Telegram enforces a strict limit of **30 messages per second** globally across a bot, and **1 message per second** to a specific user. The scheduler implements a token-bucket or thread-delay throttler to prevent `429 Too Many Requests` API failures.

### 💳 Billing Module (Subscription & Payments)
* **Plans & Subscriptions**: Manages system billing plans (`FREE`, `STARTUP`, `ENTERPRISE`) and active client subscriptions synchronized via Stripe payments.
* **Stripe Webhooks**: Listens to Stripe events (`checkout.session.completed`, `customer.subscription.updated`, `customer.subscription.deleted`) to provision or teardown limits dynamically.
* **Plan Limits Verification**: Exposes `PlanLimitService` to validate user request actions (e.g. maximum bot counts, active CRM users, campaign broadcasts access, third-party integrations, and daily AI assistant request limits) according to the user's active plan tier.

### 🔌 Integrations Module (Sync, Exports & Webhooks)
* **Google Sheets Sync**: Dynamically exchanges OAuth authorization codes and auto-refreshes tokens. Asynchronously appends order/lead row arrays using the Google Sheets REST API.
* **Excel Export**: Generates binary spreadsheets styled with Apache POI, returning a unified `ExcelExportResult` to keep controllers decoupled from header construction.
* **Webhook Delivery**: Dispatches POST dispatches signed with HMAC-SHA256 headers matching the owner's webhook secrets, using a retry mechanism up to 4 total attempts.

### 🧠 AI Module (Groq Client & Usage limits)
* **Groq Chat Assistant**: Facilitates completion prompts containing Launchly business assistant rules and chat history.
* **Schema Generation**: Resolves visual node constructor graph schemas from descriptions, ensuring exactly one `START` node is declared.
* **Daily Caching Limits**: Enforces a 20 request/day cap for the `FREE` tier, cache-storing usage counts in Redis for fast validation.

### 🖼️ Media Module (Assets & Storage)
* **Central Asset Portal**: Serves as the single access point for uploading and deleting media files (avatars, product photos).
* **Automatic Optimizations**: Automatically resizes files to a maximum of 1200x1200px, optimizes visual quality (`q_auto`), and converts uploads to next-gen WebP format (`f_auto`).
* **Secure Ownership Boundaries**: Restricts delete requests by checking that the targeted public ID matches the prefix pattern `launchly/{userId}/` of the authenticated user.
* **Dynamic Wildcards**: Maps the delete path pattern as `/{*publicId}` to correctly capture multi-segmented Cloudinary IDs containing slashes.

---

## Redis Key Patterns

All cached states in Redis are prefixed with the project namespace to avoid key collisions.

| Pattern | Data Type | TTL | Description |
|---|---|---|---|
| `launchly:bot:dialog:{botId}:{telegramId}` | Hash | 24 Hours | Stores current flow node, dialog state variables, and inputs. |
| `launchly:bot:schema:{botId}` | String | 30 Minutes | Stores visual flow schema serialized as `CachedSchema` (nodes and edges) to avoid Hibernate recursion issues. |
| `launchly:auth:token:blacklist:{token}` | String | Token Expire | Blacklisted JWT refresh tokens after logouts. |
| `launchly:broadcast:campaign:{campaignId}:progress` | Hash | 7 Days | Tracks count of sent, failed, and total messages during dispatches. |
| `launchly:broadcast:campaign:lock:{campaignId}` | String | 1 Hour | Distributed lock (Redisson / SETNX) to prevent dual dispatches. |
| `launchly:ai:usage:{userId}:{date}` | String | 25 Hours | Caches daily AI request counts for rate limiting validation. |
| `launchly:subscription:{userId}` | String | 10 Minutes | Spring Cache: owner's Stripe subscription details. |
| `launchly:subscription:plan:{userId}` | String | 10 Minutes | Spring Cache: owner's active plan tier. |
| `launchly:plan:{planId}` | String | 60 Minutes | Spring Cache: plan limits definition. |
| `launchly:bots:{userId}` | String | 5 Minutes | Spring Cache: owner's bot list. |
| `launchly:tags:{botId}` | String | 5 Minutes | Spring Cache: bot tags list. |

---

## Real-Time WebSocket Topics

The backend uses a STOMP message broker to push real-time CRM updates to authenticated owners:

* `/topic/crm/{botId}/orders` — Broadcasts new orders and order status updates.
* `/topic/crm/{botId}/leads` — Broadcasts new lead captures and status transitions.
* `/topic/crm/{botId}/messages` — Pushes live incoming/outgoing chat messages to the owner's UI.

---

## Flow Node Types (`NodeType` enum)

* **`START`** — Beginning of flow execution.
* **`MESSAGE`** — Sends text message to Telegram client.
* **`BUTTON`** — Sends interactive option buttons.
* **`INPUT`** — Prompts for user input and halts execution until a reply is received.
* **`CONDITION`** — Directs execution path based on session variables.
* **`ORDER`** — Creates a customer invoice/order in the CRM.
* **`LEAD`** — Registers a lead profile with name, email, and phone.
* **`API_CALL`** — Executes external Web API integrations.
* **`TAG`** — Tags user session with campaign labels.
* **`END`** — Closes flow path.

---

## Unified REST Exception Format

All REST API exceptions are serialized to client workspaces using a standard and predictable JSON schema:

```json
{
  "status": 400,
  "error": "Bad Request",
  "message": "Detailed and readable validation error message",
  "path": "/api/v1/bots",
  "timestamp": "2026-06-07T09:10:09.123"
}
```