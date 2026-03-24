# Build stage
FROM oven/bun:1.3.2 AS builder

WORKDIR /app

# Copy dependency manifests first for better caching
COPY package.json ./
COPY bun.lockb* ./

# Install dependencies using frozen lockfile if it exists
RUN bun install --frozen-lockfile

# Copy source code
COPY . .

# Build the application (produces ./server executable)
RUN bun run build

# Production stage
FROM oven/bun:1.3.2-slim

# Install curl for healthcheck probes
RUN apt-get update && apt-get install -y --no-install-recommends curl && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Create non-root user
RUN groupadd -r appuser && useradd -r -g appuser appuser

# Copy node_modules and built server from builder stage
COPY --chown=appuser:appuser --from=builder /app/node_modules ./node_modules
COPY --chown=appuser:appuser --from=builder /app/server ./server

# Switch to non-root user
USER appuser

# Expose application port
EXPOSE 3000

# Set default environment variables
ENV NODE_ENV=production \
    PORT=3000 \
    LOG_LEVEL=info \
    POSTGRES_DB=conduit \
    POSTGRES_USER=postgres \
    POSTGRES_PASSWORD=postgres \
    POSTGRES_HOST=0.0.0.0 \
    POSTGRES_PORT=5432 \
    JWT_ALGORITHM=HS256

# Run the server executable
CMD ["./server"]
