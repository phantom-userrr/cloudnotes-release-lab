# Release Notes — CloudNotes Release Lab

## Task 1 — Terraform/HCL validation fault

**Original error (paste `terraform validate` output):**

```text
Error: Reference to undeclared input variable "bucket_name_old".
```

**Root cause:**

`main.tf` referenced an undeclared input variable.

**Fix applied:**

Replaced `var.bucket_name_old` with `var.bucket_name` in `terraform/main.tf`.

**Evidence (clean validate output):**

```text
Success! The configuration is valid.
```

---

## Task 2 — Reusable module + state isolation fault

**Original problem:**

The root module duplicated the storage resource, referenced an outdated output, and shared the same state path between dev and staging.

**Fix applied:**

Replaced the duplicate resource with `module.storage`, updated the output to `module.storage.bucket_path`, and assigned staging its own state file.

**Evidence:**

```text
Success! The configuration is valid.
```

---

## Task 3 — Container build + secret fault

**Original error (paste `docker build` output):**

```text
failed to resolve source metadata for docker.io/library/build:latest
```

**Root cause:**

The Dockerfile referenced a nonexistent `build` stage and contained a hardcoded API key.

**Fix applied:**

Changed `COPY --from=build` to `COPY --from=deps` and removed the hardcoded API key from the Dockerfile.

**Evidence (`curl http://localhost:8080/health`):**

```text
HTTP/1.1 200 OK
{"status":"ok","service":"cloudnotes-api","version":"0.1.0","apiKeyConfigured":true}
```