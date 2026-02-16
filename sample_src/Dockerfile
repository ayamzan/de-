FROM python:3.13.11-slim

WORKDIR /app

# Install uv
RUN pip install --no-cache-dir uv

# Copy dependency files first (better layer caching)
COPY pyproject.toml uv.lock ./

# Install dependencies
RUN uv sync --frozen

# Copy application code
COPY src/ ./src/

# Run your ingestion script
ENTRYPOINT ["uv", "run", "python", "src/ingest_data.py"]