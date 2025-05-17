System Setup and Services

This page details the setup process in Program.cs and the core services configured for the application.

Application Startup (Program.cs)

The Program.cs file is the entry point of the ASP.NET Core application. It's responsible for:

Creating a WebApplicationBuilder.

Configuring services for dependency injection.

Building the WebApplication.

Configuring the HTTP request pipeline (middleware).

Running the application.

Service Configuration

Key services are registered in the Main method, primarily through extension methods:

builder.Services.AddApplicationServices(builder.Configuration): (Defined in ApplicationServicesExtension.cs)

Database Context: Registers GradingManagementSystemDbContext with SQL Server.

Services.AddDbContext<GradingManagementSystemDbContext>(options =>
{
    options.UseSqlServer(configuration.GetConnectionString("DefaultConnection")); // Or "MonsterConnection"
});
IGNORE_WHEN_COPYING_START
content_copy
download
Use code with caution.
C#
IGNORE_WHEN_COPYING_END

Controllers & API Behavior: Adds controller services and configures API behavior options, including custom handling for invalid model states to return a structured ApiResponse.

API Explorer & Swagger: Configures Swagger/OpenAPI for API documentation, including JWT bearer token authentication support in Swagger UI.

Repositories: Registers generic and specific repository implementations (e.g., IGenericRepository<>, INotificationRepository, IProjectRepository).

Services.AddScoped(typeof(IGenericRepository<>), typeof(GenericRepository<>));
Services.AddScoped<INotificationRepository, NotificationRepository>();
// ... other repositories
IGNORE_WHEN_COPYING_START
content_copy
download
Use code with caution.
C#
IGNORE_WHEN_COPYING_END

Unit of Work: Registers IUnitOfWork.

Services.AddScoped<IUnitOfWork, UnitOfWork>();
IGNORE_WHEN_COPYING_START
content_copy
download
Use code with caution.
C#
IGNORE_WHEN_COPYING_END

Business Services: Registers application services (e.g., IAuthenticationService, IEmailService, ITaskService, ITokenService, IUserProfileService).

Services.AddScoped<IAuthenticationService, AuthenticationService>();
Services.AddScoped<IEmailService, EmailService>();
// ... other services
IGNORE_WHEN_COPYING_START
content_copy
download
Use code with caution.
C#
IGNORE_WHEN_COPYING_END

SignalR: Adds SignalR services.

Services.AddSignalR();
IGNORE_WHEN_COPYING_START
content_copy
download
Use code with caution.
C#
IGNORE_WHEN_COPYING_END

builder.Services.AddIdentityServices(builder.Configuration): (Defined in IdentityServicesExtension.cs)

ASP.NET Core Identity: Configures IdentityCore for AppUser and IdentityRole, using GradingManagementSystemDbContext for storage.

Services.AddIdentityCore<AppUser>().AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<GradingManagementSystemDbContext>()
    .AddSignInManager<SignInManager<AppUser>>()
    .AddRoleManager<RoleManager<IdentityRole>>()
    .AddDefaultTokenProviders();
IGNORE_WHEN_COPYING_START
content_copy
download
Use code with caution.
C#
IGNORE_WHEN_COPYING_END

Data Protection Token Provider: Configures token lifespan (e.g., for password reset).

JWT Authentication: Configures JWT bearer authentication scheme with token validation parameters (issuer, audience, lifetime, signing key).

CORS Configuration:

Defines a CORS policy named "CorsPolicy" allowing specific origins (http://localhost:4200, https://graduation-project-angular.vercel.app), any header, any method, and credentials.

builder.Services.AddCors(options =>
{
    options.AddPolicy("CorsPolicy", policy =>
    {
        policy.WithOrigins("http://localhost:4200", "https://graduation-project-angular.vercel.app")
              .AllowAnyHeader()
              .AllowAnyMethod()
              .AllowCredentials();
    });
});
IGNORE_WHEN_COPYING_START
content_copy
download
Use code with caution.
C#
IGNORE_WHEN_COPYING_END
Database Migration and Seeding

After building the app (var app = builder.Build();), the system attempts to:

Apply Database Migrations Automatically:

var _dbContext = services.GetRequiredService<GradingManagementSystemDbContext>();
await _dbContext.Database.MigrateAsync();
IGNORE_WHEN_COPYING_START
content_copy
download
Use code with caution.
C#
IGNORE_WHEN_COPYING_END

Seed Initial Data: Calls SeedData.SeedRolesAndAdminUsersAsync to create default roles ("Admin", "Doctor", "Student") and predefined admin users if they don't exist.

HTTP Request Pipeline Configuration (Middleware)

The order of middleware registration is crucial:

app.MapHub<NotificationHub>("/api/notificationHub"): Maps the SignalR NotificationHub to the specified endpoint.

app.UseMiddleware<ExceptionMiddleware>(): Adds custom global exception handling.

Swagger:

app.UseSwagger(): Enables the Swagger JSON endpoint.

app.UseSwaggerUI(): Enables the Swagger UI.

app.UseHttpsRedirection(): Redirects HTTP requests to HTTPS.

app.UseStaticFiles(): Enables serving static files (e.g., profile pictures from wwwroot).

app.UseCors("CorsPolicy"): Applies the configured CORS policy.

app.UseRouting(): Adds route matching to the pipeline.

app.UseAuthentication(): Adds authentication middleware (validates JWTs).

app.UseAuthorization(): Adds authorization middleware (checks if authenticated users have required roles/policies).

app.MapControllers(): Maps attribute-routed controllers.

Finally, app.Run() starts the application and listens for incoming HTTP requests.

This setup ensures that all necessary services are available via DI and the request pipeline is configured correctly to handle requests, authentication, routing, and real-time communication.
