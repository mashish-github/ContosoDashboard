# Research: Document Upload and Management

**Feature**: Document Upload and Management
**Date**: 2026-03-29
**Purpose**: Resolve technical unknowns and establish best practices before design phase

## Research Tasks Completed

### 1. File Upload in Blazor Server

**Task**: Research best practices for implementing file upload in Blazor Server applications

**Findings**:

- Use `InputFile` component for client-side file selection
- Handle upload in component code-behind or service
- Stream files to avoid memory issues with large files
- Show progress using `IProgress<T>` or custom progress component
- Validate files on client and server side

**Decision**: Use `InputFile` component with server-side streaming. Implement progress indicator and client-side validation.

**Rationale**: Blazor Server supports streaming uploads efficiently. Client validation improves UX, server validation ensures security.

**Alternatives Considered**:

- JavaScript interop for custom upload UI (rejected: adds complexity, Blazor components sufficient)
- Client-side only validation (rejected: security risk)

### 2. Secure File Storage Patterns

**Task**: Research secure file storage patterns for web applications with local filesystem

**Findings**:

- Store files outside web root to prevent direct URL access
- Use GUID-based filenames to prevent enumeration attacks
- Implement authorization checks in download endpoints
- Validate file extensions server-side
- Consider virus scanning (placeholder for training)

**Decision**: Store files in `AppData/uploads/{userId}/{projectId}/{guid}.{ext}`. Serve via controller with authorization.

**Rationale**: Prevents direct access, enables permission checks, supports future cloud migration.

**Alternatives Considered**:

- Store in database as BLOB (rejected: poor performance for large files)
- Store in wwwroot with obfuscated names (rejected: still accessible if discovered)

### 3. Document Search in EF Core

**Task**: Research efficient search implementation for documents in Entity Framework Core

**Findings**:

- Use EF Core LINQ queries for structured data (category, project, date)
- Implement full-text search using SQL Server FTS or LIKE queries
- Index searchable fields for performance
- Paginate results to handle large datasets

**Decision**: Use EF Core queries with SQL Server LIKE for text search. Add database indexes on searchable fields.

**Rationale**: Simple implementation sufficient for training scale. FTS overkill for small dataset.

**Alternatives Considered**:

- Elasticsearch integration (rejected: adds complexity, overkill for local training)
- Client-side search (rejected: poor performance, security issues)

### 4. Role-Based Document Permissions

**Task**: Research implementing role-based permissions for document access in ASP.NET Core

**Findings**:

- Use existing authorization system with policies
- Check permissions at service layer and page level
- Implement hierarchical permissions (Employee < Team Lead < Project Manager < Admin)
- Use claims-based identity for user roles

**Decision**: Extend existing `CustomAuthenticationStateProvider` with document-specific policies.

**Rationale**: Leverages existing auth system, maintains consistency.

**Alternatives Considered**:

- Custom permission system (rejected: duplicates existing functionality)
- Database-only permissions (rejected: UI needs to hide unauthorized actions)

## Summary of Decisions

- **Upload**: `InputFile` with server streaming and progress
- **Storage**: Local filesystem outside web root with GUID paths
- **Search**: EF Core with LIKE queries and indexes
- **Permissions**: ASP.NET Core policies extending existing auth

All technical approaches align with constitution principles and project constraints.
