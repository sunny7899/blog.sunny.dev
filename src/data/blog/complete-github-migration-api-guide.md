---
title: Complete GitHub Migration API Guide 
author: Sunny
pubDatetime: 2026-05-07T04:06:31Z
slug: complete-github-migration-api-guide
featured: false
draft: false
tags:
  - Github
  - Migration
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

# Required Token Scopes - Roles

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

# Test it first
curl --version
health check commands
curl -s -o /dev/null -w "%{http_code}\n" https://google.com
curl -I https://google.com
curl -v https://google.com

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
  --data-raw "{\"lock_repositories\":false,\"repositories\":[\"YOURUSERNAME/REPONAME\"]}"
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
  https://api.github.com/user/migrations/{MIGRATION_ID}/archive \
  -o {ProjectName}.tar.gz
```

Extract archive:

```bash id="vwevjlwm"
tar -xvzf ssr-migration.tar.gz
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
  https://api.github.com/orgs/{ORGNAME}/migrations \
  --data-raw "{\"lock_repositories\":false,\"repositories\":["ssr"]}"
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
  https://api.github.com/orgs/{ORGNAME}/migrations
```

---

# Step 3: Download Organization Migration Archive

```bash id="y8l2nq"
curl -L \
  -H "Accept: application/vnd.github+json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  https://api.github.com/orgs/{ORGNAME}/migrations/{MIGRATION_ID}/archive \
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

