<!--
Sync Impact Report:
- Version change: initial → 1.0.0
- Modified principles: All principles defined (Security-First Development, Test-Driven Development, Clean Architecture, User Experience Focus, Maintainability and Documentation)
- Added sections: Technology Stack and Constraints, Development Workflow
- Removed sections: None
- Templates requiring updates: None
- Follow-up TODOs: None
-->

# ContosoDashboard Constitution

<!-- Example: Spec Constitution, TaskFlow Constitution, etc. -->

## Core Principles

### Security-First Development

All features must incorporate security considerations from design through implementation. The application demonstrates production-ready security patterns including authentication, authorization, and data protection, even in a training context with mock authentication.

### Test-Driven Development

Tests must be written before implementing features. Unit tests for services and components, integration tests for end-to-end functionality. Follow red-green-refactor cycle.

### Clean Architecture (NON-NEGOTIABLE)

Maintain separation of concerns with distinct layers: Models for data, Services for business logic, Pages for UI. Use dependency injection and avoid tight coupling.

### User Experience Focus

Prioritize intuitive and accessible user interfaces. Ensure proper navigation, feedback, and responsive design. Follow web accessibility guidelines.

### Maintainability and Documentation

Code must be readable, well-documented, and follow C# and Blazor best practices. Include XML comments for public APIs and maintain clear project structure.

## Technology Stack and Constraints

**Framework**: Blazor Server with .NET 8.0  
**Database**: Entity Framework Core with SQL Server (local development)  
**UI**: Bootstrap for responsive design  
**Authentication**: Mock system for training (demonstrates cookie-based auth, claims, RBAC)  
**Deployment**: Local development only, no cloud dependencies

## Development Workflow

**Methodology**: Spec-Driven Development using GitHub Spec Kit (speckit)  
**Process**: Write feature specs, create implementation plans, generate tasks, implement incrementally by user story  
**Code Reviews**: Required for all changes, verify compliance with constitution principles  
**Testing**: Automated tests for all features, manual testing for UI interactions

## Governance

This constitution guides all development activities for the ContosoDashboard training project. All features must comply with the core principles. Amendments require documentation and consensus among contributors. Use speckit workflow for feature development.

**Version**: 1.0.0 | **Ratified**: 2026-03-29 | **Last Amended**: 2026-03-29

<!-- Example: Version: 2.1.1 | Ratified: 2025-06-13 | Last Amended: 2025-07-16 -->
