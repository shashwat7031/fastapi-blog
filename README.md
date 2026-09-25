# FastAPI Blog

A full-stack blog application built with **FastAPI**, **async SQLAlchemy** and **Jinja2** templates. Users can register, log in with JWT authentication, write and edit posts, and upload a profile picture.

## Features

- User registration and login (OAuth2 password flow + JWT access tokens)
- Passwords hashed with Argon2 (`pwdlib`)
- Create, read, update and delete blog posts
- Per-user post pages and a "load more" paginated feed
- Profile picture upload, automatically cropped and resized with Pillow
- REST API under `/api` with interactive docs at `/docs`
- Server-rendered HTML pages with Bootstrap 5 and custom error pages
- Async SQLite database via `aiosqlite`

## Tech Stack

| Layer      | Tools                                   |
|------------|-----------------------------------------|
| Backend    | FastAPI, Uvicorn, Pydantic              |
| Database   | SQLAlchemy 2.0 (async), SQLite          |
| Auth       | PyJWT, pwdlib (Argon2)                  |
| Frontend   | Jinja2, Bootstrap 5, vanilla JavaScript |
| Images     | Pillow                                  |

## Project Structure

```
.
├── main.py            # App entry point, HTML page routes, error handlers
├── config.py          # Settings loaded from .env
├── database.py        # Async engine and session
├── models.py          # SQLAlchemy models (User, Post)
├── schemas.py         # Pydantic request/response schemas
├── auth.py            # Password hashing, JWT creation/verification
├── image_utils.py     # Profile picture processing
├── populate_db.py     # Seeds the database with sample users and posts
├── routers/
│   ├── users.py       # /api/users endpoints
│   └── posts.py       # /api/posts endpoints
├── templates/         # Jinja2 HTML templates
├── static/            # CSS, JS, icons, default profile picture
├── media/             # Uploaded profile pictures (not committed)
└── populate_images/   # Sample images used by populate_db.py
```

## Getting Started

### Prerequisites

- Python 3.13+
- [uv](https://docs.astral.sh/uv/) (recommended) or pip

### Installation

```bash
git clone https://github.com/shashwat7031/fastapi-blog.git
cd fastapi-blog
uv sync
```

### Configuration

Copy the example environment file and set a secret key:

```bash
cp .env.example .env
```

Generate a secure key with:

```bash
python -c "import secrets; print(secrets.token_hex(32))"
```

| Variable                      | Default   | Description                      |
|-------------------------------|-----------|----------------------------------|
| `SECRET_KEY`                  | —         | Required. Used to sign JWTs      |
| `ALGORITHM`                   | `HS256`   | JWT signing algorithm            |
| `ACCESS_TOKEN_EXPIRE_MINUTES` | `30`      | Token lifetime                   |
| `POSTS_PER_PAGE`              | `10`      | Posts shown per page             |
| `MAX_UPLOAD_SIZE_BYTES`       | `5242880` | Max profile picture size (5 MB)  |

### Run the app

```bash
uv run fastapi dev main.py
```

Then open <http://127.0.0.1:8000>. The SQLite database (`blog.db`) is created automatically on first start.

### Seed sample data (optional)

```bash
uv run python populate_db.py
```

## API Overview

Full interactive documentation is available at `/docs` (Swagger UI) and `/redoc`.

**Users** — `/api/users`

| Method | Endpoint                 | Description                 |
|--------|--------------------------|-----------------------------|
| POST   | `/api/users`             | Register a new user         |
| POST   | `/api/users/token`       | Log in and get a JWT        |
| GET    | `/api/users/me`          | Get the current user        |
| GET    | `/api/users/{id}`        | Get a user's public profile |
| GET    | `/api/users/{id}/posts`  | List a user's posts         |
| PATCH  | `/api/users/{id}`        | Update account details      |
| DELETE | `/api/users/{id}`        | Delete account              |
| PATCH  | `/api/users/{id}/picture`| Upload a profile picture    |
| DELETE | `/api/users/{id}/picture`| Remove the profile picture  |

**Posts** — `/api/posts`

| Method | Endpoint          | Description                   |
|--------|-------------------|-------------------------------|
| GET    | `/api/posts`      | List posts (`skip`, `limit`)  |
| POST   | `/api/posts`      | Create a post                 |
| GET    | `/api/posts/{id}` | Get a single post             |
| PUT    | `/api/posts/{id}` | Replace a post                |
| PATCH  | `/api/posts/{id}` | Partially update a post       |
| DELETE | `/api/posts/{id}` | Delete a post                 |

Endpoints that modify data require an `Authorization: Bearer <token>` header.

## Acknowledgements

Built while following [Corey Schafer's](https://www.youtube.com/@coreyms) FastAPI tutorial series.
