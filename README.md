# 🌱 Greenhouse Development Environment

This repo (`dot-env`) contains the **single source of truth** `docker-compose.yml` and a template `.env.example` for running the complete Greenhouse stack (PostgreSQL + ASP.NET 9 API + React/Vite front-end) locally with **one command**.

---

## Contents

```
.
├─ docker-compose.yml   # the stack definition
├─ .env.example         # copy → .env to override anything
└─ README.md            # you’re here
```

## Prerequisites

* Docker Desktop ≥ 20.10 (Compose v2 enabled)  
* Git

---

## Folder layout

Clone this repo next to each application repo so the relative build contexts resolve:

```
~/code/
├─ greenhouse-dev       # ← this repo (dot-env)
├─ greenhouse-api       # ASP.NET Core back-end
└─ greenhouse-web       # React front-end
```

> **Tip**  Use any folder names you like—the only requirement is that `greenhouse-dev` can reach its sibling folders via `../api` and `../web`.

---

## Quick start

```bash
# 1  Clone the three repos
git clone https://github.com/my-org/greenhouse-dev.git
git clone https://github.com/my-org/greenhouse-api.git
git clone https://github.com/my-org/greenhouse-web.git

# 2  Create your personal .env with any overrides
cd greenhouse-dev
cp .env.example .env        # tweak ports/secrets if you need to

# 3  Launch the full stack
docker compose up --build   # first build can take a minute
```

| Service | URL (defaults)                                                                       |
| ------- | ------------------------------------------------------------------------------------ |
| API     | http://localhost:`$API_HOST_PORT` → **8080**                                       |
| Web     | http://localhost:`$WEB_HOST_PORT` → **3000**                                       |

Stop everything with **Ctrl-C**, or run:

```bash
docker compose down -v     # also deletes the pgdata volume
```

---

## Environment variables

All overridable settings live in **`.env`**. Safe defaults mean you can ignore the file unless you need changes.

| Variable                      | Default                            | Purpose                     |
| ----------------------------- | ---------------------------------- | --------------------------- |
| `POSTGRES_DB`                 | greenhouse                         | Database name               |
| `POSTGRES_USER`               | postgres                           | DB user                     |
| `POSTGRES_PASSWORD`           | postgres                           | DB password                 |
| `API_HOST_PORT`               | 8080                               | Host port → API 8080        |
| `WEB_HOST_PORT`               | 3000                               | Host port → Nginx 80        |
| `JWT__ValidIssuer`            | GreenhouseApi                      | JWT issuer                  |
| `JWT__ValidAudience`          | GreenhouseApiUsers                 | JWT audience                |
| `JWT__Secret`                 | CHANGE_ME_FOR_PROD                 | Signing secret              |
| `WorkerCredentials__Username` | ml-worker                          | Background-job account      |
| `WorkerCredentials__Password` | Super!Secure!PW                    | Background-job password     |
| `VITE_API_BASE_URL`           | http://api:8080                    | Embedded in front-end build |

> See `.env.example` for the complete list.

---

## Common commands

| Command                                                      | Description                                  |
| ------------------------------------------------------------ | -------------------------------------------- |
| `docker compose up --build`                                  | Build images (if needed) and start the stack |
| `docker compose logs -f api`                                 | Tail API logs                                |
| `docker compose exec db psql -U $POSTGRES_USER $POSTGRES_DB` | Open Postgres shell                          |
| `docker compose down -v`                                     | Tear down containers *and* delete volumes    |

<summary>How do I run tests in CI?</summary>
CI pipelines typically check out this repo, set a custom `.env`, then call `docker compose --profile ci up -d` to run headless.
</details>

