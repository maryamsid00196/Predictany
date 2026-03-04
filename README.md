# Predictany

A Django app that receives real-time YouTube video notifications via PubSubHubbub and stores video metadata in a database.

## Features

- **YouTube PubSubHubbub integration** – Subscribe to a YouTube channel’s video feed and receive webhook notifications when new videos are published
- **Video storage** – Saves video ID, channel ID, and video link (and related fields) to the database
- **Callback endpoint** – Handles incoming Atom feed notifications from YouTube’s hub and parses them with `feedparser`

## Tech Stack

- **Django 4.2**
- **PostgreSQL** (configurable via environment)
- **Python packages:** `feedparser`, `requests`, `python-decouple`, Django REST framework

## Prerequisites

- Python 3.x
- PostgreSQL (or use SQLite for local dev by adjusting `settings.py`)
- A publicly reachable URL for the callback (e.g. ngrok for local testing)

## Setup

1. **Clone and enter the project**
   ```bash
   cd predictany
   ```

2. **Create and activate a virtual environment**
   ```bash
   python -m venv venv
   venv\Scripts\activate   # Windows
   # source venv/bin/activate   # macOS/Linux
   ```

3. **Install dependencies**
   ```bash
   pip install django djangorestframework feedparser requests python-decouple psycopg2-binary
   ```

4. **Environment variables** (optional; defaults used if not set)
   Create a `.env` in the project root or set:
   - `DATABASE_NAME` (default: `postgres`)
   - `DATABASE_USER` (default: `postgres`)
   - `DATABASE_PASSWORD` (default: `postgres`)
   - `DATABASE_HOST` (default: `localhost`)
   - `DATABASE_PORT` (default: `5432`)

5. **Run migrations**
   ```bash
   python manage.py migrate
   ```

6. **Run the development server**
   ```bash
   python manage.py runserver
   ```

## Project structure

- **`predictany/`** – Django project (settings, URLs, WSGI/ASGI)
- **`predictanyy/`** – App containing:
  - `YouTubeVideo` model (video_id, channel_id, title, video_link, channel_title, published_at, updated_at)
  - `subscription_callback` – POST endpoint that receives PubSubHubbub notifications and saves videos
  - `subscribe_to_notifications` – Helper to subscribe to a channel’s feed (callback and topic URLs are configured in code)

## Endpoints

| Path        | Method | Description                                      |
|------------|--------|--------------------------------------------------|
| `/admin/`  | GET    | Django admin                                     |
| `/callback/` | POST | PubSubHubbub webhook; receives feed, parses and saves videos |

## Subscribing to a channel

Subscription is done by calling the hub with the right callback URL and topic. The callback URL must be publicly accessible (e.g. via ngrok). Update the callback URL and channel ID in `predictanyy/views.py` in `subscribe_to_notifications` (and in `CSRF_TRUSTED_ORIGINS` in `settings.py` if you use a custom domain/ngrok URL) before subscribing.

## License

Use and modify as needed for your project.
