# Technologies Used

The Graduation Projects Grading System leverages a modern .NET technology stack and common software development patterns.

## Core Framework & Platform

*   **.NET 8**: The primary framework for building the application. (Identified from `.NETCoreApp,Version=v8.0.AssemblyAttributes.cs`)
*   **ASP.NET Core 8**: Used for building the web API, handling HTTP requests, routing, and middleware.

## Data Access

*   **Entity Framework Core**: The Object-Relational Mapper (ORM) used for interacting with the database. It simplifies data access operations and database schema management. (Identified from `Microsoft.EntityFrameworkCore` namespace and `GradingManagementSystemDbContext`).
*   **SQL Server**: The relational database management system used for storing application data. (Implied by `options.UseSqlServer` in `ApplicationServicesExtension.cs` and connection string name "MonsterConnection" or "DefaultConnection").
*   **Repository Pattern**: Used to abstract data access logic, promoting testability and separation of concerns. (Identified by `IGenericRepository<>` and its implementations).
*   **Unit of Work Pattern**: Used to manage transactions and ensure data consistency across multiple repository operations. (Identified by `IUnitOfWork` and its implementation).

## Authentication & Authorization

*   **ASP.NET Core Identity**: Provides a comprehensive membership system for managing users, passwords, roles, and claims. (Identified from `Microsoft.AspNetCore.Identity` namespace and `IdentityServicesExtension.cs`).
*   **JSON Web Tokens (JWT)**: Used for stateless, token-based authentication. The system generates JWTs upon successful login, which clients then include in subsequent requests. (Identified from `AddJwtBearer` configuration in `IdentityServicesExtension.cs` and `ITokenService`).

## Real-time Communication

*   **SignalR**: Used for enabling real-time, bi-directional communication between the server and clients, primarily for notifications. (Identified from `Microsoft.AspNetCore.SignalR` namespace, `NotificationHub.cs`, and `app.MapHub<NotificationHub>`).

## API & Development Tools

*   **Swagger (OpenAPI)**: Used for designing, building, documenting, and consuming RESTful APIs. The system integrates Swashbuckle to automatically generate Swagger UI. (Identified from `AddSwaggerGen` in `ApplicationServicesExtension.cs` and `app.UseSwagger()`).
*   **Dependency Injection (DI)**: Heavily used throughout the ASP.NET Core application to manage dependencies and promote loose coupling. (Evident in `Program.cs` service configuration).
*   **CORS (Cross-Origin Resource Sharing)**: Configured to allow requests from specified client origins (e.g., `http://localhost:4200`, `https://graduation-project-angular.vercel.app`). (Identified in `Program.cs`).

## Email Communication

*   **SMTP**: Used for sending emails (e.g., OTP for email verification, password reset links). (Identified by `System.Net.Mail.SmtpClient` in `EmailService.cs` and email settings in configuration).

## Coding Patterns & Practices

*   **Layered Architecture**: Separates concerns into API, Service, Repository, and Core layers.
*   **Async/Await**: Extensively used for non-blocking I/O operations, improving application responsiveness and scalability.
*   **DTOs (Data Transfer Objects)**: Used for shaping data passed between layers and to/from clients.
*   **Custom Middleware**: For tasks like global exception handling (`ExceptionMiddleware.cs`).
*   **Configuration Management**: Utilizes ASP.NET Core's configuration system (e.g., `IConfiguration`).

This combination of technologies and patterns provides a robust, scalable, and maintainable foundation for the Graduation Projects Grading System.


