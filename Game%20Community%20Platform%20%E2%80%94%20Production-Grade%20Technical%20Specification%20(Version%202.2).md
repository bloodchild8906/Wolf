**Document Version:** 2.2
**Date:** September 03, 2025
**Author:** Michael (divide by zero studios)
**Purpose:** This specification defines a scalable, secure, and extensible platform for game community management, initially targeting ARK: Survival Evolved with support for other games via plugins. It consolidates all prior versions (v2.0, v2.1, v2.2), incorporating core features, event automations, and new enhancements for timezone handling, event calendar, enhanced event creation UI, and a testing mode for events using a separate server.

## Table of Contents
- [1. Executive Summary](#1-executive-summary)
- [2. Strategic Vision](#2-strategic-vision)
- [3. User Personas](#3-user-personas)
- [4. Technical Stack](#4-technical-stack)
- [5. Core Features & Wireframes](#5-core-features--wireframes)
- [6. System Architecture](#6-system-architecture)
- [7. Data Flow Diagrams](#7-data-flow-diagrams)
- [8. API Endpoints](#8-api-endpoints)
- [9. Security & Compliance](#9-security--compliance)
- [10. Deployment & Infrastructure](#10-deployment--infrastructure)
- [11. Development Roadmap](#11-development-roadmap)
- [12. Testing Strategy](#12-testing-strategy)
- [13. Monitoring & Observability](#13-monitoring--observability)
- [14. Assumptions, Risks, Constraints](#14-assumptions-risks-constraints)
- [15. Conclusion](#15-conclusion)
- [Scaffolding the Solution](#scaffolding-the-solution)
- [Prerequisites](#prerequisites)
- [Step 1: Create Solution and Projects](#step-1-create-solution-and-projects)
- [Step 2: Add Dependencies](#step-2-add-dependencies)
- [Step 3: Folder Structure](#step-3-folder-structure)
- [Step 4: Key Files](#step-4-key-files)
- [Step 5: Infrastructure Setup](#step-5-infrastructure-setup)
- [Step 6: Kubernetes Stub](#step-6-kubernetes-stub)
- [Step 7: Next Steps](#step-7-next-steps)

---

## 1. Executive Summary
The Game Community Platform is a self-hostable, cloud-ready solution built with .NET 9.0 MAUI Blazor Hybrid for cross-platform clients (desktop, mobile, web) and MongoDB for flexible data storage. It integrates a headless CMS (Orchard Core), Discord-based authentication, event management with advanced automations, and administrative tools like an RCON terminal and ArkShop manager.

### Core Features (v2.0)
- **Headless CMS:** Dynamic content management via Orchard Core.
- **RCON Terminal:** Secure, web-based server management with macro support.
- **ArkShop Manager:** GUI for in-game economy configuration.
- **Event System:** Create, RSVP, and sync events with Discord notifications.
- **Real-Time Updates:** SignalR for live server status and notifications.
- **Security:** JWT-based auth, RBAC, OWASP compliance.
- **Scalability:** Microservices-inspired, Kubernetes-ready architecture.
- **Extensibility:** Plugin system for future game integrations.

### Enhanced Features (v2.1)
- **Event-Triggered Automations:** Execute RCON command sequences at event start.
- **Discord Notifications:** Real-time announcements via bot for event start/end.
- **Temporary Shop Modifications:** Apply custom ArkShop configs during events, revert at end.

### New Features (v2.2)
- **Timezone Handling:** Events stored in UTC; client UI converts to user-selected timezone.
- **Event Calendar:** Interactive calendar UI for viewing and managing events.
- **Enhanced Event Creation UI:** Form with title, description, start/end times (with timezone picker), RCON commands, and temporary shop configs.
- **Event Testing Mode:** Execute automations on a separate test server for preview.

### Key Benefits
- Consolidates fragmented tools into one platform.
- Automates event workflows, reducing manual effort.
- Ensures global accessibility with timezone support.
- Provides safe testing to prevent unintended changes.
- Supports multiple communities via multi-tenancy.

---

## 2. Strategic Vision
The platform streamlines game community operations by providing a cohesive, extensible solution. It addresses pain points like disjointed tools, poor mobile UX, scalability issues, and manual event management. The new features enhance usability for global users and ensure reliable, testable automations. Future expansions include AI moderation, gamification, and integrations with Steam or Twitch.

**CMS Choice:** Orchard Core for its open-source, modular design, headless APIs (GraphQL/REST), multi-tenancy, and workflow engine.

---

## 3. User Personas
- **Server Admin (Technical):**
- **Goals:** Efficient server management, shop configuration, monitoring, automated events, safe testing.
- **Needs:** Intuitive RCON terminal, macro automation, real-time logs, event creation with timezone support, testing mode, calendar view.
- **Community Member (Casual):**
- **Goals:** Stay updated, RSVP to events, view events in local time.
- **Needs:** Mobile-friendly UI, clear notifications, interactive calendar.
- **Content Creator (Moderator):**
- **Goals:** Publish news/guides, manage workflows, create engaging events.
- **Needs:** Rich-text editor, approval system, analytics, robust event creation UI.

---

## 4. Technical Stack
### Frontend
- **Framework:** .NET MAUI Blazor Hybrid (AOT-enabled).
- **UI:** MudBlazor (responsive, includes calendar component).
- **State:** Fluxor (Redux-style).
- **Real-Time:** SignalR.
- **Offline:** Blazored.LocalStorage with IndexedDB/SQLite sync.
- **Timezone:** System.TimeZoneInfo for conversions.

### Backend
- **API:** ASP.NET Core 9.0 Web API.
- **Auth:** ASP.NET Identity + Discord OAuth2.0.
- **Authz:** Role-Based Access Control (RBAC).
- **CMS:** Orchard Core (headless).
- **Messaging:** MassTransit with RabbitMQ.
- **Jobs:** Hangfire (schedules event start/end).
- **Analytics:** Application Insights.

### Data Layer
- **Primary DB:** MongoDB (game data, events, macros, configs).
- **CMS DB:** PostgreSQL (Orchard Core).
- **Cache:** Redis (sessions, real-time data).
- **Search:** MongoDB Atlas Search.

### Infrastructure
- **Containerization:** Docker + Docker Compose.
- **Orchestration:** Kubernetes (with Helm).
- **CI/CD:** GitHub Actions.
- **Monitoring:** Serilog + Seq, Application Insights.
- **Security:** Let’s Encrypt SSL, OWASP compliance.
- **Testing Server:** Separate ARK server instance.

---

## 5. Core Features & Wireframes
### 5.1. RCON Terminal & Macros (v2.0)
- **Features:** Secure web-based console, command history, auto-completion, macro storage (MongoDB), real-time streaming via SignalR.
- **Wireframe:** Split-pane layout (left: server selector/macros; center: terminal; right: logs/help).

### 5.2. ArkShop Management (v2.0)
- **Features:** GUI for editing shop items, prices, categories; import/export configs; live reload via RCON; preview mode.
- **Wireframe:** Tabbed interface (Items, Config, Preview).

### 5.3. Event Management (Enhanced v2.1, v2.2)
- **Features:**
- Create/RSVP events with Discord sync.
- Execute RCON command sequences at event start.
- Apply temporary ArkShop configs, revert at end.
- Notify Discord bot on start/end (via RabbitMQ).
- Timezone support: Store in UTC, display in user’s timezone.
- Interactive calendar: Month/week/day views, click-to-view details.
- Testing mode: Execute automations on test server.
- **Wireframe (Event Calendar):** Full-screen MudBlazor calendar; events color-coded (upcoming, active); click opens details modal; admin sees “Create Event” button.
- **Wireframe (Event Creation):** Modal form with:
- Fields: Title, Description, Start/End Time (with timezone dropdown), RCON Commands (multi-line), Temp Shop Config (JSON/form).
- Toggle: “Test Mode” (routes to test server).
- Buttons: Save, Test, Cancel.

### 5.4. Additional Features (v2.0)
- User Profiles with Discord-linked avatars.
- Notifications (push/email/Discord).
- Plugin System (stub for future games).
- Analytics Dashboard (basic metrics).

---

## 6. System Architecture
```mermaid

graph TD
%% Client Layer
subgraph "Client Applications"
A[MAUI Blazor Desktop]
B[MAUI Blazor Mobile]
C[Web Browser]
end

%% Load Balancer
subgraph "Load Balancer"
D[Nginx / Traefik]
end

%% API Layer
subgraph "API Gateway"
E[ASP.NET Core Gateway]
F[Auth Service]
end

%% Core Services
subgraph "Core Services"
H[Events API]
I[RCON/Shop API]
J[User Management API]
K[Discord Bot Service]
L[Notification Service]
end

%% CMS Layer
subgraph "CMS Layer"
M[Orchard Core]
N[Content API]
O[Media Service]
end

%% Data Layer
subgraph "Data Layer"
P[MongoDB Cluster]
Q[PostgreSQL]
R[Redis Cache]
end

%% External Services
subgraph "External Services"
S[Discord API]
T[Main Game Server]
U[Test Game Server]
V[CDN/Storage]
end

%% Connections
A --> D
B --> D
C --> D

D --> E
E --> F

F --> H
F --> I
F --> J

H --> P
H --> R
I --> P
I --> R
I --> T
I --> U
J --> P

K --> S
K --> T
K --> U

L --> S
L --> V

M --> Q
N --> M
O --> V
```
---

## 7. Data Flow Diagrams
### Event Creation & Automation Workflow (v2.2)
```mermaid
sequenceDiagram
%% Participants
participant Admin
participant UI as Blazor UI
participant API as Events API
participant DB as MongoDB
participant Jobs as Hangfire
participant Rcon as RCON Service
participant Shop as ArkShop Service
participant MQ as RabbitMQ
participant Bot as Discord Bot
participant Discord as Discord Server
participant Test as Test Server

%% Event Creation Flow
Admin->>UI: Create Event\n(Title, UTC Times, Timezone, RCON Seq, Temp Shop, Test Mode?)
UI->>API: POST /api/events
API->>Shop: Fetch Current Shop Config
Shop-->>API: Return Current Config
API->>DB: Save Event\n(UTC Times, OriginalShopConfig, TempShopConfig, IsTestMode, TimeZoneId)
DB-->>API: Confirm Save

%% Test Mode Flow
alt Test Mode
API->>Test: Execute RCON Sequence
Test-->>API: Results
API->>Test: Apply TempShopConfig
Test-->>API: Confirm
API-->>UI: Test Results
else Production Mode
API->>Jobs: Schedule Start Job at StartTime (UTC)
API->>Jobs: Schedule End Job at EndTime (UTC)
API-->>UI: Success
end

%% Start Event Flow
Note over Jobs,Rcon: At StartTime (UTC)
Jobs->>Rcon: Execute RCON Sequence
Rcon-->>Jobs: Results
Jobs->>Shop: Apply TempShopConfig
Shop-->>Jobs: Confirm
Jobs->>MQ: Publish EventStartedMessage
MQ->>Bot: Consume EventStartedMessage
Bot->>Discord: Send Start Embed

%% End Event Flow
Note over Jobs,Shop: At EndTime (UTC)
Jobs->>Shop: Revert to OriginalShopConfig
Shop-->>Jobs: Confirm
Jobs->>MQ: Publish EventEndedMessage
MQ->>Bot: Consume EventEndedMessage
Bot->>Discord: Send End Embed
```


### RCON Command/Macro Execution (v2.0)
```mermaid
sequenceDiagram
participant User as Admin
participant UI as RCON Terminal
participant API as RCON API
participant Cache as Redis
participant DB as MongoDB
participant Server as Game Server
participant Audit as Audit Log
User->>UI: Execute Macro
UI->>API: POST /api/rcon/macro/execute/{id}
API->>DB: Fetch Macro
DB-->>API: Macro Data
API->>Cache: Check Server Status
Cache-->>API: Online
loop Each Command
API->>Server: Send RCON Command
Server-->>API: Response
end
API->>Audit: Log Action
API-->>UI: Stream Results (SignalR)
UI-->>User: Display
```

---

## 8. API Endpoints
### Authentication (v2.0)
- `POST /api/auth/login`
- `POST /api/auth/refresh`
- `POST /api/auth/logout`
- `GET /api/auth/profile`
- `PUT /api/auth/profile`

### Event Management (v2.2)
- `GET /api/events`: Returns events with UTC times and TimeZoneId.
- `GET /api/events/{id}`
- `POST /api/events`: Accepts TimeZoneId, RconCommands, TempShopConfig, IsTestMode.
- `PUT /api/events/{id}`: Updates event, reschedules jobs.
- `DELETE /api/events/{id}`
- `POST /api/events/{id}/rsvp`
- `DELETE /api/events/{id}/rsvp`
- `GET /api/events/{id}/attendees`

### RCON & Macros (v2.0)
- `POST /api/rcon/execute`
- `GET /api/rcon/macros`
- `POST /api/rcon/macros`
- `PUT /api/rcon/macros/{id}`
- `DELETE /api/rcon/macros/{id}`
- `POST /api/rcon/macro/execute/{id}`

### ArkShop (v2.0)
- `GET /api/arkshop`
- `PUT /api/arkshop`

---

## 9. Security & Compliance
- **Auth:** JWT (15-min expiry), Discord OAuth2.0.
- **Authz:** RBAC (Admin, Moderator, User).
- **Encryption:** TLS 1.3+, AES-256 at rest.
- **Validation:** FluentValidation for all inputs.
- **Auditing:** MongoDB logs for admin actions, including automations and test mode.
- **Timezone Security:** Validate TimeZoneId against System.TimeZoneInfo; log conversions.
- **Testing Mode:** Restrict test server access to admins; isolate configs.
- **Compliance:** OWASP Top 10, GDPR-ready.

---

## 10. Deployment & Infrastructure
- **Environments:** Dev (Docker Compose), Staging/Prod (Kubernetes).
- **CI/CD:** GitHub Actions with blue-green deployments.
- **Scaling:** Horizontal pod scaling, MongoDB sharding.
- **Test Server:** Separate ARK server instance (isolated container/namespace).

---

## 11. Development Roadmap
- **Phase 1:** Core setup (auth, CMS, events, basic RCON).
- **Phase 2 :** ArkShop, Discord bot, macros, SignalR, event automations.
- **Phase 2:** Timezone support, event calendar, enhanced event creation UI, testing mode.
- **Phase 3:** Security hardening, CI/CD, monitoring, analytics, plugins.

---

## 12. Testing Strategy
- **Unit Tests:** xUnit, 85% coverage (controllers, services, timezone conversions).
- **Integration Tests:** Testcontainers (MongoDB, test server interactions).
- **E2E Tests:** Playwright (UI flows).
- **Security Scans:** OWASP ZAP.

---

## 13. Monitoring & Observability
- **APM:** Application Insights (track automations, timezone usage, test mode).
- **Metrics:** Prometheus + Grafana.
- **Logging:** Serilog + Seq.
- **Health Checks:** `/health` endpoint.

---

## 14. Assumptions, Risks, Constraints
- **Assumptions:**
- Stable Discord/game APIs.
- Budget for hosting.
- Test server mirrors main server’s API.
- TimeZoneInfo sufficient for conversions.
- **Risks:**
- API rate limits (mitigated by caching).
- Dependency updates (use stable versions).
- Timezone mismatches (use UTC storage).
- Test server drift (sync configs regularly).
- **Constraints:**
- Initial focus on ARK.
- Aggressive timeline.
- Testing mode requires running test server.
- Complex shop config merges may need manual validation.

---

## 15. Conclusion
This consolidated specification provides a robust, production-ready blueprint for a game community platform. It balances immediate needs with long-term extensibility, offering advanced event management, global accessibility, and safe testing capabilities.

---

# Scaffolding the Solution

## Prerequisites
- .NET 9.0 SDK
- Visual Studio 2025 or VS Code
- Docker Desktop
- Node.js
- Discord Developer Portal (OAuth app + bot token)

## Step 1: Create Solution and Projects
In a new directory `GameCommunityPlatform`:

```bash
dotnet new sln -n GameCommunityPlatform
dotnet new maui-blazor -n ClientApp
dotnet sln add ClientApp/ClientApp.csproj
dotnet new webapi -n Api --use-program-main
dotnet sln add Api/Api.csproj
dotnet new classlib -n Shared
dotnet sln add Shared/Shared.csproj
dotnet new console -n DiscordBot
dotnet sln add DiscordBot/DiscordBot.csproj
dotnet new xunit -n Api.Tests
dotnet sln add Api.Tests/Api.Tests.csproj
cd Api.Tests
dotnet add reference ../Api/Api.csproj
dotnet add reference ../Shared/Shared.csproj
cd ..
cd Api
dotnet add reference ../Shared/Shared.csproj
cd ..
cd ClientApp
dotnet add reference ../Shared/Shared.csproj
cd ..
cd DiscordBot
dotnet add reference ../Shared/Shared.csproj
cd ..
```

## Step 2: Add Dependencies
### ClientApp
```bash
cd ClientApp
dotnet add package MudBlazor --version 8.11.0
dotnet add package Fluxor --version 6.7.0
dotnet add package Microsoft.AspNetCore.SignalR.Client --version 9.0.8
dotnet add package Blazored.LocalStorage --version 4.5.0
cd ..
```

### Api
```bash
cd Api
dotnet add package Microsoft.AspNetCore.Authentication.JwtBearer --version 9.0.8
dotnet add package AspNetCore.Identity.MongoDbCore --version 7.0.0
dotnet add package OrchardCore.Application.Cms.Targets --version 2.2.0
dotnet add package MongoDB.Driver --version 3.4.3
dotnet add package Npgsql.EntityFrameworkCore.PostgreSQL --version 9.0.4
dotnet add package StackExchange.Redis --version 2.9.11
dotnet add package MassTransit.RabbitMQ --version 8.5.2
dotnet add package Hangfire.AspNetCore --version 1.8.21
dotnet add package FluentValidation.AspNetCore --version 11.3.1
dotnet add package Serilog.AspNetCore --version 9.0.0
dotnet add package Serilog.Sinks.Seq --version 9.0.0
dotnet add package CoreRCON --version 5.4.2
dotnet add package Swashbuckle.AspNetCore --version 9.0.4
dotnet add package Microsoft.ApplicationInsights.AspNetCore --version 2.22.0
cd ..
```

### DiscordBot
```bash
cd DiscordBot
dotnet add package Discord.Net --version 3.18.0
dotnet add package Microsoft.Extensions.Hosting --version 9.0.0
dotnet add package MassTransit.RabbitMQ --version 8.5.2
cd ..
```

### Api.Tests
```bash
cd Api.Tests
dotnet add package Moq --version 4.20.72
dotnet add package FluentAssertions --version 8.6.0
dotnet add package Testcontainers.MongoDb --version 4.7.0
cd ..
```

## Step 3: Folder Structure
```
GameCommunityPlatform/
├── ClientApp/
│   ├── Components/          # RconTerminal.razor, ArkShopEditor.razor
│   ├── Pages/               # Events.razor (with calendar)
│   ├── Services/            # OfflineCacheService.cs, TimezoneService.cs
│   ├── Stores/              # EventState.cs
│   ├── wwwroot/
│   ├── MauiProgram.cs
│   ├── ClientApp.csproj
├── Api/
│   ├── Controllers/         # EventsController.cs, RconController.cs, ArkShopController.cs
│   ├── Middleware/          # AuditLoggingMiddleware.cs
│   ├── Repositories/        # EventRepository.cs, MacroRepository.cs
│   ├── Services/            # RconService.cs, ArkShopService.cs, AnalyticsService.cs
│   ├── Jobs/                # EventStartJob.cs, EventEndJob.cs
│   ├── Validators/          # EventValidator.cs
│   ├── Hubs/                # NotificationHub.cs
│   ├── Plugins/             # IPlugin.cs
│   ├── Program.cs
│   ├── appsettings.json
│   ├── Api.csproj
├── Shared/
│   ├── Models/              # Event.cs, Macro.cs, ArkShopConfig.cs
│   ├── Messages/            # EventStartedMessage.cs, EventEndedMessage.cs
│   ├── Interfaces/          # IRconService.cs, IPlugin.cs
│   ├── Shared.csproj
├── DiscordBot/
│   ├── Services/            # BotService.cs
│   ├── Program.cs
│   ├── appsettings.json
│   ├── DiscordBot.csproj
├── Api.Tests/
│   ├── Unit/                # EventsControllerTests.cs, TimezoneTests.cs
│   ├── Integration/
│   ├── Api.Tests.csproj
├── docker-compose.yml
├── k8s/deployment.yaml
├── GameCommunityPlatform.sln
└── README.md
```

## Step 4: Key Files
### Api/Program.cs
```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
using Microsoft.IdentityModel.Tokens;
using OrchardCore.Environment.Shell;
using OrchardCore.Modules;
using MassTransit;
using Hangfire;
using Serilog;
using MongoDB.Driver;
using System.Text;
using Microsoft.AspNetCore.Diagnostics.HealthChecks;
using Microsoft.ApplicationInsights.Extensibility;
using System.Threading.RateLimiting;
using Api.Middleware;
using Api.Services;
using Api.Jobs;

var builder = WebApplication.CreateBuilder(args);

// Logging
Log.Logger = new LoggerConfiguration()
.WriteTo.Console()
.WriteTo.Seq("http://localhost:5341")
.CreateLogger();
builder.Host.UseSerilog();

// Services
builder.Services.AddControllers();
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();

// Authentication
builder.Services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
.AddJwtBearer(options =>
{
options.TokenValidationParameters = new TokenValidationParameters
{
ValidateIssuerSigningKey = true,
IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(builder.Configuration["Jwt:Key"])),
ValidateIssuer = false,
ValidateAudience = false
};
});

// Authorization
builder.Services.AddAuthorization(options =>
{
options.AddPolicy("AdminOnly", policy => policy.RequireRole("Admin"));
options.AddPolicy("ModeratorOrAdmin", policy => policy.RequireRole("Moderator", "Admin"));
});

// Orchard Core
builder.Services.AddOrchardCms(builder => builder.AddTenantFeatures("Headless"));

// Databases
var mongoConn = builder.Configuration.GetConnectionString("MongoDb");
builder.Services.AddSingleton<IMongoClient>(new MongoClient(mongoConn));
builder.Services.AddScoped(sp => sp.GetRequiredService<IMongoClient>().GetDatabase("GameCommunity"));
builder.Services.AddDbContext<OrchardDbContext>(options => options.UseNpgsql(builder.Configuration.GetConnectionString("Postgres")));

// Repositories
builder.Services.AddScoped<IEventRepository, EventRepository>();
builder.Services.AddScoped<IMacroRepository, MacroRepository>();

// Services
builder.Services.AddScoped<IRconService, RconService>();
builder.Services.AddScoped<IArkShopService, ArkShopService>();
builder.Services.AddScoped<IAnalyticsService, AnalyticsService>();
builder.Services.AddSingleton<IPluginLoader, PluginLoader>();

// Messaging
builder.Services.AddMassTransit(x =>
{
x.AddConsumer<EventStartedConsumer>();
x.AddConsumer<EventEndedConsumer>();
x.UsingRabbitMq((ctx, cfg) =>
{
cfg.Host(builder.Configuration["RabbitMQ:Host"]);
cfg.ReceiveEndpoint("event_queue", e =>
{
e.ConfigureConsumer<EventStartedConsumer>(ctx);
e.ConfigureConsumer<EventEndedConsumer>(ctx);
});
});
});

// Jobs
builder.Services.AddHangfire(config => config.UseMongoStorage(mongoConn, "Hangfire"));
builder.Services.AddHangfireServer();

// SignalR
builder.Services.AddSignalR();

// Rate Limiting
builder.Services.AddRateLimiter(options =>
{
options.AddFixedWindowLimiter("fixed", opt =>
{
opt.Window = TimeSpan.FromSeconds(10);
opt.PermitLimit = 5;
});
});

// Health Checks
builder.Services.AddHealthChecks()
.AddMongoDb(mongoConn)
.AddNpgSql(builder.Configuration.GetConnectionString("Postgres"));

// Analytics
builder.Services.AddApplicationInsightsTelemetry(options =>
{
options.ConnectionString = builder.Configuration["ApplicationInsights:ConnectionString"];
});

// Validation
builder.Services.AddValidatorsFromAssemblyContaining<Program>();

var app = builder.Build();

app.UseRateLimiter();
if (app.Environment.IsDevelopment())
{
app.UseSwagger();
app.UseSwaggerUI();
app.UseHangfireDashboard();
}
app.UseHttpsRedirection();
app.UseAuthentication();
app.UseAuthorization();
app.UseMiddleware<AuditLoggingMiddleware>();
app.MapControllers();
app.MapHub<NotificationHub>("/hubs/notifications");
app.UseOrchardCore();
app.MapHealthChecks("/health");

// Initialize Plugins
var pluginLoader = app.Services.GetRequiredService<IPluginLoader>();
pluginLoader.LoadPlugins();

app.Run();
```

### Api/appsettings.json
```json
{
"Logging": {
"LogLevel": {
"Default": "Information",
"Microsoft.AspNetCore": "Warning"
}
},
"AllowedHosts": "*",
"ConnectionStrings": {
"MongoDb": "mongodb://localhost:27017",
"Postgres": "Host=localhost;Database=orchard;Username=postgres;Password=password"
},
"Jwt": {
"Key": "your-secret-key-here-min-32-chars",
"Issuer": "https://yourdomain.com",
"Audience": "https://yourdomain.com"
},
"Discord": {
"ClientId": "your-id",
"ClientSecret": "your-secret",
"RedirectUri": "https://localhost:5001/api/auth/callback",
"BotToken": "your-bot-token",
"ChannelId": "your-channel-id"
},
"RabbitMQ": {
"Host": "amqp://guest:guest@localhost:5672/"
},
"Redis": {
"Connection": "localhost:6379"
},
"Ark": {
"Main": {
"RconHost": "your-main-server-ip",
"RconPort": 27020,
"RconPassword": "your-main-rcon-pass",
"ArkShopConfigPath": "/path/to/main/ArkShop/config.json"
},
"Test": {
"RconHost": "your-test-server-ip",
"RconPort": 27021,
"RconPassword": "your-test-rcon-pass",
"ArkShopConfigPath": "/path/to/test/ArkShop/config.json"
}
},
"ApplicationInsights": {
"ConnectionString": "InstrumentationKey=your-key;IngestionEndpoint=https://your-endpoint"
}
}
```

### Shared/Models/Event.cs
```csharp
using MongoDB.Bson;
using MongoDB.Bson.Serialization.Attributes;

namespace Shared.Models;

public class Event
{
[BsonId]
[BsonRepresentation(BsonType.ObjectId)]
public string Id { get; set; }
public string Title { get; set; }
public DateTime StartTime { get; set; }  // UTC
public DateTime EndTime { get; set; }    // UTC
public string TimeZoneId { get; set; }   // e.g., "America/New_York"
public string Description { get; set; }
public List<string> Attendees { get; set; } = new();
public List<string> RconCommands { get; set; } = new();
public ArkShopConfig TempShopConfig { get; set; }
public ArkShopConfig OriginalShopConfig { get; set; }
public bool IsTestMode { get; set; }
}
```

### Shared/Models/ArkShopConfig.cs
```csharp
namespace Shared.Models;

public class ArkShopConfig
{
public Dictionary<string, ItemCategory> Items { get; set; } = new();
public int DefaultPoints { get; set; }
}

public class ItemCategory
{
public string Name { get; set; }
public decimal Price { get; set; }
}
```

### Shared/Models/Macro.cs
```csharp
using MongoDB.Bson;
using MongoDB.Bson.Serialization.Attributes;

namespace Shared.Models;

public class Macro
{
[BsonId]
[BsonRepresentation(BsonType.ObjectId)]
public string Id { get; set; }
public string Name { get; set; }
public List<string> Commands { get; set; } = new();
public string Description { get; set; }
public string UserId { get; set; }
}
```

### Shared/Messages/EventStartedMessage.cs
```csharp
using Shared.Models;

namespace Shared.Messages;

public class EventStartedMessage
{
public Event Event { get; set; }
}
```

### Shared/Messages/EventEndedMessage.cs
```csharp
using Shared.Models;

namespace Shared.Messages;

public class EventEndedMessage
{
public Event Event { get; set; }
}
```

### Shared/Interfaces/IPlugin.cs
```csharp
namespace Shared.Interfaces;

public interface IPlugin
{
string Name { get; }
void Initialize(IServiceProvider services);
}
```

### Shared/Interfaces/IRconService.cs
```csharp
namespace Shared.Interfaces;

public interface IRconService
{
Task<string> ExecuteCommandAsync(string command, bool isTestMode = false);
Task<string> ExecuteMacroAsync(string macroId, string userId);
}
```

### Shared/Interfaces/IArkShopService.cs
```csharp
using Shared.Models;

namespace Shared.Interfaces;

public interface IArkShopService
{
Task<ArkShopConfig> GetConfigAsync(bool isTestMode = false);
Task ApplyTempConfigAsync(ArkShopConfig tempConfig, bool isTestMode = false);
Task RevertConfigAsync(ArkShopConfig originalConfig, bool isTestMode = false);
}
```

### Api/Repositories/EventRepository.cs
```csharp
using MongoDB.Driver;
using Shared.Models;

public interface IEventRepository
{
Task<List<Event>> GetAllAsync();
Task<Event> GetByIdAsync(string id);
Task CreateAsync(Event @event);
Task UpdateAsync(Event @event);
Task DeleteAsync(string id);
}

public class EventRepository : IEventRepository
{
private readonly IMongoCollection<Event> _events;

public EventRepository(IMongoDatabase database)
{
_events = database.GetCollection<Event>("events");
}

public async Task<List<Event>> GetAllAsync()
{
return await _events.Find(_ => true).ToListAsync();
}

public async Task<Event> GetByIdAsync(string id)
{
return await _events.Find(e => e.Id == id).FirstOrDefaultAsync();
}

public async Task CreateAsync(Event @event)
{
await _events.InsertOneAsync(@event);
}

public async Task UpdateAsync(Event @event)
{
await _events.ReplaceOneAsync(e => e.Id == @event.Id, @event);
}

public async Task DeleteAsync(string id)
{
await _events.DeleteOneAsync(e => e.Id == id);
}
}
```

### Api/Repositories/MacroRepository.cs
```csharp
using MongoDB.Driver;
using Shared.Models;

public interface IMacroRepository
{
Task<List<Macro>> GetAllAsync(string userId);
Task<Macro> GetByIdAsync(string id);
Task CreateAsync(Macro macro);
Task UpdateAsync(Macro macro);
Task DeleteAsync(string id);
}

public class MacroRepository : IMacroRepository
{
private readonly IMongoCollection<Macro> _macros;

public MacroRepository(IMongoDatabase database)
{
_macros = database.GetCollection<Macro>("macros");
}

public async Task<List<Macro>> GetAllAsync(string userId)
{
return await _macros.Find(m => m.UserId == userId).ToListAsync();
}

public async Task<Macro> GetByIdAsync(string id)
{
return await _macros.Find(m => m.Id == id).FirstOrDefaultAsync();
}

public async Task CreateAsync(Macro macro)
{
await _macros.InsertOneAsync(macro);
}

public async Task UpdateAsync(Macro macro)
{
await _macros.ReplaceOneAsync(m => m.Id == macro.Id, macro);
}

public async Task DeleteAsync(string id)
{
await _macros.DeleteOneAsync(m => m.Id == id);
}
}
```

### Api/Services/RconService.cs
```csharp
using CoreRCON;
using Shared.Models;
using Shared.Interfaces;

public class RconService : IRconService
{
private readonly IConfiguration _config;
private readonly IMacroRepository _macroRepo;
private RCON _mainRcon;
private RCON _testRcon;

public RconService(IConfiguration config, IMacroRepository macroRepo)
{
_config = config;
_macroRepo = macroRepo;

// Main server
var mainHost = _config["Ark:Main:RconHost"];
var mainPort = ushort.Parse(_config["Ark:Main:RconPort"]);
var mainPass = _config["Ark:Main:RconPassword"];
_mainRcon = new RCON(System.Net.IPAddress.Parse(mainHost), mainPort, mainPass);

// Test server
var testHost = _config["Ark:Test:RconHost"];
var testPort = ushort.Parse(_config["Ark:Test:RconPort"]);
var testPass = _config["Ark:Test:RconPassword"];
_testRcon = new RCON(System.Net.IPAddress.Parse(testHost), testPort, testPass);
}

public async Task<string> ExecuteCommandAsync(string command, bool isTestMode = false)
{
try
{
var rcon = isTestMode ? _testRcon : _mainRcon;
await rcon.ConnectAsync();
return await rcon.SendCommandAsync(command);
}
catch (Exception ex)
{
throw new InvalidOperationException("RCON command failed", ex);
}
}

public async Task<string> ExecuteMacroAsync(string macroId, string userId)
{
var macro = await _macroRepo.GetByIdAsync(macroId);
if (macro == null || macro.UserId != userId) throw new UnauthorizedAccessException("Invalid macro");

var results = new List<string>();
await _mainRcon.ConnectAsync();
foreach (var cmd in macro.Commands)
{
var result = await _mainRcon.SendCommandAsync(cmd);
results.Add(result);
}
return string.Join("\n", results);
}
}
```

### Api/Services/ArkShopService.cs
```csharp
using System.Text.Json;
using Shared.Models;
using Shared.Interfaces;

public class ArkShopService : IArkShopService
{
private readonly IConfiguration _config;
private readonly IRconService _rcon;

public ArkShopService(IConfiguration config, IRconService rcon)
{
_config = config;
_rcon = rcon;
}

public async Task<ArkShopConfig> GetConfigAsync(bool isTestMode = false)
{
try
{
var path = isTestMode ? _config["Ark:Test:ArkShopConfigPath"] : _config["Ark:Main:ArkShopConfigPath"];
var json = await File.ReadAllTextAsync(path);
return JsonSerializer.Deserialize<ArkShopConfig>(json);
}
catch (Exception ex)
{
throw new InvalidOperationException("Failed to load ArkShop config", ex);
}
}

public async Task ApplyTempConfigAsync(ArkShopConfig tempConfig, bool isTestMode = false)
{
try
{
var path = isTestMode ? _config["Ark:Test:ArkShopConfigPath"] : _config["Ark:Main:ArkShopConfigPath"];
var json = JsonSerializer.Serialize(tempConfig, new JsonSerializerOptions { WriteIndented = true });
await File.WriteAllTextAsync(path, json);
await _rcon.ExecuteCommandAsync("shop.reload", isTestMode);
}
catch (Exception ex)
{
throw new InvalidOperationException("Failed to apply ArkShop config", ex);
}
}

public async Task RevertConfigAsync(ArkShopConfig originalConfig, bool isTestMode = false)
{
await ApplyTempConfigAsync(originalConfig, isTestMode);
}
}
```

### Api/Services/AnalyticsService.cs
```csharp
using Microsoft.ApplicationInsights;

public interface IAnalyticsService
{
void TrackEvent(string eventName, Dictionary<string, string> properties = null);
}

public class AnalyticsService : IAnalyticsService
{
private readonly TelemetryClient _telemetry;

public AnalyticsService(TelemetryClient telemetry) => _telemetry = telemetry;

public void TrackEvent(string eventName, Dictionary<string, string> properties = null)
{
_telemetry.TrackEvent(eventName, properties);
}
}
```

### Api/Services/PluginLoader.cs
```csharp
using Shared.Interfaces;

public interface IPluginLoader
{
void LoadPlugins();
}

public class PluginLoader : IPluginLoader
{
private readonly IServiceProvider _services;

public PluginLoader(IServiceProvider services) => _services = services;

public void LoadPlugins()
{
var plugins = AppDomain.CurrentDomain.GetAssemblies()
.SelectMany(a => a.GetTypes())
.Where(t => typeof(IPlugin).IsAssignableFrom(t) && !t.IsInterface)
.Select(t => Activator.CreateInstance(t) as IPlugin);

foreach (var plugin in plugins)
{
plugin.Initialize(_services);
Log.Information("Loaded plugin: {Name}", plugin.Name);
}
}
}
```

### Api/Controllers/EventsController.cs
```csharp
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Authorization;
using Shared.Models;
using Shared.Interfaces;
using Hangfire;
using Api.Jobs;
using System;
using System.Linq;

[ApiController]
[Route("api/events")]
[Authorize]
public class EventsController : ControllerBase
{
private readonly IEventRepository _repo;
private readonly IAnalyticsService _analytics;
private readonly IArkShopService _shopService;
private readonly IRconService _rconService;
private readonly IBackgroundJobClient _hangfire;

public EventsController(IEventRepository repo, IAnalyticsService analytics, IArkShopService shopService, IRconService rconService, IBackgroundJobClient hangfire)
{
_repo = repo;
_analytics = analytics;
_shopService = shopService;
_rconService = rconService;
_hangfire = hangfire;
}

[HttpGet]
public async Task<IActionResult> GetEvents()
{
var events = await _repo.GetAllAsync();
_analytics.TrackEvent("EventsViewed");
return Ok(events);
}

[HttpGet("{id}")]
public async Task<IActionResult> GetEvent(string id)
{
var @event = await _repo.GetByIdAsync(id);
if (@event == null) return NotFound();
return Ok(@event);
}

[HttpPost]
[Authorize(Policy = "AdminOnly")]
public async Task<IActionResult> CreateEvent([FromBody] Event newEvent)
{
// Validate timezone
if (!string.IsNullOrEmpty(newEvent.TimeZoneId) && !TimeZoneInfo.GetSystemTimeZones().Any(tz => tz.Id == newEvent.TimeZoneId))
{
return BadRequest("Invalid TimeZoneId");
}

// Convert times to UTC
if (!string.IsNullOrEmpty(newEvent.TimeZoneId))
{
var tz = TimeZoneInfo.FindSystemTimeZoneById(newEvent.TimeZoneId);
newEvent.StartTime = TimeZoneInfo.ConvertTimeToUtc(newEvent.StartTime, tz);
newEvent.EndTime = TimeZoneInfo.ConvertTimeToUtc(newEvent.EndTime, tz);
}

// Backup shop config
if (newEvent.TempShopConfig != null)
{
newEvent.OriginalShopConfig = await _shopService.GetConfigAsync(newEvent.IsTestMode);
}

await _repo.CreateAsync(newEvent);
_analytics.TrackEvent("EventCreated", new() { { "Title", newEvent.Title }, { "TestMode", newEvent.IsTestMode.ToString() } });

// Test mode: Execute immediately
if (newEvent.IsTestMode)
{
var results = new List<string>();
foreach (var cmd in newEvent.RconCommands)
{
results.Add(await _rconService.ExecuteCommandAsync(cmd, true));
}
if (newEvent.TempShopConfig != null)
{
await _shopService.ApplyTempConfigAsync(newEvent.TempShopConfig, true);
}
return Ok(new { Event = newEvent, RconResults = results });
}

// Schedule jobs
_hangfire.Schedule(() => new EventStartJob(_repo, _shopService, _rconService, _hangfire).ExecuteAsync(newEvent.Id), newEvent.StartTime);
_hangfire.Schedule(() => new EventEndJob(_repo, _shopService, _hangfire).ExecuteAsync(newEvent.Id), newEvent.EndTime);

return CreatedAtAction(nameof(GetEvent), new { id = newEvent.Id }, newEvent);
}

[HttpPut("{id}")]
[Authorize(Policy = "AdminOnly")]
public async Task<IActionResult> UpdateEvent(string id, [FromBody] Event updatedEvent)
{
var existing = await _repo.GetByIdAsync(id);
if (existing == null) return NotFound();

// Validate timezone
if (!string.IsNullOrEmpty(updatedEvent.TimeZoneId) && !TimeZoneInfo.GetSystemTimeZones().Any(tz => tz.Id == updatedEvent.TimeZoneId))
{
return BadRequest("Invalid TimeZoneId");
}

// Convert times to UTC
if (!string.IsNullOrEmpty(updatedEvent.TimeZoneId))
{
var tz = TimeZoneInfo.FindSystemTimeZoneById(updatedEvent.TimeZoneId);
updatedEvent.StartTime = TimeZoneInfo.ConvertTimeToUtc(updatedEvent.StartTime, tz);
updatedEvent.EndTime = TimeZoneInfo.ConvertTimeToUtc(updatedEvent.EndTime, tz);
}

updatedEvent.Id = id;
if (updatedEvent.TempShopConfig != null && updatedEvent.OriginalShopConfig == null)
{
updatedEvent.OriginalShopConfig = await _shopService.GetConfigAsync(updatedEvent.IsTestMode);
}

await _repo.UpdateAsync(updatedEvent);
_analytics.TrackEvent("EventUpdated", new() { { "Title", updatedEvent.Title } });

if (!updatedEvent.IsTestMode)
{
_hangfire.Schedule(() => new EventStartJob(_repo, _shopService, _rconService, _hangfire).ExecuteAsync(id), updatedEvent.StartTime);
_hangfire.Schedule(() => new EventEndJob(_repo, _shopService, _hangfire).ExecuteAsync(id), updatedEvent.EndTime);
}

return NoContent();
}

[HttpPost("{id}/rsvp")]
public async Task<IActionResult> Rsvp(string id)
{
var @event = await _repo.GetByIdAsync(id);
if (@event == null) return NotFound();

var userId = User.FindFirst(System.Security.Claims.ClaimTypes.NameIdentifier)?.Value;
if (!@event.Attendees.Contains(userId))
{
@event.Attendees.Add(userId);
await _repo.UpdateAsync(@event);
}
return NoContent();
}
}
```

### Api/Jobs/EventStartJob.cs
```csharp
using Shared.Models;
using Shared.Messages;
using Shared.Interfaces;
using MassTransit;
using Hangfire;

public class EventStartJob
{
private readonly IEventRepository _repo;
private readonly IArkShopService _shopService;
private readonly IRconService _rconService;
private readonly IBackgroundJobClient _hangfire;

public EventStartJob(IEventRepository repo, IArkShopService shopService, IRconService rconService, IBackgroundJobClient hangfire)
{
_repo = repo;
_shopService = shopService;
_rconService = rconService;
_hangfire = hangfire;
}

public async Task ExecuteAsync(string eventId)
{
var @event = await _repo.GetByIdAsync(eventId);
if (@event == null || @event.IsTestMode) return;

// Execute RCON sequence
foreach (var cmd in @event.RconCommands)
{
await _rconService.ExecuteCommandAsync(cmd);
}

// Apply temp shop
if (@event.TempShopConfig != null)
{
await _shopService.ApplyTempConfigAsync(@event.TempShopConfig);
}

// Notify Discord
var publishEndpoint = _hangfire.GetService<IPublishEndpoint>();
await publishEndpoint.Publish(new EventStartedMessage { Event = @event });
}
}
```

### Api/Jobs/EventEndJob.cs
```csharp
using Shared.Models;
using Shared.Messages;
using Shared.Interfaces;
using MassTransit;
using Hangfire;

public class EventEndJob
{
private readonly IEventRepository _repo;
private readonly IArkShopService _shopService;
private readonly IBackgroundJobClient _hangfire;

public EventEndJob(IEventRepository repo, IArkShopService shopService, IBackgroundJobClient hangfire)
{
_repo = repo;
_shopService = shopService;
_hangfire = hangfire;
}

public async Task ExecuteAsync(string eventId)
{
var @event = await _repo.GetByIdAsync(eventId);
if (@event == null || @event.IsTestMode) return;

// Revert shop
if (@event.OriginalShopConfig != null)
{
await _shopService.RevertConfigAsync(@event.OriginalShopConfig);
}

// Notify Discord
var publishEndpoint = _hangfire.GetService<IPublishEndpoint>();
await publishEndpoint.Publish(new EventEndedMessage { Event = @event });
}
}
```

### Api/Controllers/RconController.cs
```csharp
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Authorization;
using Shared.Models;
using Shared.Interfaces;
using System.Security.Claims;

[ApiController]
[Route("api/rcon")]
[Authorize(Policy = "AdminOnly")]
public class RconController : ControllerBase
{
private readonly IRconService _rconService;
private readonly IMacroRepository _macroRepo;
private readonly string _userId;

public RconController(IRconService rconService, IMacroRepository macroRepo, IHttpContextAccessor httpContext)
{
_rconService = rconService;
_macroRepo = macroRepo;
_userId = httpContext.HttpContext.User.FindFirst(ClaimTypes.NameIdentifier)?.Value;
}

[HttpPost("execute")]
public async Task<IActionResult> Execute([FromBody] string command)
{
var result = await _rconService.ExecuteCommandAsync(command);
return Ok(result);
}

[HttpPost("macro/execute/{id}")]
public async Task<IActionResult> ExecuteMacro(string id)
{
var result = await _rconService.ExecuteMacroAsync(id, _userId);
return Ok(result);
}

[HttpGet("macros")]
public async Task<IActionResult> GetMacros()
{
var macros = await _macroRepo.GetAllAsync(_userId);
return Ok(macros);
}

[HttpPost("macros")]
public async Task<IActionResult> CreateMacro([FromBody] Macro macro)
{
macro.UserId = _userId;
await _macroRepo.CreateAsync(macro);
return CreatedAtAction(nameof(GetMacros), new { id = macro.Id }, macro);
}

[HttpPut("macros/{id}")]
public async Task<IActionResult> UpdateMacro(string id, [FromBody] Macro macro)
{
macro.Id = id;
macro.UserId = _userId;
await _macroRepo.UpdateAsync(macro);
return NoContent();
}

[HttpDelete("macros/{id}")]
public async Task<IActionResult> DeleteMacro(string id)
{
await _macroRepo.DeleteAsync(id);
return NoContent();
}
}
```

### Api/Controllers/ArkShopController.cs
```csharp
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Authorization;
using Shared.Models;
using Shared.Interfaces;

[ApiController]
[Route("api/arkshop")]
[Authorize(Policy = "AdminOnly")]
public class ArkShopController : ControllerBase
{
private readonly IArkShopService _service;

public ArkShopController(IArkShopService service) => _service = service;

[HttpGet]
public async Task<IActionResult> GetConfig() => Ok(await _service.GetConfigAsync());

[HttpPut]
public async Task<IActionResult> UpdateConfig([FromBody] ArkShopConfig config)
{
await _service.ApplyTempConfigAsync(config);
return NoContent();
}
}
```

### Api/Middleware/AuditLoggingMiddleware.cs
```csharp
using Serilog;
using System.Security.Claims;

public class AuditLoggingMiddleware
{
private readonly RequestDelegate _next;

public AuditLoggingMiddleware(RequestDelegate next) => _next = next;

public async Task InvokeAsync(HttpContext context)
{
if (context.User.Identity?.IsAuthenticated == true && context.Request.Path.StartsWithSegments("/api") && context.Request.Method != "GET")
{
Log.Information("Audit: User {User} performed {Method} on {Path}",
context.User.FindFirst(ClaimTypes.NameIdentifier)?.Value, context.Request.Method, context.Request.Path);
}
await _next(context);
}
}
```

### Api/Hubs/NotificationHub.cs
```csharp
using Microsoft.AspNetCore.SignalR;

public class NotificationHub : Hub
{
public async Task SendRconOutput(string message)
{
await Clients.All.SendAsync("ReceiveRconOutput", message);
}
}
```

### ClientApp/Services/OfflineCacheService.cs
```csharp
using Blazored.LocalStorage;
using Shared.Models;

public class OfflineCacheService
{
private readonly ILocalStorageService _storage;

public OfflineCacheService(ILocalStorageService storage) => _storage = storage;

public async Task<List<Event>> GetCachedEventsAsync()
{
return await _storage.GetItemAsync<List<Event>>("events") ?? new();
}

public async Task CacheEventsAsync(List<Event> events)
{
await _storage.SetItemAsync("events", events);
}
}
```

### ClientApp/Services/TimezoneService.cs
```csharp
using System;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.JSInterop;

public class TimezoneService
{
private readonly IJSRuntime _jsRuntime;

public TimezoneService(IJSRuntime jsRuntime)
{
_jsRuntime = jsRuntime;
}

public async Task<string> GetBrowserTimeZoneAsync()
{
try
{
var tz = await _jsRuntime.InvokeAsync<string>("eval", "Intl.DateTimeFormat().resolvedOptions().timeZone");
return TimeZoneInfo.GetSystemTimeZones().Any(t => t.Id == tz) ? tz : "UTC";
}
catch
{
return "UTC";
}
}

public DateTime ConvertToLocal(DateTime utcDateTime, string timeZoneId)
{
var tz = TimeZoneInfo.FindSystemTimeZoneById(timeZoneId);
return TimeZoneInfo.ConvertTimeFromUtc(utcDateTime, tz);
}
}
```

### ClientApp/Pages/Events.razor
```razor
@page "/events"
@using MudBlazor
@using System.Net.Http.Json
@using Shared.Models
@inject HttpClient Http
@inject OfflineCacheService CacheService
@inject TimezoneService TimezoneService
@inject NavigationManager Nav

<MudContainer MaxWidth="MaxWidth.Large">
<MudText Typo="Typo.h3">Events</MudText>
<MudButton Variant="Variant.Filled" Color="Color.Primary" OnClick="OpenCreate">Create Event</MudButton>
<MudButton OnClick="LoadEvents">Refresh</MudButton>

<!-- Calendar -->
<MudCalendar T="Event" Items="@events" Date="@selectedDate" OnDateChanged="OnDateChanged" OnSelectItem="ViewEvent">
<ItemTemplate>
<MudText>@context.Title (@(TimezoneService.ConvertToLocal(context.StartTime, userTimeZone).ToShortTimeString()))</MudText>
</ItemTemplate>
</MudCalendar>

<!-- Event Details -->
@if (selectedEvent != null)
{
<MudCard>
<MudCardHeader>@selectedEvent.Title</MudCardHeader>
<MudCardContent>
<MudText>Start: @TimezoneService.ConvertToLocal(selectedEvent.StartTime, userTimeZone)</MudText>
<MudText>End: @TimezoneService.ConvertToLocal(selectedEvent.EndTime, userTimeZone)</MudText>
<MudText>@selectedEvent.Description</MudText>
<MudText>Attendees: @(selectedEvent.Attendees.Count)</MudText>
</MudCardContent>
<MudCardActions>
<MudButton OnClick="() => Rsvp(selectedEvent.Id)">RSVP</MudButton>
</MudCardActions>
</MudCard>
}
</MudContainer>

<MudDialog @bind-IsVisible="showCreate">
<TitleContent>Create Event</TitleContent>
<DialogContent>
<MudTextField @bind-Value="newEvent.Title" Label="Title" />
<MudDatePicker @bind-Date="newEvent.StartTime" Label="Start Time" />
<MudDatePicker @bind-Date="newEvent.EndTime" Label="End Time" />
<MudSelect T="string" @bind-Value="newEvent.TimeZoneId" Label="Timezone">
@foreach (var tz in TimeZoneInfo.GetSystemTimeZones())
{
<MudSelectItem Value="@tz.Id">@tz.DisplayName</MudSelectItem>
}
</MudSelect>
<MudTextField @bind-Value="newEvent.Description" Label="Description" Lines="3" />
<MudTextField @bind-Value="rconInput" Label="RCON Commands (one per line)" Lines="5" />
<MudTextField @bind-Value="shopJson" Label="Temp Shop Config (JSON)" Lines="5" />
<MudCheckBox @bind-Checked="newEvent.IsTestMode" Label="Test Mode (use test server)" />
</DialogContent>
<DialogActions>
<MudButton OnClick="CancelCreate">Cancel</MudButton>
<MudButton Color="Color.Primary" OnClick="SaveEvent">Save</MudButton>
@if (newEvent.IsTestMode)
{
<MudButton Color="Color.Secondary" OnClick="TestEvent">Test</MudButton>
}
</DialogActions>
</MudDialog>

@code {
private List<Event> events = new();
private DateTime? selectedDate = DateTime.UtcNow;
private Event selectedEvent;
private Event newEvent = new();
private string rconInput;
private string shopJson;
private bool showCreate;
private string userTimeZone;

protected override async Task OnInitializedAsync()
{
userTimeZone = await TimezoneService.GetBrowserTimeZoneAsync();
events = await CacheService.GetCachedEventsAsync();
await LoadEvents();
}

private async Task LoadEvents()
{
try
{
events = await Http.GetFromJsonAsync<List<Event>>("api/events");
await CacheService.CacheEventsAsync(events);
}
catch
{
// Offline: Use cache
}
StateHasChanged();
}

private async Task Rsvp(string id)
{
await Http.PostAsync($"api/events/{id}/rsvp", null);
await LoadEvents();
}

private void OpenCreate()
{
newEvent = new Event { RconCommands = new(), TempShopConfig = new(), TimeZoneId = userTimeZone };
rconInput = string.Empty;
shopJson = string.Empty;
showCreate = true;
}

private async Task SaveEvent()
{
newEvent.RconCommands = rconInput.Split('\n', StringSplitOptions.RemoveEmptyEntries).ToList();
if (!string.IsNullOrWhiteSpace(shopJson))
{
newEvent.TempShopConfig = System.Text.Json.JsonSerializer.Deserialize<ArkShopConfig>(shopJson);
}
await Http.PostAsJsonAsync("api/events", newEvent);
await LoadEvents();
showCreate = false;
}

private async Task TestEvent()
{
newEvent.RconCommands = rconInput.Split('\n', StringSplitOptions.RemoveEmptyEntries).ToList();
if (!string.IsNullOrWhiteSpace(shopJson))
{
newEvent.TempShopConfig = System.Text.Json.JsonSerializer.Deserialize<ArkShopConfig>(shopJson);
}
newEvent.IsTestMode = true;
var response = await Http.PostAsJsonAsync("api/events", newEvent);
if (response.IsSuccessStatusCode)
{
var result = await response.Content.ReadFromJsonAsync<dynamic>();
// Display results (e.g., in dialog)
showCreate = false;
}
}

private void CancelCreate() => showCreate = false;

private void OnDateChanged(DateTime? date)
{
selectedDate = date;
selectedEvent = null;
StateHasChanged();
}

private void ViewEvent(Event @event)
{
selectedEvent = @event;
StateHasChanged();
}
}
```

### ClientApp/Components/RconTerminal.razor
```razor
@page "/rcon"
@using MudBlazor
@using Microsoft.AspNetCore.SignalR.Client
@using Shared.Models
@inject HttpClient Http
@inject NavigationManager Nav

<MudContainer MaxWidth="MaxWidth.Large">
<MudGrid Spacing="2">
<MudItem xs="3">
<MudCard>
<MudCardHeader>Servers & Macros</MudCardHeader>
<MudCardContent>
<MudSelect T="string" Label="Select Server" Variant="Variant.Outlined">
<MudSelectItem Value="@("Main")">Main Server</MudSelectItem>
<MudSelectItem Value="@("Test")">Test Server</MudSelectItem>
</MudSelect>
<MudDivider Class="my-2" />
<MudList Clickable="true" @bind-SelectedItem="selectedMacro">
@foreach (var macro in macros)
{
<MudListItem Text="@macro.Name" OnClick="() => ExecuteMacro(macro.Id)" />
}
</MudList>
<MudButton Variant="Variant.Filled" Color="Color.Primary" OnClick="OpenCreateMacroDialog">Add Macro</MudButton>
</MudCardContent>
</MudCard>
</MudItem>
<MudItem xs="6">
<MudCard>
<MudCardHeader>RCON Terminal</MudCardHeader>
<MudCardContent>
<MudTextField @bind-Value="command" Label="Command" Variant="Variant.Outlined" Adornment="Adornment.End" AdornmentIcon="@Icons.Material.Filled.Send" OnAdornmentClick="Execute" />
<MudTextField Lines="10" ReadOnly="true" Value="@output" Variant="Variant.Outlined" />
</MudCardContent>
</MudCard>
</MudItem>
<MudItem xs="3">
<MudCard>
<MudCardHeader>Logs & Help</MudCardHeader>
<MudCardContent>
<MudText>Command History:</MudText>
<MudList>
@foreach (var log in logs)
{
<MudListItem>@log</MudListItem>
}
</MudList>
<MudDivider />
<MudText>Help: Use commands like 'listplayers'</MudText>
</MudCardContent>
</MudCard>
</MudItem>
</MudGrid>
</MudContainer>

<MudDialog @bind-IsVisible="showCreateDialog">
<TitleContent>Create Macro</TitleContent>
<DialogContent>
<MudTextField @bind-Value="newMacro.Name" Label="Name" />
<MudTextField @bind-Value="newMacro.Description" Label="Description" />
<MudTextField @bind-Value="newCommand" Label="Add Command" Adornment="Adornment.End" AdornmentIcon="@Icons.Material.Filled.Add" OnAdornmentClick="AddCommand" />
<MudList>
@foreach (var cmd in newMacro.Commands)
{
<MudListItem>@cmd</MudListItem>
}
</MudList>
</DialogContent>
<DialogActions>
<MudButton OnClick="() => showCreateDialog = false">Cancel</MudButton>
<MudButton Color="Color.Primary" OnClick="SaveMacro">Save</MudButton>
</DialogActions>
</MudDialog>

@code {
private string command;
private string output;
private List<Macro> macros = new();
private List<string> logs = new();
private Macro selectedMacro;
private HubConnection hub;
private bool showCreateDialog;
private Macro newMacro = new();
private string newCommand;

protected override async Task OnInitializedAsync()
{
hub = new HubConnectionBuilder().WithUrl(Nav.ToAbsoluteUri("/hubs/notifications")).Build();
await hub.StartAsync();
hub.On<string>("ReceiveRconOutput", (msg) =>
{
output += "\n" + msg;
logs.Add(msg);
StateHasChanged();
});

macros = await Http.GetFromJsonAsync<List<Macro>>("api/rcon/macros");
}

private async Task Execute()
{
var response = await Http.PostAsJsonAsync("api/rcon/execute", command);
if (response.IsSuccessStatusCode) output = await response.Content.ReadAsStringAsync();
command = string.Empty;
}

private async Task ExecuteMacro(string id)
{
var response = await Http.PostAsync($"api/rcon/macro/execute/{id}", null);
if (response.IsSuccessStatusCode) output = await response.Content.ReadAsStringAsync();
}

private void OpenCreateMacroDialog()
{
newMacro = new Macro { Commands = new() };
newCommand = string.Empty;
showCreateDialog = true;
}

private void AddCommand()
{
if (!string.IsNullOrWhiteSpace(newCommand))
{
newMacro.Commands.Add(newCommand);
newCommand = string.Empty;
StateHasChanged();
}
}

private async Task SaveMacro()
{
var response = await Http.PostAsJsonAsync("api/rcon/macros", newMacro);
if (response.IsSuccessStatusCode)
{
macros = await Http.GetFromJsonAsync<List<Macro>>("api/rcon/macros");
showCreateDialog = false;
}
}
}
```

### ClientApp/Components/ArkShopConfiguration.razor
```razor
@page "/arkshop"
@using MudBlazor
@using Shared.Models
@inject HttpClient Http

<MudContainer MaxWidth="MaxWidth.Large">
<MudTabs>
<MudTabPanel Text="Items">
<MudDataGrid Items="@config.Items.Values" T="ItemCategory" EditMode="DataGridEditMode.Form" EditTrigger="DataGridEditTrigger.OnRowClick">
<Columns>
<PropertyColumn Property="x => x.Name" Title="Name" />
<PropertyColumn Property="x => x.Price" Title="Price" />
<TemplateColumn>
<EditTemplate>
<MudTextField @bind-Value="context.Item.Name" Label="Name" />
<MudNumericField @bind-Value="context.Item.Price" Label="Price" />
</EditTemplate>
</TemplateColumn>
</Columns>
</MudDataGrid>
<MudButton OnClick="SaveConfig">Save Changes</MudButton>
</MudTabPanel>
<MudTabPanel Text="Config">
<MudNumericField @bind-Value="config.DefaultPoints" Label="Default Points" />
</MudTabPanel>
<MudTabPanel Text="Preview">
<MudText>Shop Preview (Mock UI)</MudText>
</MudTabPanel>
</MudTabs>
</MudContainer>

@code {
private ArkShopConfig config = new();

protected override async Task OnInitializedAsync()
{
config = await Http.GetFromJsonAsync<ArkShopConfig>("api/arkshop");
}

private async Task SaveConfig()
{
await Http.PutAsJsonAsync("api/arkshop", config);
}
}
```

### DiscordBot/Program.cs
```csharp
using Discord;
using Discord.WebSocket;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Configuration;

var builder = Host.CreateApplicationBuilder(args);

builder.Configuration.AddJsonFile("appsettings.json");

builder.Services.AddSingleton<DiscordSocketClient>();
builder.Services.AddHostedService<BotService>();

var host = builder.Build();
await host.RunAsync();
```

### DiscordBot/Services/BotService.cs
```csharp
using Discord.WebSocket;
using Shared.Messages;
using MassTransit;
using Discord;

public class BotService : BackgroundService
{
private readonly DiscordSocketClient _client;
private readonly IConfiguration _config;
private readonly IBus _bus;

public BotService(DiscordSocketClient client, IConfiguration config, IBus bus)
{
_client = client;
_config = config;
_bus = bus;
}

protected override async Task ExecuteAsync(CancellationToken stoppingToken)
{
_client.Log += LogAsync;
await _client.LoginAsync(TokenType.Bot, _config["Discord:BotToken"]);
await _client.StartAsync();

// Consume from RabbitMQ
await _bus.ConnectReceiveEndpoint("event_queue", e =>
{
e.Consumer<EventStartedConsumer>();
e.Consumer<EventEndedConsumer>();
});

_client.MessageReceived += async (msg) =>
{
if (msg.Content == "!events")
{
await msg.Channel.SendMessageAsync("Upcoming events...");
}
};

await Task.Delay(-1, stoppingToken);
}

private Task LogAsync(LogMessage msg)
{
Console.WriteLine(msg);
return Task.CompletedTask;
}
}

public class EventStartedConsumer : IConsumer<EventStartedMessage>
{
private readonly DiscordSocketClient _client;
private readonly IConfiguration _config;

public EventStartedConsumer(DiscordSocketClient client, IConfiguration config)
{
_client = client;
_config = config;
}

public async Task Consume(ConsumeContext<EventStartedMessage> context)
{
var channel = _client.GetChannel(ulong.Parse(_config["Discord:ChannelId"])) as ISocketMessageChannel;
await channel.SendMessageAsync($"Event Started: {context.Message.Event.Title}");
}
}

public class EventEndedConsumer : IConsumer<EventEndedMessage>
{
private readonly DiscordSocketClient _client;
private readonly IConfiguration _config;

public EventEndedConsumer(DiscordSocketClient client, IConfiguration config)
{
_client = client;
_config = config;
}

public async Task Consume(ConsumeContext<EventEndedMessage> context)
{
var channel = _client.GetChannel(ulong.Parse(_config["Discord:ChannelId"])) as ISocketMessageChannel;
await channel.SendMessageAsync($"Event Ended: {context.Message.Event.Title}");
}
}
```

### DiscordBot/appsettings.json
```json
{
"Discord": {
"BotToken": "your-bot-token",
"ChannelId": "your-channel-id"
},
"RabbitMQ": {
"Host": "amqp://guest:guest@localhost:5672/"
}
}
```

### Api.Tests/Unit/EventsControllerTests.cs
```csharp
using Moq;
using FluentAssertions;
using Api.Controllers;
using Shared.Models;
using Shared.Interfaces;
using System;

public class EventsControllerTests
{
[Fact]
public async Task CreateEvent_ValidInput_SchedulesJobs()
{
var mockRepo = new Mock<IEventRepository>();
var mockAnalytics = new Mock<IAnalyticsService>();
var mockShop = new Mock<IArkShopService>();
var mockRcon = new Mock<IRconService>();
var mockHangfire = new Mock<IBackgroundJobClient>();
var controller = new EventsController(mockRepo.Object, mockAnalytics.Object, mockShop.Object, mockRcon.Object, mockHangfire.Object);

var @event = new Event
{
Title = "Test Event",
StartTime = DateTime.Now.AddHours(1),
EndTime = DateTime.Now.AddHours(2),
TimeZoneId = "America/New_York",
RconCommands = new() { "command1" },
TempShopConfig = new()
};

mockShop.Setup(s => s.GetConfigAsync(It.IsAny<bool>())).ReturnsAsync(new ArkShopConfig());

var result = await controller.CreateEvent(@event);

mockRepo.Verify(r => r.CreateAsync(It.IsAny<Event>()), Times.Once());
mockHangfire.Verify(h => h.Schedule(It.IsAny<Expression<Action>>(), It.IsAny<DateTimeOffset>()), Times.Exactly(2));
result.Should().BeOfType<CreatedAtActionResult>();
}
}
```

### Api.Tests/Unit/TimezoneTests.cs
```csharp
using FluentAssertions;
using System;

public class TimezoneTests
{
[Fact]
public void ConvertToUtc_ValidTimezone_ReturnsUtcTime()
{
var localTime = new DateTime(2025, 9, 3, 12, 0, 0);
var tz = TimeZoneInfo.FindSystemTimeZoneById("America/New_York");
var utcTime = TimeZoneInfo.ConvertTimeToUtc(localTime, tz);

utcTime.Should().Be(localTime.AddHours(4)); // EDT offset
}
}
```

## Step 5: Infrastructure Setup
`docker-compose.yml`:
```yaml
version: '3.8'
services:
api:
build: ./Api
ports:
- "5001:80"
depends_on:
- mongo
- postgres
- redis
- rabbitmq
mongo:
image: mongo:latest
ports:
- "27017:27017"
postgres:
image: postgres:latest
environment:
POSTGRES_DB: orchard
POSTGRES_USER: postgres
POSTGRES_PASSWORD: password
ports:
- "5432:5432"
redis:
image: redis:latest
ports:
- "6379:6379"
rabbitmq:
image: rabbitmq:management
ports:
- "5672:5672"
- "15672:15672"
seq:
image: datalust/seq:latest
ports:
- "5341:80"
environment:
ACCEPT_EULA: Y
discordbot:
build: ./DiscordBot
depends_on:
- api
- rabbitmq
testserver:
image: your-ark-server-image
ports:
- "27021:27021"
# Configure test server as needed
```

## Step 6: Kubernetes Stub
`k8s/deployment.yaml`:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
name: game-api
spec:
replicas: 3
selector:
matchLabels:
app: game-api
template:
metadata:
labels:
app: game-api
spec:
containers:
- name: api
image: your-docker-image/api:latest
ports:
- containerPort: 80
env:
- name: ASPNETCORE_ENVIRONMENT
value: Production
- name: testserver
image: your-ark-server-image
ports:
- containerPort: 27021
```

## Step 7: Next Steps
- **Run:** `dotnet run --project Api` (https://localhost:5001), `dotnet run --project ClientApp`, `dotnet run --project DiscordBot`.
- **Test:** `dotnet test Api.Tests`.
- **Orchard Setup:** Access `/admin` to configure tenants.
- **Plugins:** Implement sample plugins in `/Plugins`.
- **Analytics:** Track events in controllers/services.
- **Timezone Testing:** Verify conversions in UI and API.
- **Test Server Setup:** Ensure test server is running and accessible.
- **UI Mockups:** If you want visual mockups (e.g., calendar or event creation form), confirm explicitly.
- **Expand:** Add multi-server support, advanced shop config merging, more tests.
