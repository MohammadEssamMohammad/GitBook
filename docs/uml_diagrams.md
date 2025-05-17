UML Diagrams

This section provides various UML diagrams to visualize the system's structure, relationships, and interactions.

1. Component Diagram

This diagram shows the high-level components of the system and their dependencies.

graph LR
    subgraph "Client Tier"
        UserInterface[Web/Mobile Frontend]
    end

    subgraph "Application Tier (Backend)"
        APILayer["GradingManagementSystem.APIs<br/>(Controllers, Hubs, Middleware)"]
        ServiceLayer["GradingManagementSystem.Service<br/>(Business Logic Services)"]
        RepositoryLayer["GradingManagementSystem.Repository<br/>(Data Access, UnitOfWork, DbContext)"]
        CoreLayer["GradingManagementSystem.Core<br/>(Entities, DTOs, Interfaces)"]
    end

    subgraph "Infrastructure/External Services"
        Database[(SQL Server Database)]
        IdentityService[ASP.NET Core Identity]
        EmailGateway[Email Gateway (SMTP)]
    end

    UserInterface --> APILayer
    APILayer --> ServiceLayer
    APILayer --> IdentityService
    APILayer --> CoreLayer

    ServiceLayer --> RepositoryLayer
    ServiceLayer --> CoreLayer
    ServiceLayer --> EmailGateway

    RepositoryLayer --> CoreLayer
    RepositoryLayer --> Database
    IdentityService --> Database
IGNORE_WHEN_COPYING_START
content_copy
download
Use code with caution.
Mermaid
IGNORE_WHEN_COPYING_END
2. Simplified Class Diagram (Key Entities)

This diagram illustrates the main entities and their relationships. For brevity, not all attributes or methods are shown.

classDiagram
    direction LR

    class AppUser {
        +string Id
        +string FullName
        +string Email
        +string ProfilePicture
        +string Specialty
        +Admin Admin
        +Doctor Doctor
        +Student Student
    }

    class Admin {
        +int Id
        +string FullName
        +string Email
        +string AppUserId
        +AppUser AppUser
        +List~Notification~ Notifications
        +List~Evaluation~ Evaluations
    }

    class Doctor {
        +int Id
        +string FullName
        +string Email
        +string AppUserId
        +AppUser AppUser
        +List~DoctorProjectIdea~ DoctorProjectIdeas
        +List~Team~ SupervisedTeams
        +List~TaskItem~ Tasks
        +List~Evaluation~ Evaluations
    }

    class Student {
        +int Id
        +string FullName
        +string Email
        +string Specialty
        +bool InTeam
        +string AppUserId
        +int TeamId
        +int LeaderOfTeamId
        +AppUser AppUser
        +Team Team
        +Team LeaderOfTeam
        +List~Evaluation~ Evaluations
        +List~TaskMember~ TaskMembers
    }

    class Team {
        +int Id
        +string Name
        +bool HasProject
        +string Specialty
        +int LeaderId
        +int SupervisorId
        +Student Leader
        +Doctor Supervisor
        +List~Student~ Students
        +FinalProjectIdea FinalProjectIdea
        +List~Schedule~ Schedules
        +List~TaskItem~ Tasks
    }

    class ProjectIdea {
        <<Abstract>>
        +string Name
        +string Description
        +DateTime SubmissionDate
        +string Status
    }
    DoctorProjectIdea --|> ProjectIdea
    TeamProjectIdea --|> ProjectIdea

    class DoctorProjectIdea {
        +int DoctorId
        +Doctor Doctor
        +bool Taken
    }

    class TeamProjectIdea {
        +int TeamId
        +int LeaderId
        +Team Team
        +Student Leader
    }

    class FinalProjectIdea {
        +int ProjectId
        +string ProjectName
        +string ProjectDescription
        +int SupervisorId
        +int TeamId
        +Doctor Supervisor
        +Team Team
    }

    class Schedule {
        +int Id
        +DateTime ScheduleDate
        +string Status
        +int TeamId
        +Team Team
        +AcademicAppointment AcademicAppointment
        +List~CommitteeDoctorSchedule~ CommitteeDoctorSchedules
        +List~Evaluation~ Evaluations
    }

    class CommitteeDoctorSchedule {
        +int ScheduleId
        +int DoctorId
        +string DoctorRole
        +bool HasCompletedEvaluation
        +Schedule Schedule
        +Doctor Doctor
    }

    class AcademicAppointment {
        +int Id
        +string Year
        +DateTime FirstTermStart
        +DateTime FirstTermEnd
        +string Status
        +List~Criteria~ Criterias
        +List~Schedule~ Schedules
    }

    class Criteria {
        +int Id
        +string Name
        +int MaxGrade
        +string Evaluator
        +string GivenTo
        +int AcademicAppointmentId
        +AcademicAppointment AcademicAppointment
        +List~Evaluation~ Evaluations
    }

    class Evaluation {
        +int Id
        +int ScheduleId
        +int CriteriaId
        +int DoctorEvaluatorId
        +int AdminEvaluatorId
        +int StudentId
        +int TeamId
        +double Grade
        +Schedule Schedule
        +Criteria Criteria
        +Doctor DoctorEvaluator
        +Admin AdminEvaluator
        +Student Student
        +Team Team
    }

    class TaskItem {
        +int Id
        +string Name
        +DateTime Deadline
        +int SupervisorId
        +int TeamId
        +Doctor Supervisor
        +Team Team
        +List~TaskMember~ TaskMembers
    }

    class TaskMember {
        +int TaskId
        +int StudentId
        +TaskItem Task
        +Student Student
    }

    class Notification {
        +int Id
        +string Title
        +string Role
        +int AdminId
        +Admin Admin
    }

    AppUser "1" -- "0..1" Admin : associated with
    AppUser "1" -- "0..1" Doctor : associated with
    AppUser "1" -- "0..1" Student : associated with

    Team "1" -- "1..*" Student : has members
    Student "1" -- "0..1" Team : member of
    Team "1" -- "1" Student : has leader (LeaderId)
    Team "0..1" -- "1" Doctor : supervised by (SupervisorId)

    Doctor "1" -- "0..*" DoctorProjectIdea : submits
    Team "1" -- "0..*" TeamProjectIdea : submits
    Team "1" -- "0..1" FinalProjectIdea : has

    Team "1" -- "0..*" Schedule : has
    Schedule "1" -- "0..*" CommitteeDoctorSchedule : involves
    Doctor "1" -- "0..*" CommitteeDoctorSchedule : participates in
    AcademicAppointment "1" -- "0..*" Schedule : for

    AcademicAppointment "1" -- "0..*" Criteria : defines
    Criteria "1" -- "0..*" Evaluation : basis for
    Schedule "1" -- "0..*" Evaluation : occurs during

    Evaluation "0..1" -- "1" Student : for (if individual)
    Evaluation "0..1" -- "1" Team : for (if team)
    Evaluation "0..1" -- "1" Doctor : by (DoctorEvaluatorId)
    Evaluation "0..1" -- "1" Admin : by (AdminEvaluatorId)

    Doctor "1" -- "0..*" TaskItem : assigns
    Team "1" -- "0..*" TaskItem : assigned to
    TaskItem "1" -- "1..*" TaskMember : consists of
    Student "1" -- "0..*" TaskMember : assigned

    Admin "1" -- "0..*" Notification : sends
