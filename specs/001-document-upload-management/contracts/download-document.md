# Contract: Document Download API

**Type**: Web API Endpoint
**Method**: GET
**Path**: `/api/documents/{id}/download`
**Purpose**: Securely download a document file
**Feature**: Document Upload and Management

## Request

### URL Parameters

- `id` (int, required): Document ID

### Headers

- `Authorization`: Bearer token or cookie authentication
- `Accept`: `application/octet-stream`

### Query Parameters

- None

## Response

### Success (200 OK)

- **Content-Type**: Based on document MIME type (e.g., `application/pdf`)
- **Content-Disposition**: `attachment; filename="{originalFileName}"`
- **Body**: File content as binary stream

### Error Responses

- **401 Unauthorized**: User not authenticated
- **403 Forbidden**: User lacks permission to access document
- **404 Not Found**: Document doesn't exist or is deleted
- **500 Internal Server Error**: Storage system error

## Authorization

- User must be authenticated
- User must be document owner, or document shared with user, or have project access for project documents
- Admin has access to all documents

## Implementation Notes

- Stream file content directly to response to handle large files
- Log download activity for audit
- Validate document exists and user has permission before streaming
- Handle concurrent downloads safely
