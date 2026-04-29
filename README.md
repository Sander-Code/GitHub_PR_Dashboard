# GitHub PR Dashboard

A lightweight, single-file HTML dashboard for keeping track of your GitHub pull requests — no build tools, no dependencies, no server required.

## Features

**My PRs tab**
- Lists all your open pull requests across all repositories
- Shows how long ago a PR was opened and when the last activity occurred (highlighted in red if older than 2 weeks)
- Displays reviewer count and approval count
- Shows which branch protection rules are blocking a merge (required approvals, code-owner review, stale review policy, etc.)

**Review requests tab**
- Lists all PRs you have been invited to review
- Direct link to each PR on GitHub
- Shows reviewer and approval counts
- Displays all CI check results per PR (passing, failing, pending, skipped)

**General**
- Colour-coded repo labels — PRs from the same repo share a colour, making it easy to scan across repositories at a glance
- Filter and search bar (searches title, repo, and author) with a repo dropdown and sort options (newest, oldest, last activity)
- Light and dark theme with OS preference detection — your choice is saved in `localStorage`
- All data is fetched directly from the GitHub API — your token is never stored or sent anywhere other than `api.github.com`

## Getting started

### 1. Download the file

Save `index.html` from this repository to your machine.

### 2. Create a GitHub token

The dashboard supports both **classic** and **fine-grained** personal access tokens. Fine-grained tokens are recommended — they let you limit access to specific repositories and permissions.

#### Option A — Fine-grained token (recommended)

1. Go to [github.com](https://github.com) → **Settings** → **Developer settings** → **Personal access tokens** → **Fine-grained tokens**
2. Click **Generate new token**
3. Give it a name (e.g. `PR Dashboard`) and set an expiration
4. Under **Repository access**, choose **All repositories** or select specific ones
5. Under **Permissions**, grant the following **Repository permissions**:

| Permission | Access | Why |
|---|---|---|
| Pull requests | Read-only | List PRs, reviews, and review requests |
| Checks | Read-only | CI check results |
| Administration | Read-only | Branch protection rules |
| Metadata | Read-only | Required by GitHub for all fine-grained tokens |

6. Click **Generate token**, copy it, and select **Fine-grained** in the dashboard dropdown

#### Option B — Classic token

1. Go to **Settings** → **Developer settings** → **Personal access tokens** → **Tokens (classic)**
2. Click **Generate new token (classic)**
3. Give it a name and select the `repo` scope (or `public_repo` for public repositories only)
4. Click **Generate token** and copy it — leave the dropdown on **Classic**

> ⚠️ Treat your token like a password. Do not commit it or share it with others.

### 3. Open the dashboard

Double-click `index.html` — it opens directly in your browser. Paste your token into the input field and click **Load**.

No internet connection beyond the GitHub API is required. No frameworks, no npm, no build step.

## Requirements

- A modern browser (Chrome, Firefox, Safari, Edge)
- A GitHub Personal Access Token with the `repo` scope

## How it works

The dashboard uses the [GitHub REST API](https://docs.github.com/en/rest) directly from the browser:

| Data | Endpoint |
|---|---|
| Authenticated user | `GET /user` |
| Your open PRs | `GET /search/issues` |
| Review requests | `GET /search/issues` |
| Reviews on a PR | `GET /repos/{owner}/{repo}/pulls/{number}/reviews` |
| PR details (branch name, SHA) | `GET /repos/{owner}/{repo}/pulls/{number}` |
| Branch protection rules | `GET /repos/{owner}/{repo}/branches/{branch}/protection` |
| CI check runs | `GET /repos/{owner}/{repo}/commits/{sha}/check-runs` |

All filtering, sorting, and searching happens client-side after the initial fetch — no additional API calls are made when you change filters.

## Limitations

- The GitHub Search API returns a maximum of 50 results per query. If you have more than 50 open PRs or review requests, only the 50 most recently updated will appear.
- Branch protection details require the token owner to have at least write access to the repository; otherwise the protection endpoint returns a 404 and no blockers will be shown.
- CI check results are only available for repositories where checks are configured.

## Privacy

Your token is held in memory only for the duration of the session and is sent exclusively to `api.github.com`. It is never logged, stored in `localStorage`, or transmitted anywhere else.
