# Stage 1: Build dependencies
FROM python:3.11-slim as builder

WORKDIR /build

RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    gcc \
    && rm -rf /var/lib/apt/lists/*

COPY requirements.txt .

RUN pip install --no-cache-dir --user -r requirements.txt

# Stage 2: Final minimal image
FROM python:3.11-slim

WORKDIR /app

# Install system dependencies needed at runtime (e.g. libzbar0 for pyzbar)
RUN apt-get update && apt-get install -y --no-install-recommends \
    libzbar0 \
    && rm -rf /var/lib/apt/lists/*

# Copy installed packages from builder
COPY --from=builder /root/.local /root/.local
COPY --from=builder /build/requirements.txt .

# Ensure scripts in .local/bin are usable
ENV PATH=/root/.local/bin:$PATH
ENV PYTHONUNBUFFERED=1

# Copy application source code
COPY app/ ./app/
COPY models/ ./models/

EXPOSE 8000

CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
