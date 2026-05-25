# ==========================================
# STAGE 1: Build / Assets Preparation
# ==========================================
FROM alpine:3.19 AS builder
WORKDIR /app
COPY index.html .
# Simulate asset optimization/minification if needed
RUN echo "Optimizing assets..." && cat index.html > index.min.html

# ==========================================
# STAGE 2: Secure Production Environment
# ==========================================
FROM nginx:1.25-alpine AS production

# Create a non-privileged system user and group for Nginx
RUN addgroup -S appgroup && adduser -S appuser -G appgroup

# Move optimized code from Stage 1 into the standard Nginx directory
COPY --from=builder /app/index.min.html /usr/share/nginx/html/index.html

# Nginx default configuration tries to use root-only paths (/var/run, /var/cache).
# We must reconfigure Nginx paths so our non-root 'appuser' can write to them.
RUN chown -R appuser:appgroup /usr/share/nginx/html && \
    chmod -R 755 /usr/share/nginx/html && \
    chown -R appuser:appgroup /var/cache/nginx && \
    chown -R appuser:appgroup /var/log/nginx && \
    chown -R appuser:appgroup /etc/nginx/conf.d

# Touch and grant ownership to the PID file location
RUN touch /var/run/nginx.pid && \
    chown -R appuser:appgroup /var/run/nginx.pid

# Switch from root to our restricted user context
USER appuser

EXPOSE 8080
