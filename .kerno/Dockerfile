# Multi-stage Dockerfile for Bun-based Conduit application

# Stage 1: Build stage
FROM oven/bun:1.3.2 AS builder

WORKDIR /app

# Copy package files
COPY package.json bun.lockb* ./

# Install dependencies
RUN bun install --frozen-lockfile

# Copy source code and build scripts
COPY . .

# Build the application (produces ./server executable)
RUN bun run build

# Stage 2: Production stage
FROM oven/bun:1.3.2-slim AS production

WORKDIR /app

# Copy node_modules for runtime dependencies
COPY --from=builder /app/node_modules ./node_modules

# Copy the built server executable from builder stage
COPY --from=builder /app/server ./server

# Set default environment variables
ENV NODE_ENV=production \
    PORT=3000 \
    POSTGRES_DB=conduit \
    POSTGRES_USER=postgres \
    POSTGRES_PASSWORD=postgres \
    POSTGRES_HOST=0.0.0.0 \
    POSTGRES_PORT=5432 \
    JWT_ALGORITHM=HS256 \
    LOG_LEVEL=info \
    JWT_SECRET=change-me-in-production

# Expose the application port
EXPOSE 3000

# Run the server executable
CMD ["./server"]
