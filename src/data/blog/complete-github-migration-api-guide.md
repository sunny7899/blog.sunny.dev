---
title: Complete GitHub Migration API Guide 
author: Sunny
pubDatetime: 2026-05-16T04:06:31Z
slug: complete-github-migration-api-guide
featured: false
draft: false
tags:
  - Github
description:
  Personal & Organization Repository Migration Using REST APIs
---

Migrating repositories between GitHub accounts, organizations, or platforms can become challenging when dealing with multiple repositories, backups, enterprise compliance, or automated DevOps workflows.

GitHub provides powerful REST APIs for exporting repositories as migration archives. However, developers often face issues with:

* authentication
* organization access
* SSO authorization
* migration IDs
* archive downloads
* Git Bash JSON formatting
* incorrect endpoints

This guide covers the complete migration workflow, best practices, troubleshooting, and API usage for both personal and organization repositories.

---

# What is GitHub Migration API?

GitHub Migration APIs allow you to:

* export repositories
* migrate repositories between GitHub instances
* create backups
* automate repository archival
* migrate organization repositories
* download migration archives

GitHub supports:

1. User migrations
2. Organization migrations

Official Docs:
[GitHub Migration APIs Documentation](https://docs.github.com/en/rest/migrations)

---

# Types of GitHub Migrations

| Migration Type                | Endpoint                 |
| ----------------------------- | ------------------------ |
| Personal account repositories | `/user/migrations`       |
| Organization repositories     | `/orgs/{org}/migrations` |

This distinction is critical.

Many developers accidentally use:

```bash id="p1b5zc"
/user/migrations
```

for organization repositories and receive:

```json id="zobwy5"
422 Validation Failed
```

---

# Prerequisites

Before starting migrations, ensure you have:

## 1. Personal Access Token (PAT)

GitHub migration APIs work best with a Classic PAT.

Create one here:

[GitHub Classic Personal Access Token Settings](https://github.com/settings/tokens/new)

---

# Required Token Scopes

## Personal Repository Migration

Required scopes:

* `repo`

## Organization Repository Migration

Required scopes:

* `repo`
* `read:org`
* `admin:org`

---

# Important: SSO Authorization

If your organization uses SAML SSO, your token must be authorized for the organization.

Go to:

[GitHub Token Settings](https://github.com/settings/tokens)

Then:

* Select your token
* Click `Configure SSO`
* Authorize your organization

Without this step, GitHub APIs may return:

* `404 Not Found`
* `422 Validation Failed`
* repository access failures

Official SSO Docs:
[GitHub SSO Token Authorization Docs](https://docs.github.com/en/authentication/authenticating-with-saml-single-sign-on/authorizing-a-personal-access-token-for-use-with-saml-single-sign-on)

---

# Personal Repository Migration

## Step 1: Start Migration

Example repository:

```text id="83b9fz"
sunny7899/FastAPI-CRUD
```

Command:

```bash id="b5zvpb"
curl -L -X POST \
  -H "Accept: application/vnd.github+json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "X-GitHub-Api-Version: 2022-11-28" \
  https://api.github.com/user/migrations \
  --data-raw "{\"lock_repositories\":false,\"repositories\":[\"sunny7899/FastAPI-CRUD\"]}"
```

---

# Common Git Bash Mistake

Incorrect:

```bash id="vz94cf"
-H
"Authorization: Bearer TOKEN"
```

This causes:

```text id="3v1yb2"
curl: option -H: requires parameter
```

Always:

* keep headers on one line
* or use `\` for continuation

---

# Step 2: Check Migration Status

List migrations:

```bash id="m8ntsq"
curl -L \
  -H "Accept: application/vnd.github+json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  https://api.github.com/user/migrations
```

Response:

```json id="zbcv1n"
[
  {
    "id": 12286999,
    "state": "exported"
  }
]
```

---

# Important: Migration ID vs Node ID

This is invalid:

```text id="h2ptuo"
LM_kwDOAOdyCc4Au3y2
```

That is a GraphQL Node ID.

Migration APIs require numeric IDs:

```text id="9k6a4s"
12286999
```

---

# Step 3: Download Migration Archive

```bash id="ot51ea"
curl -L \
  -H "Accept: application/vnd.github+json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  https://api.github.com/user/migrations/12286999/archive \
  -o fastapi-crud.tar.gz
```

Extract archive:

```bash id="vwevjlwm"
tar -xvzf fastapi-crud.tar.gz
```

---

# Organization Repository Migration

Organization migrations use a completely different endpoint.

Repository example:

```text id="hzg8uy"
angulardevelopment/ssr
```

---

# Step 1: Create Organization Migration

```bash id="4e4s7d"
curl -L -X POST \
  -H "Accept: application/vnd.github+json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "X-GitHub-Api-Version: 2022-11-28" \
  https://api.github.com/orgs/angulardevelopment/migrations \
  --data-raw "{\"lock_repositories\":false,\"repositories\":[\"angulardevelopment/ssr\"]}"
```

---

# Why `/user/migrations` Fails for Organization Repositories

Using:

```text id="twjlwm"
/user/migrations
```

with organization repositories often returns:

```json id="dr77ow"
{
  "message": "Validation Failed"
}
```

because organization repositories must use:

```text id="gk0lvq"
/orgs/{org}/migrations
```

---

# Step 2: List Organization Migrations

```bash id="u94a1z"
curl -L \
  -H "Accept: application/vnd.github+json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  https://api.github.com/orgs/angulardevelopment/migrations
```

---

# Step 3: Download Organization Migration Archive

```bash id="y8l2nq"
curl -L \
  -H "Accept: application/vnd.github+json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  https://api.github.com/orgs/angulardevelopment/migrations/123456/archive \
  -o ssr-migration.tar.gz
```

---

# Downloading Archives Using Postman

In Postman:

1. Create GET request
2. Add migration archive URL
3. Add headers
4. Click dropdown beside `Send`
5. Select `Send and Download`

This downloads the `.tar.gz` archive directly.

---

# Common Migration Errors & Fixes

## 1. 404 Not Found

Cause:

* wrong migration ID
* using Node ID instead of numeric ID

Fix:

* use numeric migration ID

---

## 2. 422 Validation Failed

Cause:

* wrong endpoint
* repo inaccessible
* organization authorization missing

Fix:

* use `/orgs/{org}/migrations`
* authorize SSO
* ensure admin access

---

## 3. Problems Parsing JSON

Cause:

* malformed Git Bash JSON

Fix:
Use:

```bash id="kxd92n"
--data-raw "{\"key\":\"value\"}"
```

instead of multiline single-quoted JSON.

---

# Best Practices for GitHub Migrations

## Use Classic PATs

Migration APIs behave more consistently with classic tokens.

---

## Always Verify Repo Access First

Test:

```bash id="tx9jlwm"
curl -H "Authorization: Bearer TOKEN" \
https://api.github.com/repos/OWNER/REPO
```

before starting migrations.

---

## Avoid Repository Locking Unless Necessary

```json id="hpt7ea"
"lock_repositories": false
```

prevents downtime during exports.

---

## Monitor Migration State

Possible states:

* pending
* exporting
* exported
* failed

Download archives only when:

```json id="3tgnxw"
"state": "exported"
```

---

# Security Recommendations

Never:

* expose PATs publicly
* commit tokens to Git repositories
* share migration archives externally

Use:

* GitHub secrets
* environment variables
* CI/CD secret managers

---

# Automating GitHub Backups

Migration APIs are excellent for:

* scheduled backups
* disaster recovery
* compliance exports
* enterprise archival
* repository replication

You can automate exports using:

* GitHub Actions
* cron jobs
* Jenkins
* Azure DevOps
* shell scripts

---

# Final Thoughts

GitHub Migration APIs are extremely powerful but can be confusing because:

* personal and organization migrations use different endpoints
* SSO authorization is often required
* numeric migration IDs are mandatory
* Git Bash formatting can break requests

Once configured correctly, the APIs provide a reliable way to automate repository exports, backups, and enterprise migrations.

---

# Useful References

* [GitHub Migration APIs](https://docs.github.com/en/rest/migrations)
* [GitHub Organization Migration APIs](https://docs.github.com/en/rest/migrations/orgs)
* [GitHub User Migration APIs](https://docs.github.com/en/rest/migrations/users)
* [GitHub Personal Access Tokens](https://github.com/settings/tokens)
* [GitHub SSO Authorization Docs](https://docs.github.com/en/authentication/authenticating-with-saml-single-sign-on)


