# Contract: List Documents API

**Type**: Web API Endpoint
**Method**: GET
**Path**: `/api/documents`
**Purpose**: Retrieve paginated list of documents with filtering
**Feature**: Document Upload and Management

## Request

### Query Parameters

- `page` (int, optional, default 1): Page number
- `pageSize` (int, optional, default 20): Items per page (max 100)
- `search` (string, optional): Search term for title, description, tags
- `category` (string, optional): Filter by category
- `projectId` (int, optional): Filter by project
- `uploaderId` (string, optional): Filter by uploader
- `sortBy` (string, optional): Sort field (title, uploadDate, fileSize, category)
- `sortOrder` (string, optional): Sort direction (asc, desc, default desc)

### Headers

- `Authorization`: Bearer token or cookie authentication
- `Accept`: `application/json`

## Response

### Success (200 OK)

```json
{
  "items": [
    {
      "id": 1,
      "title": "Project Plan.pdf",
      "description": "Q1 project planning document",
      "category": "Project Documents",
      "projectId": 123,
      "projectName": "Dashboard Redesign",
      "tags": "planning,q1",
      "fileName": "project-plan.pdf",
      "fileSize": 2457600,
      "mimeType": "application/pdf",
      "uploadDate": "2026-03-29T10:30:00Z",
      "uploaderId": "user123",
      "uploaderName": "John Doe"
    }
  ],
  "totalCount": 45,
  "page": 1,
  "pageSize": 20,
  "totalPages": 3
}
```

### Error Responses

- **400 Bad Request**: Invalid query parameters
- **401 Unauthorized**: User not authenticated
- **500 Internal Server Error**: Database error

## Authorization

- User must be authenticated
- Returns only documents user has access to (owned, shared, or project documents)

## Implementation Notes

- Use EF Core with dynamic LINQ for filtering
- Implement search with SQL LIKE on title, description, tags
- Paginate results for performance
- Include related data (project name, uploader name) via joins
