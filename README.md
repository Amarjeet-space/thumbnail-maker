# Thumbnail Maker

A simple photo/video sharing app. FastAPI backend with JWT authentication, Streamlit frontend, SQLite for data, and ImageKit as the media CDN.

## Features

- Email/password sign up and login (JWT-based)
- Upload images and videos with a caption
- Chronological feed of all posts
- Delete your own posts
- Media served and transformed via ImageKit

## Tech Stack

| Layer | Technology |
|---|---|
| Backend | FastAPI, fastapi-users, SQLAlchemy (async), aiosqlite |
| Frontend | Streamlit |
| Media storage | ImageKit |
| Database | SQLite (`test.db`, auto-created) |

## Project Structure

```
thumbnail-maker/
├── main.py            # Starts the FastAPI backend (uvicorn)
├── frontend.py         # Streamlit UI
├── requirements.txt
├── .env                # ImageKit credentials (not committed)
└── app/
    ├── app.py          # Routes: /upload, /feed, /posts/{id}, auth routers
    ├── db.py           # SQLAlchemy models (User, Post) + async session
    ├── users.py        # fastapi-users JWT auth backend
    ├── schemas.py       # Pydantic schemas for user read/create/update
    └── img.py           # ImageKit client setup
```

## Setup

**1. Requires Python 3.13+**

**2. Create a virtual environment and install dependencies:**
```bash
python -m venv venv
venv\Scripts\activate        # Windows
source venv/bin/activate     # Mac/Linux

pip install -r requirements.txt
```

**3. Configure ImageKit:**

Sign up free at [imagekit.io](https://imagekit.io), then create a `.env` file in the project root:
```
IMAGEKIT_PRIVATE_KEY=your_private_key_here
IMAGEKIT_PUBLIC_KEY=your_public_key_here
IMAGEKIT_URL=https://ik.imagekit.io/your_imagekit_id
```

## Running

Run the backend and frontend in two separate terminals, both from the project root.

**Terminal 1 — backend (port 8000):**
```bash
python main.py
```

**Terminal 2 — frontend (port 8501):**
```bash
streamlit run frontend.py
```

Open `http://localhost:8501` in your browser. Sign up with any email/password, then log in to upload and view posts.

## API Endpoints

| Method | Endpoint | Description |
|---|---|---|
| POST | `/auth/register` | Create an account |
| POST | `/auth/jwt/login` | Log in, returns a JWT |
| GET | `/users/me` | Get current user info |
| POST | `/upload` | Upload media + caption (auth required) |
| GET | `/feed` | List all posts, newest first (auth required) |
| DELETE | `/posts/{post_id}` | Delete your own post (auth required) |

Full interactive docs available at `http://localhost:8000/docs` once the backend is running.

## Notes

- **Database resets:** `test.db` is created automatically on first run and is *not* migrated automatically. If you change any model in `app/db.py`, delete `test.db` and restart the backend to regenerate it with the new schema — otherwise you'll hit "no such column" errors.
- **JWT secret:** `SECRET` in `app/users.py` is a hardcoded placeholder for local development. Change it to a securely generated value before deploying anywhere public.
- **Token lifetime:** JWTs expire after 1 hour (`lifetime_seconds=3600` in `app/users.py`). Log in again if you get a 401 after leaving the app idle.