# Security Assessment Report

**Generated:** 2026-10-07T01:34:44.637524Z

## Summary

| Metric | Count |
|---|---|
| Total Findings | 3 |
| CVE Vulnerabilities | 0 |
| CWE Vulnerabilities | 3 |
| Total Rules Assessed | 59 |
| Rules Passed | 56 |

### By Severity

| Severity | Count |
|---|---|
| mandatory | 0 |
| optional | 1 |
| potential | 2 |

## CVE Findings (Dependency Vulnerabilities)

No matching advisories were returned for 119 resolved NuGet package/version pairs, including transitive and test dependencies. Four GitHub Advisory API batches completed successfully with pagination handling. Minimum included CVE severity: **high**. This does not establish that dependencies or the runtime are free of vulnerabilities.

## CWE Findings (Code-Level Vulnerabilities)

### CWE-606: Unchecked Input for Loop Condition
- **Category:** Code Quality
- **Severity:** potential
- **Story Points:** 3
- **Files:** PhotoAlbum/Pages/Index.cshtml.cs, PhotoAlbum/Services/PhotoService.cs, PhotoAlbum/appsettings.json, PhotoAlbum/Program.cs

IndexModel.OnPostUploadAsync accepts an anonymous multipart POST to /Index?handler=Upload (lines 53-58), checks only whether the file collection is empty, and iterates every supplied file (lines 63-94) without enforcing FileUpload:MaxFilesPerUpload=10 (appsettings.json line 13). A caller can obtain the normal antiforgery token from the public gallery and submit, for example, 100 small valid PNG files within the framework request and form-binding limits. Every successful iteration uploads and saves a file and database row, then calls GetAllPhotosAsync (Index.cshtml.cs lines 65-70), which sorts and materializes the entire Photos table (PhotoService.cs lines 43-49). Thus attacker-controlled file count drives repeated expensive database reads with work proportional to the batch count times the existing gallery size, plus growth during the batch. Program.cs lines 29-34 and PhotoService.cs lines 89-105 constrain multipart section/file byte sizes, not this configured batch count or the repeated full-gallery work. The loop is finite and framework-bounded, but its application-level input count remains unchecked and permits excessive processing beyond the declared batch limit.

### CWE-778: Insufficient Logging
- **Category:** Credentials & Secrets
- **Severity:** potential
- **Story Points:** 3
- **Files:** PhotoAlbum/Pages/Login.cshtml.cs, PhotoAlbum/appsettings.json

LoginModel.OnPostAsync compares the submitted username and password at lines 52-54, then returns the login page for invalid credentials at lines 55-57 without recording an authentication-failure event. LoginModel has no ILogger or other audit-event mechanism (lines 18-23); neither the rejected username nor the failure outcome is logged. Consequently repeated invalid admin-login attempts cannot be identified from application authentication audit events. PhotoAlbum/appsettings.json:16-20 sets Microsoft.AspNetCore logging to Warning; ordinary framework request diagnostics do not provide a credential-validation failure event for this custom handler. Credential values are omitted.

### CWE-79: Improper Neutralization of Input During Web Page Generation ('Cross-site Scripting')
- **Category:** Injection Attacks
- **Severity:** optional
- **Story Points:** 8
- **Files:** PhotoAlbum/wwwroot/js/upload.js, PhotoAlbum/Pages/Index.cshtml

First confirmed match: DOM-based cross-site scripting in handleFiles()/showErrors(). The file-input change and drop handlers (upload.js:49-58) pass user-selected files to handleFiles(). A rejected file's attacker-crafted file.name is interpolated into an error at line 73 (also line 75), passed to showErrors() at lines 81-82, and inserted without HTML encoding through innerHTML at lines 224-226. A filename containing an img element with an onerror handler is interpreted as HTML and can execute JavaScript in the application's origin, even though the enclosing feedback element starts hidden (Index.cshtml:32-40). Exploitation is interaction-dependent: a victim must obtain and select or drop an attacker-crafted filename on a filesystem that permits the markup; an attacker merely uploading a filename does not automatically execute script for other gallery visitors. This is not confirmed automatic gallery stored XSS: the ordinary gallery GET uses Razor-encoded OriginalFileName expressions (Index.cshtml:66-70). Only this first confirmed filename-to-error-HTML match is recorded for CWE-79.

## Scope and Limitations

The six requested CWE checklists cover 59 rules. NOT_FOUND means no confirmed match in the reviewed source, not proof of absence. Assessment severity labels are planning priorities, not CVSS ratings. The complete checklists and dependency result are preserved in the report security directory. No remediation or exploitation tests were performed.

Additional specialist observations outside these checklists: the SQL administrator credential is deterministically derived from public deployment metadata (`infra/main.bicep:59,67-68`, CWE-330), and the provisioning script prints its generated SQL administrator credential (`azure-setup.sh:200`, CWE-532). These are not counted as requested-checklist findings. No secret values are reproduced.
