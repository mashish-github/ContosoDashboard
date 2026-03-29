# Data Model: Document Upload and Management

**Feature**: Document Upload and Management
**Date**: 2026-03-29
**Purpose**: Define data entities, relationships, and validation rules for the document management system

## Entities

### Document

Represents an uploaded document with its metadata.

**Attributes**:

- `Id` (int, PK): Unique identifier (integer for consistency with existing keys)
- `Title` (string, required, max 255): Document title provided by user
- `Description` (string, optional, max 1000): Optional description
- `Category` (string, required): Predefined category ("Project Documents", "Team Resources", "Personal Files", "Reports", "Presentations", "Other")
- `ProjectId` (int, optional, FK): Associated project ID (null for personal documents)
- `Tags` (string, optional, max 500): Comma-separated tags for search
- `FilePath` (string, required, max 500): Secure file path (GUID-based)
- `FileName` (string, required, max 255): Original filename for display
- `FileSize` (long, required): File size in bytes
- `MimeType` (string, required, max 255): MIME type (e.g., "application/pdf")
- `UploadDate` (DateTime, required): Upload timestamp
- `UploaderId` (string, required, FK): User ID of uploader
- `IsDeleted` (bool, default false): Soft delete flag

**Relationships**:

- Belongs to `User` (UploaderId → User.Id)
- Belongs to `Project` (ProjectId → Project.Id, optional)
- Has many `DocumentShare`

**Validation Rules**:

- Title: Required, 1-255 characters
- Description: Optional, 0-1000 characters
- Category: Required, must be from predefined list
- FileSize: Must be ≤ 25MB (26,214,400 bytes)
- MimeType: Must be from allowed types (PDF, Office docs, text, images)
- FilePath: Must follow pattern `{userId}/{projectId or "personal"}/{guid}.{ext}`

**Business Rules**:

- Only uploader or project managers can edit/delete
- Soft delete preserves file for audit
- Category determines default permissions

### DocumentShare

Represents sharing a document with another user.

**Attributes**:

- `Id` (int, PK): Unique identifier
- `DocumentId` (int, required, FK): Shared document ID
- `SharedWithUserId` (string, required, FK): User ID receiving access
- `SharedByUserId` (string, required, FK): User ID who shared
- `ShareDate` (DateTime, required): When shared
- `Permissions` (string, optional): Future extension for granular permissions

**Relationships**:

- Belongs to `Document` (DocumentId → Document.Id)
- Belongs to `User` (SharedWithUserId → User.Id)
- Belongs to `User` (SharedByUserId → User.Id)

**Validation Rules**:

- Cannot share with self
- Must have permission to share the document
- Recipient must exist

**Business Rules**:

- Creates notification for recipient
- Grants read access to the document
- Can be revoked by sharer or document owner

## Database Schema

```sql
CREATE TABLE Documents (
    Id INT IDENTITY(1,1) PRIMARY KEY,
    Title NVARCHAR(255) NOT NULL,
    Description NVARCHAR(1000),
    Category NVARCHAR(50) NOT NULL,
    ProjectId INT,
    Tags NVARCHAR(500),
    FilePath NVARCHAR(500) NOT NULL,
    FileName NVARCHAR(255) NOT NULL,
    FileSize BIGINT NOT NULL,
    MimeType NVARCHAR(255) NOT NULL,
    UploadDate DATETIME2 NOT NULL,
    UploaderId NVARCHAR(450) NOT NULL,
    IsDeleted BIT NOT NULL DEFAULT 0,

    CONSTRAINT FK_Documents_Users FOREIGN KEY (UploaderId) REFERENCES AspNetUsers(Id),
    CONSTRAINT FK_Documents_Projects FOREIGN KEY (ProjectId) REFERENCES Projects(Id),
    CONSTRAINT CHK_Documents_Category CHECK (Category IN ('Project Documents', 'Team Resources', 'Personal Files', 'Reports', 'Presentations', 'Other')),
    CONSTRAINT CHK_Documents_FileSize CHECK (FileSize <= 26214400) -- 25MB
);

CREATE TABLE DocumentShares (
    Id INT IDENTITY(1,1) PRIMARY KEY,
    DocumentId INT NOT NULL,
    SharedWithUserId NVARCHAR(450) NOT NULL,
    SharedByUserId NVARCHAR(450) NOT NULL,
    ShareDate DATETIME2 NOT NULL,

    CONSTRAINT FK_DocumentShares_Documents FOREIGN KEY (DocumentId) REFERENCES Documents(Id),
    CONSTRAINT FK_DocumentShares_SharedWith FOREIGN KEY (SharedWithUserId) REFERENCES AspNetUsers(Id),
    CONSTRAINT FK_DocumentShares_SharedBy FOREIGN KEY (SharedByUserId) REFERENCES AspNetUsers(Id),
    CONSTRAINT CHK_NoSelfShare CHECK (SharedWithUserId != SharedByUserId)
);

-- Indexes for performance
CREATE INDEX IX_Documents_UploaderId ON Documents(UploaderId);
CREATE INDEX IX_Documents_ProjectId ON Documents(ProjectId);
CREATE INDEX IX_Documents_Category ON Documents(Category);
CREATE INDEX IX_Documents_UploadDate ON Documents(UploadDate DESC);
CREATE INDEX IX_DocumentShares_DocumentId ON DocumentShares(DocumentId);
CREATE INDEX IX_DocumentShares_SharedWithUserId ON DocumentShares(SharedWithUserId);
```

## Data Flow

1. **Upload**: User selects file → Validate → Generate secure path → Save to disk → Create Document record → Return success
2. **Share**: User selects document and recipient → Validate permissions → Create DocumentShare record → Send notification
3. **Search**: Query Documents table with filters → Join related tables → Return paginated results
4. **Download**: Check permissions → Stream file from secure location → Log access

## Migration Strategy

- Add new tables via EF Core migration
- Existing data remains unchanged
- Rollback: Drop new tables (files remain for safety)