IGNORE_WHEN_COPYING_START
content_copy
download
Use code with caution.
Mermaid
IGNORE_WHEN_COPYING_END
3. Sequence Diagram: Student Registration

This diagram shows the sequence of interactions for a new student registration.
(Refer to Data Flow section for the Mermaid diagram, as it's already detailed there.)

4. Sequence Diagram: Admin Sends Notification

This diagram shows the sequence of interactions when an admin sends a notification.
(Refer to Data Flow section for the Mermaid diagram, as it's already detailed there.)

5. Use Case Diagram

This diagram illustrates the main functionalities (use cases) available to different actors (Admin, Doctor, Student).

actor Admin
actor Doctor
actor Student

rectangle "Graduation Project Grading System" {
    Admin -- (Manage Academic Appointments)
    Admin -- (Manage Evaluation Criteria)
    Admin -- (Manage User Accounts)
    Admin -- (Review Project Ideas)
    Admin -- (Assign Supervisors to Teams)
    Admin -- (Send Notifications)
    Admin -- (Evaluate Teams/Students)
    Admin -- (Create Schedules)
    Admin -- (View All Projects)
    Admin -- (View All Teams)

    Doctor -- (Login)
    Doctor -- (Manage Profile)
    Doctor -- (Submit Project Ideas)
    Doctor -- (View Own Project Ideas)
    Doctor -- (Review Team Requests for Own Projects)
    Doctor -- (Supervise Teams)
    Doctor -- (Create Tasks for Teams)
    Doctor -- (Review Student Tasks)
    Doctor -- (Evaluate Teams/Students in Schedules)
    Doctor -- (View Assigned Schedules)
    Doctor -- (View Notifications)

    Student -- (Register)
    Student -- (Verify Email)
    Student -- (Login)
    Student -- (Manage Profile)
    Student -- (Create Team)
    Student -- (Invite Members to Team)
    Student -- (Respond to Team Invitations)
    Student -- (Submit Team Project Idea)
    Student -- (Request Doctor's Project Idea)
    Student -- (View Available Project Ideas)
    Student -- (View Team Tasks)
    Student -- (View Own Grades)
    Student -- (View Schedules)
    Student -- (View Notifications)

    (Login) .> (Manage Profile) : <<includes>>
    (Manage Academic Appointments) ..> Admin
    (Manage Evaluation Criteria) ..> Admin
    (Send Notifications) ..> Admin
    (Evaluate Teams/Students) ..> Admin
    (Evaluate Teams/Students in Schedules) ..> Doctor
    (Supervise Teams) ..> Doctor
}
IGNORE_WHEN_COPYING_START
content_copy
download
Use code with caution.
Mermaid
IGNORE_WHEN_COPYING_END

These diagrams provide a visual understanding of the system's components, entity relationships, and key interaction flows. For more detailed sequence diagrams of other specific flows, refer to the Data Flow section or analyze the respective controller and service interactions.