```json
  {
        "id": ,
        "node_id": "",
        "owner": {
            "login": "codeforwebdevelopment",
            "id": ,
            "node_id": "",
            "avatar_url": "",
            "gravatar_id": "",
            "url": "https://api.github.com/users/codeforwebdevelopment",
            "html_url": "https://github.com/codeforwebdevelopment",
            "followers_url": "https://api.github.com/users/codeforwebdevelopment/followers",
            "following_url": "https://api.github.com/users/codeforwebdevelopment/following{/other_user}",
            "gists_url": "https://api.github.com/users/codeforwebdevelopment/gists{/gist_id}",
            "starred_url": "https://api.github.com/users/codeforwebdevelopment/starred{/owner}{/repo}",
            "subscriptions_url": "https://api.github.com/users/codeforwebdevelopment/subscriptions",
            "organizations_url": "https://api.github.com/users/codeforwebdevelopment/orgs",
            "repos_url": "https://api.github.com/users/codeforwebdevelopment/repos",
            "events_url": "https://api.github.com/users/codeforwebdevelopment/events{/privacy}",
            "received_events_url": "https://api.github.com/users/codeforwebdevelopment/received_events",
            "type": "Organization",
            "user_view_type": "public",
            "site_admin": false
        },
        "guid": "",
        "state": "exported",
        "lock_repositories": false,
        "exclude_metadata": false,
        "exclude_git_data": false,
        "exclude_attachments": false,
        "exclude_releases": false,
        "exclude_owner_projects": false,
        "org_metadata_only": false,
        "repositories": [
            {
                "id": ,
                "node_id": "",
                "name": "js30-projects",
                "full_name": "codeforwebdevelopment/js30-projects",
                "private": false,
                "owner": {
                    "login": "codeforwebdevelopment",
                    "id": ,
                    "node_id": "",
                    "avatar_url": "",
                    "gravatar_id": "",
                    "url": "https://api.github.com/users/codeforwebdevelopment",
                    "html_url": "https://github.com/codeforwebdevelopment",
                    "followers_url": "https://api.github.com/users/codeforwebdevelopment/followers",
                    "following_url": "https://api.github.com/users/codeforwebdevelopment/following{/other_user}",
                    "gists_url": "https://api.github.com/users/codeforwebdevelopment/gists{/gist_id}",
                    "starred_url": "https://api.github.com/users/codeforwebdevelopment/starred{/owner}{/repo}",
                    "subscriptions_url": "https://api.github.com/users/codeforwebdevelopment/subscriptions",
                    "organizations_url": "https://api.github.com/users/codeforwebdevelopment/orgs",
                    "repos_url": "https://api.github.com/users/codeforwebdevelopment/repos",
                    "events_url": "https://api.github.com/users/codeforwebdevelopment/events{/privacy}",
                    "received_events_url": "https://api.github.com/users/codeforwebdevelopment/received_events",
                    "type": "Organization",
                    "user_view_type": "public",
                    "site_admin": false
                },
                "html_url": "https://github.com/codeforwebdevelopment/js30-projects",
                "description": null,
                "fork": false,
                "url": "https://api.github.com/repos/codeforwebdevelopment/js30-projects",
                "forks_url": "https://api.github.com/repos/codeforwebdevelopment/js30-projects/forks",
                "keys_url": "https://api.github.com/repos/codeforwebdevelopment/js30-projects/keys{/key_id}",
                "collaborators_url": "https://api.github.com/repos/codeforwebdevelopment/js30-projects/collaborators{/collaborator}",
                "teams_url": "https://api.github.com/repos/codeforwebdevelopment/js30-projects/teams",
                "hooks_url": "https://api.github.com/repos/codeforwebdevelopment/js30-projects/hooks",
                "issue_events_url": "https://api.github.com/repos/codeforwebdevelopment/js30-projects/issues/events{/number}",
                "events_url": "https://api.github.com/repos/codeforwebdevelopment/js30-projects/events",
                "assignees_url": "https://api.github.com/repos/codeforwebdevelopment/js30-projects/assignees{/user}",
                "branches_url": "https://api.github.com/repos/codeforwebdevelopment/js30-projects/branches{/branch}",
                "tags_url": "https://api.github.com/repos/codeforwebdevelopment/js30-projects/tags",
                "blobs_url": "https://api.github.com/repos/codeforwebdevelopment/js30-projects/git/blobs{/sha}",
                "git_tags_url": "https://api.github.com/repos/codeforwebdevelopment/js30-projects/git/tags{/sha}",
                "git_refs_url": "https://api.github.com/repos/codeforwebdevelopment/js30-projects/git/refs{/sha}",
                "trees_url": "https://api.github.com/repos/codeforwebdevelopment/js30-projects/git/trees{/sha}",
                "statuses_url": "https://api.github.com/repos/codeforwebdevelopment/js30-projects/statuses/{sha}",
                "languages_url": "https://api.github.com/repos/codeforwebdevelopment/js30-projects/languages",
                "stargazers_url": "https://api.github.com/repos/codeforwebdevelopment/js30-projects/stargazers",
                "contributors_url": "https://api.github.com/repos/codeforwebdevelopment/js30-projects/contributors",
                "subscribers_url": "https://api.github.com/repos/codeforwebdevelopment/js30-projects/subscribers",
                "subscription_url": "https://api.github.com/repos/codeforwebdevelopment/js30-projects/subscription",
                "commits_url": "https://api.github.com/repos/codeforwebdevelopment/js30-projects/commits{/sha}",
                "git_commits_url": "https://api.github.com/repos/codeforwebdevelopment/js30-projects/git/commits{/sha}",
                "comments_url": "https://api.github.com/repos/codeforwebdevelopment/js30-projects/comments{/number}",
                "issue_comment_url": "https://api.github.com/repos/codeforwebdevelopment/js30-projects/issues/comments{/number}",
                "contents_url": "https://api.github.com/repos/codeforwebdevelopment/js30-projects/contents/{+path}",
                "compare_url": "https://api.github.com/repos/codeforwebdevelopment/js30-projects/compare/{base}...{head}",
                "merges_url": "https://api.github.com/repos/codeforwebdevelopment/js30-projects/merges",
                "archive_url": "https://api.github.com/repos/codeforwebdevelopment/js30-projects/{archive_format}{/ref}",
                "downloads_url": "https://api.github.com/repos/codeforwebdevelopment/js30-projects/downloads",
                "issues_url": "https://api.github.com/repos/codeforwebdevelopment/js30-projects/issues{/number}",
                "pulls_url": "https://api.github.com/repos/codeforwebdevelopment/js30-projects/pulls{/number}",
                "milestones_url": "https://api.github.com/repos/codeforwebdevelopment/js30-projects/milestones{/number}",
                "notifications_url": "https://api.github.com/repos/codeforwebdevelopment/js30-projects/notifications{?since,all,participating}",
                "labels_url": "https://api.github.com/repos/codeforwebdevelopment/js30-projects/labels{/name}",
                "releases_url": "https://api.github.com/repos/codeforwebdevelopment/js30-projects/releases{/id}",
                "deployments_url": "https://api.github.com/repos/codeforwebdevelopment/js30-projects/deployments",
                "created_at": "2021-05-13T17:36:09Z",
                "updated_at": "2025-01-10T14:18:06Z",
                "pushed_at": "2025-01-10T14:18:01Z",
                "git_url": "git://github.com/codeforwebdevelopment/js30-projects.git",
                "ssh_url": "git@github.com:codeforwebdevelopment/js30-projects.git",
                "clone_url": "https://github.com/codeforwebdevelopment/js30-projects.git",
                "svn_url": "https://github.com/codeforwebdevelopment/js30-projects",
                "homepage": "",
                "size": 46760,
                "stargazers_count": 1,
                "watchers_count": 1,
                "language": "HTML",
                "has_issues": true,
                "has_projects": true,
                "has_downloads": false,
                "has_wiki": true,
                "has_pages": false,
                "has_discussions": false,
                "forks_count": 1,
                "mirror_url": null,
                "archived": false,
                "disabled": false,
                "open_issues_count": 0,
                "license": null,
                "allow_forking": true,
                "is_template": false,
                "web_commit_signoff_required": false,
                "has_pull_requests": true,
                "pull_request_creation_policy": "all",
                "topics": [
                    "hacktoberfest"
                ],
                "visibility": "public",
                "forks": 1,
                "open_issues": 0,
                "watchers": 1,
                "default_branch": "master",
                "permissions": {
                    "admin": true,
                    "maintain": true,
                    "push": true,
                    "triage": true,
                    "pull": true
                }
            }
        ],
        "url": "",
        "created_at": "2026-05-15T23:30:31.000+05:30",
        "updated_at": "2026-05-15T23:30:49.000+05:30"
    }
```

