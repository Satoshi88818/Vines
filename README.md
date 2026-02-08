
# MusicVines Pro V2.2 – Grok Imagine Edition

**AI-powered short music video generator**  
Create vertical, reactive, cinematic 9:16 music clips using user-provided audio + AI-generated or uploaded video base — enhanced with waveform visualization, parallax zoom, smart text overlays, and viral caption/hashtag suggestions powered by Grok-4.

**Status**: Proof-of-concept / Early production (v2.2 – Feb 2026 improvements)

## ✨ Key Features

- **Audio processing**  
  - Peak normalization (safe for silent/low-volume clips)  
  - Smart looping or trimming to match video duration  
  - Gentle fade-in / fade-out (1 second)

- **Video enhancement pipeline** (MoviePy)  
  - Subtle parallax zoom effect  
  - Custom **reactive waveform overlay** (matplotlib + dynamic window sampling)  
  - Clean title + caption overlays with stroke for readability  
  - 1080×1920 vertical output (Reels / TikTok / Shorts ready)

- **AI Integration (xAI Grok ecosystem)**  
  - Grok-4 keyframe analysis → optimized caption + 8–12 targeted hashtags + 3 viral reasons  
  - Optional Grok Imagine image fallback background (if no base video)  
  - **Future** Grok text-to-video hook (placeholder – not publicly available yet)

- **Production-grade backend**  
  - Asynchronous task processing (Celery + Redis)  
  - Rate limiting (Flask-Limiter)  
  - File upload endpoints (audio + base video)  
  - CORS, CSRF protection, secure filename handling  
  - Progress tracking via task status endpoint

- **Legal & ethical stance**  
  Uses **only user-provided audio** — no music generation or scraping

## Requirements

### Minimum

- Python 3.10–3.12
- ≥ 8 GB RAM (MoviePy + matplotlib can be memory hungry)
- Redis server (for Celery & caching)

### Python Dependencies (recommended `requirements.txt`)

```text
flask==3.0.*
flask-limiter==3.8.*
flask-caching==2.3.*
flask-cors==5.0.*
flask-wtf==1.2.*
celery[redis]==5.4.*
redis==5.0.*
moviepy==1.0.3
Pillow==10.*
numpy==1.*
matplotlib==3.9.*
requests==2.32.*
python-dotenv==1.0.*
werkzeug==3.0.*
```

> **Note**: `moviepy` 1.0.3 is still the most stable version for many users in 2025–2026. Newer versions may require imageio-ffmpeg tweaks.

### System Dependencies (Ubuntu/Debian example)

```bash
sudo apt update
sudo apt install -y \
    python3-dev \
    ffmpeg \
    libsm6 libxext6 libxrender-dev \
    redis-server
```

## Installation & Development Setup

1. Clone & enter directory

```bash
git clone https://github.com/yourusername/musicvines-pro.git
cd musicvines-pro
```

2. Create virtual environment

```bash
python -m venv .venv
source .venv/bin/activate    # Windows: .venv\Scripts\activate
```

3. Install dependencies

```bash
pip install --upgrade pip
pip install -r requirements.txt
```

4. Create `.env` file

```env
# Required
XAI_API_KEY=your_xai_api_key_here

# Optional – change if desired
FLASK_SECRET_KEY=super-secret-random-string-123
ALLOWED_ORIGINS=http://localhost:3000,https://yourdomain.com
UPLOAD_FOLDER=uploads
REDIS_URL=redis://localhost:6379/0
CELERY_BROKER_URL=redis://localhost:6379/0
CELERY_RESULT_BACKEND=redis://localhost:6379/0
```

5. Create upload folder

```bash
mkdir -p uploads
```

6. Start Redis (if not running as service)

```bash
redis-server
```

7. Start Celery worker (in separate terminal)

```bash
celery -A app.celery worker --loglevel=info
```

8. Start Flask (development)

```bash
python app.py
# or
flask run --host=0.0.0.0 --port=5000
```

API should now be available at `http://localhost:5000`

## Deployment (Recommended – Docker + docker-compose)

Example `docker-compose.yml`:

```yaml
version: "3.9"

services:
  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"

  worker:
    build: .
    command: celery -A app.celery worker --loglevel=info
    volumes:
      - ./uploads:/app/uploads
    env_file:
      - .env
    depends_on:
      - redis

  web:
    build: .
    command: python app.py
    ports:
      - "5000:5000"
    volumes:
      - ./uploads:/app/uploads
    env_file:
      - .env
    depends_on:
      - redis
      - worker
```

Build & run:

```bash
docker-compose up --build
```

## API Endpoints (Quick Reference)

| Method | Endpoint                  | Description                              | Auth / Rate limit |
|--------|---------------------------|------------------------------------------|-------------------|
| POST   | `/clip_grok`              | Start video generation task              | 10/min            |
| POST   | `/upload_audio`           | Upload user audio file                   | —                 |
| POST   | `/upload_base`            | Upload base video (fallback)             | —                 |
| GET    | `/task_status/<task_id>`  | Get Celery task progress & result        | —                 |
| GET    | `/`                       | Health check                             | —                 |

**Body example for `/clip_grok`** (JSON):

```json
{
  "prompt": "Cyberpunk neon city at night with flying cars, dramatic lighting, bass-heavy vibe",
  "caption": "Original vibes only ✨",
  "user_id": "user123",
  "audio_filename": "user_audio_abc123.mp3",
  "base_video_filename": "base_456789.mp4"
}
```

## Current Limitations (Feb 2026)

- xAI **text-to-video API** is **not publicly available** → must generate base clip in Grok web/app → download → upload
- Waveform generation is CPU-intensive (matplotlib in loop)
- No built-in user authentication / persistence
- No automatic cleanup of old files in `uploads/`

## Planned Improvements & Future Enhancements

High priority:

- [ ] Auto-cleanup cron job for `uploads/` (delete files > 48h old)
- [ ] Add simple API key / JWT authentication
- [ ] Webhook / SSE for real-time progress updates
- [ ] Multiple aspect ratios (1:1, 16:9 support)
- [ ] Beat detection → snap zooms / effects to music
- [ ] Color grade / LUT presets

Nice-to-have:

- [ ] Grok-4 prompt enhancer before video generation
- [ ] Thumbnail generation + upload to X / TikTok draft
- [ ] Voice-over / lyric overlay mode
- [ ] Multi-segment prompt support (scene changes)
- [ ] Web frontend (React / Next.js)

Dream / long-term:

- Replace MoviePy with faster GPU-accelerated pipeline (e.g. diffusers + video extension or ComfyUI workflow)
- Full integration when xAI public video generation becomes available

## Contributing

Pull requests welcome — especially:

- Performance optimizations for waveform / encoding
- Better error handling & user-friendly messages
- Frontend (even a minimal HTML+JS client)

## License

MIT (see [LICENSE](./LICENSE))

Built with ❤️ + frustration using Grok-4, MoviePy, Celery, Redis, Flask — 2025/2026

```

Feel free to adjust links, license, repository name, etc.

