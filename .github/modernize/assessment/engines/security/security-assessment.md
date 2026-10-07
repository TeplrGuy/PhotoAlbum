# Security Assessment Report

**Generated:** 2026-10-07T14:12:32.146598Z

## Summary

| Metric | Count |
|--------|-------|
| Total Findings | 6 |
| CVE Vulnerabilities | 0 |
| CWE Vulnerabilities | 6 |
| Total Rules Assessed | 59 |
| Rules Passed | 53 |

### By Severity

| Severity | Count |
|----------|-------|
| mandatory | 0 |
| optional | 2 |
| potential | 4 |

## CVE Findings (Dependency Vulnerabilities)
No CVE findings at the configured minimum severity threshold (low).

## CWE Findings (Code-Level Vulnerabilities)
### CWE-606: Unchecked Input for Loop Condition
- **Category:** Code Quality
- **Severity:** potential
- **Story Points:** 3
- **Files:** PhotoAlbum/Pages/Index.cshtml.cs

IndexModel.OnPostUploadAsync iterates the request-controlled files collection in an unbounded foreach loop (line 63). The configured MaxFilesPerUpload value is not enforced in this handler, so an upload request can cause processing of every submitted file.

### CWE-1057: Data Access Operations Outside of Expected Data Manager Component
- **Category:** Code Quality
- **Severity:** potential
- **Story Points:** 5
- **Files:** PhotoAlbum/Pages/PhotoFile.cshtml.cs, PhotoAlbum/Services/PhotoService.cs

PhotoFileModel.OnGetAsync retrieves metadata through IPhotoService but directly reads image content with System.IO.File.ReadAllBytesAsync (line 67). File persistence is otherwise handled by PhotoService, so the read bypasses the application's central photo service/data manager.

### CWE-820: Missing Synchronization
- **Category:** Concurrency & Synchronization
- **Severity:** potential
- **Story Points:** 8
- **Files:** PhotoAlbum/Pages/PhotoFile.cshtml.cs, PhotoAlbum/Services/PhotoService.cs

PhotoFileModel.OnGetAsync checks File.Exists and then reads the file in a separate operation (lines 61-67), while PhotoService.DeletePhotoAsync can delete that same photo file concurrently (lines 238-245). No synchronization protects this shared file; deletion between the check and read can turn a valid photo request into an exception and HTTP 500.

### CWE-259: Use of Hard-coded Password
- **Category:** Credentials & Secrets
- **Severity:** optional
- **Story Points:** 5
- **Files:** infra/main.bicep

The SQL administrator password expression in infra/main.bicep:59 embeds a fixed password prefix and appends a deterministic uniqueString expression, making the credential partly hard-coded.

### CWE-778: Insufficient Logging
- **Category:** Credentials & Secrets
- **Severity:** potential
- **Story Points:** 3
- **Files:** PhotoAlbum/Pages/Login.cshtml.cs

LoginModel.OnPostAsync rejects invalid administrator credentials and returns an error at lines 52-57 without logging the failed authentication event; the page model has no logger dependency.

### CWE-798: Use of Hard-coded Credentials
- **Category:** Credentials & Secrets
- **Severity:** optional
- **Story Points:** 5
- **Files:** infra/main.bicep

The SQL administrator credential in infra/main.bicep:59 contains a fixed password prefix combined with a deterministic uniqueString expression rather than a fully externally supplied credential.
