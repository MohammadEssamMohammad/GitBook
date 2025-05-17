Database Schema Overview

The database schema is designed using Entity Framework Core, employing a code-first approach. Key entities and their relationships define the structure of the SQL Server database.

Core Entities and Relationships

The following are the primary entities and their significant relationships. For a visual representation, please see the Simplified Class Diagram in the UML Diagrams section.

AppUser (Identity):

Stores core user information for authentication (username, email, password hash).

Base for specific user roles.

Relationships:

One-to-One with Admin, Doctor, or Student.

Admin:

Represents an administrator user.

Linked to AppUser.

Relationships:

One-to-Many with Notification (Admin sends notifications).

One-to-Many with Evaluation (Admin can be an evaluator).

Doctor:

Represents a doctor/faculty member.

Linked to AppUser.

Relationships:

One-to-Many with DoctorProjectIdea (Doctor submits project ideas).

One-to-Many with Team (as Supervisor).

One-to-Many with TaskItem (as Supervisor assigning tasks).

Many-to-Many with Schedule via CommitteeDoctorSchedule (as Examiner/Supervisor).

One-to-Many with Evaluation (Doctor can be an evaluator).

Student:

Represents a student user.

Linked to AppUser.

Relationships:

Many-to-One with Team (Student is a member of a team).

One-to-One with Team (Student can be a leader of a team).

One-to-Many with TeamProjectIdea (as Leader submitting project ideas).

Many-to-Many with TaskItem via TaskMember.

One-to-Many with Evaluation (Student receives evaluations).

Team:

Represents a student team.

Relationships:

One-to-Many with Student (Team has members).

One-to-One with Student (Team has a leader).

Many-to-One with Doctor (Team is supervised by a doctor).

One-to-Many with TeamProjectIdea.

One-to-One with FinalProjectIdea.

One-to-Many with Schedule.

One-to-Many with TaskItem.

One-to-Many with Invitation (Team sends invitations).

Project Ideas:

DoctorProjectIdea: Submitted by a Doctor.

TeamProjectIdea: Submitted by a Student (Team Leader) for their Team.

FinalProjectIdea: The approved project idea for a Team, linked to either a DoctorProjectIdea or TeamProjectIdea and a Supervisor (Doctor).

AcademicAppointment:

Defines academic years and term dates (e.g., "2023-2024", First Term Start/End).

Relationships:

One-to-Many with Criteria.

One-to-Many with Schedule.

Criteria:

Defines evaluation criteria (name, description, max grade, evaluator type, target).

Linked to an AcademicAppointment.

Relationships:

One-to-Many with Evaluation.

Many-to-Many with Schedule via CriteriaSchedule.

Schedule:

Defines evaluation/presentation schedules for teams.

Linked to a Team and an AcademicAppointment.

Relationships:

One-to-Many with CommitteeDoctorSchedule (linking Doctors as examiners/supervisors).

One-to-Many with Evaluation.

Many-to-Many with Criteria via CriteriaSchedule.

Evaluation:

Stores grades given during evaluations.

Linked to Schedule, Criteria, an evaluator (Admin or Doctor), and a recipient (Student or Team).

Tasks:

TaskItem: A task assigned by a Doctor (Supervisor) to a Team.

TaskMember: Links a TaskItem to specific Students within the team, tracking their status.

Notification:

Stores notifications sent by Admins to specific roles (Students, Doctors, All).

Invitation:

Represents an invitation from a Team (Leader) to a Student to join the team.

Auxiliary Entities:

UserOtp: Stores OTPs for email verification, linked to user email.

TemporaryUser: Stores student registration data before email verification.

CommitteeDoctorSchedule: Join table for Schedule and Doctor, defining the doctor's role in the schedule.

CriteriaSchedule: Join table for Criteria and Schedule.

Database Context

GradingManagementSystemDbContext.cs: This class, inheriting from IdentityDbContext<AppUser, IdentityRole, string>, is the Entity Framework Core context. It defines DbSet properties for each entity, representing tables in the database. Configuration for entities (like relationships, constraints) is applied in the OnModelCreating method, often by loading configurations from separate classes (e.g., AdminConfigurations, TeamConfigurations).

Migrations

Database schema changes are managed using EF Core Migrations. The GradingManagementSystem.Repository/Data/Migrations folder contains migration files that track changes to the model and allow updating the database schema accordingly.

This schema facilitates the complex interactions and data storage requirements of the Graduation Projects Grading System.


