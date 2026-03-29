# Contract: IFileStorageService

**Type**: Interface Contract
**Purpose**: Abstraction for file storage operations to enable cloud migration
**Feature**: Document Upload and Management

## Interface Definition

```csharp
public interface IFileStorageService
{
    Task<string> UploadAsync(Stream fileStream, string fileName, string userId, int? projectId);
    Task<Stream> DownloadAsync(string filePath);
    Task DeleteAsync(string filePath);
    Task<bool> ExistsAsync(string filePath);
    string GetUrl(string filePath); // For future cloud URLs
}
```

## Method Specifications

### UploadAsync

**Purpose**: Store an uploaded file securely
**Input**:

- `fileStream` (Stream): File content stream
- `fileName` (string): Original filename with extension
- `userId` (string): Owner user ID
- `projectId` (int?): Associated project ID or null for personal
  **Output**: `Task<string>` - Secure file path for database storage
  **Behavior**:
- Generates unique path: `{userId}/{projectId ?? "personal"}/{guid}.{ext}`
- Saves file to secure location
- Returns path for metadata storage
  **Errors**: Throws `IOException` on storage failure, `ArgumentException` on invalid input

### DownloadAsync

**Purpose**: Retrieve file content for download
**Input**: `filePath` (string): Secure file path from database
**Output**: `Task<Stream>` - File content stream
**Behavior**: Opens file stream for reading
**Errors**: Throws `FileNotFoundException` if file doesn't exist

### DeleteAsync

**Purpose**: Remove file from storage
**Input**: `filePath` (string): Secure file path
**Output**: `Task`
**Behavior**: Deletes file if exists
**Errors**: Throws `IOException` on deletion failure

### ExistsAsync

**Purpose**: Check if file exists
**Input**: `filePath` (string): Secure file path
**Output**: `Task<bool>` - True if file exists
**Behavior**: Checks file existence without opening

### GetUrl

**Purpose**: Get accessible URL for file (future cloud support)
**Input**: `filePath` (string): Secure file path
**Output**: `string` - URL or local path
**Behavior**: Returns local path for now, cloud URL later

## Implementation Notes

- Local implementation: `LocalFileStorageService` using `System.IO.File`
- Future: `AzureBlobStorageService` using Azure.Storage.Blobs
- All paths use forward slashes for cross-platform compatibility
- File extensions validated against whitelist
- Storage location outside web root for security
