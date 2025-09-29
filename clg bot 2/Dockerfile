FROM python:3.10-slim

# Install system dependencies for MySQL client build (Flask-MySQLdb needs libmysqlclient)
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
       build-essential \
       default-libmysqlclient-dev \
       pkg-config \
       git \
    && rm -rf /var/lib/apt/lists/*

ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    NLTK_DATA=/usr/local/share/nltk_data

WORKDIR /app

# Install Python deps first for better layer caching
COPY requirements.txt ./
RUN pip install --upgrade pip \
    && pip install -r requirements.txt

# Pre-download required NLTK datasets to cache in image
RUN python - <<'PY'
import nltk
nltk.download('punkt')
nltk.download('wordnet')
nltk.download('omw-1.4')
PY

# Copy the application code
COPY . .

# Expose the port expected by Render
EXPOSE 10000

# Default start command
CMD ["gunicorn", "app:app", "--workers=2", "--threads=4", "--timeout=120", "--bind=0.0.0.0:${PORT:-10000}"]

