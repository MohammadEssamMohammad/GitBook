# System Architecture

The Graduation Projects Grading System is built using a layered architecture, promoting separation of concerns, maintainability, and scalability.

## High-Level Architecture

The system follows a clean, n-tier architectural pattern:

1.  **API Layer (Presentation)**: Handles incoming HTTP requests, authentication, authorization, request validation, and responses. It interacts with the Service Layer.
    *   Located in `GradingManagementSystem.APIs` project.
    *   Key components: Controllers, SignalR Hubs, Middlewares, DTOs (for request/response shaping).
2.  **Service Layer (Business Logic)**: Contains the core business logic, orchestrates operations, and interacts with the Repository Layer.
    *   Located in `GradingManagementSystem.Service` project.
    *   Key components: Service classes (e.g., `AuthenticationService`, `UserProfileService`).
3.  **Repository Layer (Data Access)**: Abstracts the data persistence mechanism. It defines interfaces for data access and provides concrete implementations.
    *   Located in `GradingManagementSystem.Repository` project.
    *   Key components: Repository interfaces (in Core), Repository implementations, Entity Framework Core `DbContext`, Unit of Work pattern.
4.  **Core Layer (Domain)**: Contains the domain entities, DTOs (Data Transfer Objects), custom responses, and interfaces for services and repositories. This layer is central and has no dependencies on other layers.
    *   Located in `GradingManagementSystem.Core` project.
    *   Key components: Entities, DTOs, Repository and Service interfaces, Enum definitions.
5.  **Infrastructure/Shared Services**:
    *   **Database**: SQL Server is used for data persistence, managed via Entity Framework Core.
    *   **Identity**: ASP.NET Core Identity is used for user authentication and authorization.
    *   **Real-time Communication**: SignalR is used for sending real-time notifications.
    *   **Email Service**: For sending emails (e.g., OTP, password reset).

```mermaid
graph TD
    Client[Client Applications <br/> (e.g., Web/Mobile Frontend)] -->|HTTP/S, WebSocket| API_Layer[API Layer <br/> (ASP.NET Core MVC/API, SignalR)]
    API_Layer -->|Calls| Service_Layer[Service Layer <br/> (Business Logic)]
    Service_Layer -->|Uses| Core_Layer_Interfaces[Core Layer Interfaces <br/> (IService, IRepository)]
    Service_Layer -->|Uses| Repository_Layer[Repository Layer <br/> (Data Access Logic)]
    Repository_Layer -->|Uses| Core_Layer_Entities[Core Layer Entities <br/> (Domain Objects)]
    Repository_Layer -->|Uses| EF_Core[Entity Framework Core]
    EF_Core -->|CRUD Operations| Database[(SQL Server Database)]
    API_Layer -->|Uses| ASPNET_Identity[ASP.NET Core Identity <br/> (Authentication & Authorization)]
    ASPNET_Identity -->|User Store| EF_Core
    API_Layer -->|Uses| SignalR_Hub[SignalR Hubs <br/> (NotificationHub)]
    Service_Layer -->|Uses| Email_Service[Email Service]

    subgraph "GradingManagementSystem.APIs"
        API_Layer
        SignalR_Hub
    end

    subgraph "GradingManagementSystem.Service"
        Service_Layer
        Email_Service
    end

    subgraph "GradingManagementSystem.Repository"
        Repository_Layer
        EF_Core
    end

    subgraph "GradingManagementSystem.Core"
        Core_Layer_Interfaces
        Core_Layer_Entities
    end

    subgraph "External/Infrastructure"
        Database
        ASPNET_Identity
    end
```

## Key Components

*   **`Program.cs`**: The main entry point for the API application. It configures services (Dependency Injection), the HTTP request pipeline (middleware), and initializes the application.
*   **Controllers (e.g., `AuthenticationController`, `ProjectsController`)**: Handle API requests for specific resources, validate input, call service methods, and return appropriate HTTP responses.
*   **Services (e.g., `IAuthenticationService`, `ITaskService`)**: Encapsulate business logic. They are injected into controllers.
*   **Repositories (e.g., `IGenericRepository<>`, `IProjectRepository`)**: Provide an abstraction layer over data storage. Implemented using Entity Framework Core.
*   **`UnitOfWork` (`IUnitOfWork`)**: Manages database transactions and coordinates changes across multiple repositories.
*   **Entities (e.g., `Student`, `Team`, `ProjectIdea`)**: Represent the domain objects and map to database tables.
*   **DTOs (Data Transfer Objects)**: Used to transfer data between layers, especially between the API and clients, and between services and controllers.
*   **`GradingManagementSystemDbContext`**: The Entity Framework Core DbContext class that manages database sessions, change tracking, and persistence.
*   **`NotificationHub`**: A SignalR hub responsible for real-time communication, primarily for sending notifications to connected clients based on their roles.
*   **ASP.NET Core Identity (`AppUser`, `UserManager`, `SignInManager`, `RoleManager`)**: Handles user registration, login, roles, and security.
*   **Middleware (`ExceptionMiddleware`)**: Custom middleware for global exception handling, ensuring consistent error responses.
*   **Extensions (`ApplicationServicesExtension`, `IdentityServicesExtension`)**: Static classes used to organize service registration and configuration in `Program.cs`.

This layered approach ensures that each part of the system has a distinct responsibility, making the application easier to develop, test, and maintain.


