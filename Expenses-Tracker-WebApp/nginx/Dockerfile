FROM nginx:1.23.3-alpine

# Copy custom Nginx config
COPY ./default.conf /etc/nginx/conf.d/default.conf

# Run Nginx in foreground (important for Docker)
CMD ["nginx", "-g", "daemon off;"]
