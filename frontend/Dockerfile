# =====================================================
# HomeEase Frontend - Production Dockerfile
# React (Vite) + Nginx Alpine | Port 80
# =====================================================

# ---- Stage 1: Build React/Vite app ----
FROM node:22-alpine AS build

WORKDIR /app

# Copy package files first for layer caching
COPY package*.json ./

# Install all dependencies (including dev for build)
RUN npm ci

# Copy source code
COPY . .

# Build the Vite production bundle
RUN npm run build

# ---- Stage 2: Serve with Nginx ----
FROM nginx:1.27-alpine AS runtime

# Remove default nginx config
RUN rm /etc/nginx/conf.d/default.conf

# Copy custom nginx config (handles React Router + security headers)
COPY nginx.conf /etc/nginx/conf.d/default.conf

# Copy Vite build output from build stage
COPY --from=build /app/dist /usr/share/nginx/html

# Expose HTTP port
EXPOSE 80

# Health check
HEALTHCHECK --interval=30s --timeout=5s --start-period=10s --retries=3 \
  CMD wget -qO- http://localhost/health || exit 1

CMD ["nginx", "-g", "daemon off;"]