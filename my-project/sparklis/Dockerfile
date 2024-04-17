# Use an official Nginx image as the base
FROM nginx:alpine

# Remove the default Nginx static files
RUN rm -rf /usr/share/nginx/html/*

# Copy the Sparklis static files into the Nginx server's root directory
COPY sparklis-static-files/webapp /usr/share/nginx/html


# Expose port 80 to the Docker host, so the application can be accessed externally
EXPOSE 80

# Use the default command of the Nginx image, which starts Nginx in the foreground
CMD ["nginx", "-g", "daemon off;"]