Downloading any GitHub repository with these steps
1. Create a Personal Access Token (PAT)
2. Initiate the migration
3. Check the migration status
4. Download the migration checks

### What is the Purpose of this Archive?

A standard `git clone` only copies Git commits, branches, and code.

The GitHub Migration Archive (`.tar.gz`) is designed for complete repository migration and disaster recovery. It packages:

* **The complete Git repository:** Every branch, tag, commit, and file blob.
* **GitHub metadata:** Issues, pull requests, comments, reviews, labels, milestones, and release attachments formatted as JSON.

---

### Does it Contain the Actual Code?

**Yes.** Inside the archive, GitHub stores a bare Git directory (usually under `repositories/<org>/<repo>.git/` or `repositories/<repo>/`). Because all the Git database objects (`objects/`, `refs/`, `HEAD`) are included, the full source code across all historical revisions is present.

---

### How to Check Commits and Lines of Code

Extract the archive, navigate to the bare Git folder, and use Git tools to calculate metrics:

#### 1. Extract the Archive

```bash
mkdir migration_data
tar -zxvf migration_archive.tar.gz -C migration_data

```

Find the `.git` directory inside:

```bash
find migration_data -type d -name "*.git"

```

*(Suppose the directory found is `migration_data/repositories/codeforwebdevelopment/ml-nlp-js.git`)*

---

#### 2. Count the Commits

You can query the bare repository directly using the `--git-dir` flag without even checking out the working tree:

* **Count commits on the default branch (e.g., `main` or `master`):**
```bash
git --git-dir="migration_data/repositories/codeforwebdevelopment/ml-nlp-js.git" rev-list --count HEAD

```


* **Count all commits across all branches and tags:**
```bash
git --git-dir="migration_data/repositories/codeforwebdevelopment/ml-nlp-js.git" rev-list --count --all

```



---

#### 3. Restore the Working Tree and Count Lines of Code

To count lines of code, turn the bare Git directory into a working directory with checked-out source files:

1. **Clone it locally to get the files:**
```bash
git clone "migration_data/repositories/codeforwebdevelopment/ml-nlp-js.git" ml-nlp-js-code
cd ml-nlp-js-code

```


2. **Count lines of code:**
* **Using `cloc` (Recommended — ignores generated files/minified code):**
```bash
# Install if needed: brew install cloc (macOS) or sudo apt install cloc (Linux)
cloc .

```


* **Using built-in shell commands:**
```bash
git ls-files | xargs wc -l

```

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


```
https://github.com/ username/repository_name id migrations 
  
  curl -L -X POST \
  -H "Accept: application/vnd.github+json" \
  -H "Authorization: Bearer token" \
  -H "Content-Type: application/json" \
  -H "X-GitHub-Api-Version: 2026-03-10" \
  https://api.github.com/orgs/angulardevelopment/migrations \
  -d '{"lock_repositories": false, "repositories": ["agile-board"]}'

    curl -L \
  -H "Accept: application/vnd.github+json" \
  -H "Authorization: Bearer token" \
  https://api.github.com/orgs/angulardevelopment/migrations/id
  
curl -L \
  -H "Authorization: Bearer token" \
  -H "Accept: application/vnd.github+json" \
  -H "X-GitHub-Api-Version: 2022-11-28" \
  https://api.github.com/orgs/angulardevelopment/migrations/id/archive \
  -o / agile-board.tar.gz

  curl -L -X POST \
  -H "Accept: application/vnd.github+json" \
  -H "Authorization: Bearer token" \
  -H "Content-Type: application/json" \
  -H "X-GitHub-Api-Version: 2022-11-28" \
  https://api.github.com/user/migrations \
  -d '{"lock_repositories":false,"repositories":["sunny7899/book-store"]}'
  
  curl -L \
  -H "Accept: application/vnd.github+json" \
  -H "Authorization: Bearer token" \
  https://api.github.com/user/migrations/id
  
  curl -L \
  -H "Accept: application/vnd.github+json" \
  -H "Authorization: Bearer token" \
  https://api.github.com/user/migrations/id/archive \
  -o book-store.tar.gz
  
  curl --request POST --header "PRIVATE-TOKEN: token"
"https://gitlab.com/api/v4/projects/username%2Frepository_name/export"

curl --request GET --header "PRIVATE-TOKEN: token"
"https://gitlab.com/api/v4/projects/username%2Frepository_name/export"

curl --request GET --header "PRIVATE-TOKEN: token" -o
airtable.tar.gz
"https://gitlab.com/api/v4/projects/username%2Frepository_name/export/download"

```