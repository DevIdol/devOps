# Use Alpine Linux as base image
FROM alpine:latest

# Install nginx and remove cache
RUN apk update && \
    apk add nginx && \
    rm -rf /var/cache/apk/*

# Copy nginx configuration file
COPY nginx.conf /etc/nginx/http.d/default.conf

# Copy entrypoint script
# COPY entrypoint.sh /entrypoint.sh

# Create html directory
RUN mkdir -p /var/www/html

# Copy html file to /var/www/html
COPY *.html /var/www/html

# Change ownership of /var/www/html to user 'nginx'
RUN chown -R nginx:nginx /var/www/html

# Set Working directory
WORKDIR /var/www/html

# Expose port 80
EXPOSE 80

# Start nginx on container startup
CMD [ "nginx", "-g", "daemon off;" ]
# ENTRYPOINT [ "./entrypoint.sh" ]