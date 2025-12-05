# SpookyMart Frontend Dockerfile
FROM node:18-alpine AS builder

# Set working directory
WORKDIR /app

# Copy package files
COPY package*.json ./

# Install dependencies
RUN npm install

# Copy source code
COPY . .

# Build the application
RUN npm run build

# Production stage - Use Node.js with serve instead of nginx
FROM node:18-alpine AS production

# Install serve globally for serving static files
RUN npm install -g serve

# Create app directory
WORKDIR /app

# Create non-root user for security
RUN addgroup -g 1001 -S nodejs && \
    adduser -S spookymart -u 1001

# Copy built application from builder stage
COPY --from=builder --chown=spookymart:nodejs /app/build ./build

# Install curl for health checks
RUN apk add --no-cache curl

# Switch to non-root user
USER spookymart

# Expose port 8080
EXPOSE 8080

# Health check - check if serve is responding
HEALTHCHECK --interval=30s --timeout=10s --start-period=60s --retries=3 \
  CMD curl -f http://localhost:8080/ || exit 1

# Start serve to serve the static files
CMD ["serve", "-s", "build", "-l", "8080", "--no-clipboard"]
