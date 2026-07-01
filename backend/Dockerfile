# =====================================================
# HomeEase Backend - Production Dockerfile
# Node.js 22 Alpine | Express API | Port 5000
# =====================================================

# ---- Stage 1: Install dependencies ----
FROM node:22-alpine AS deps

WORKDIR /app

# Install only production dependencies first (layer caching)
COPY package*.json ./
RUN npm ci --only=production

# ---- Stage 2: Runtime ----
FROM node:22-alpine AS runtime

# Install dumb-init for proper signal handling
RUN apk add --no-cache dumb-init

# Create non-root user for security
RUN addgroup -g 1001 -S nodejs && \
    adduser -S homeease -u 1001 -G nodejs

WORKDIR /app

# Copy production node_modules from deps stage
COPY --from=deps --chown=homeease:nodejs /app/node_modules ./node_modules

# Copy source code
COPY --chown=homeease:nodejs . .

# Switch to non-root user
USER homeease

# Expose API port
EXPOSE 5000

# Health check: verify API is alive
HEALTHCHECK --interval=30s --timeout=10s --start-period=20s --retries=3 \
  CMD wget -qO- http://localhost:5000/api/health || exit 1

# Use dumb-init to handle SIGTERM/SIGINT cleanly
ENTRYPOINT ["dumb-init", "--"]
CMD ["node", "server.js"]
