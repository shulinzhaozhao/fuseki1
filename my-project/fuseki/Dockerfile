# Use a slim Debian-based OpenJDK image
FROM openjdk:23-jdk-slim

# Set the working directory inside the container
WORKDIR /fuseki

# Use the official Fuseki base image
FROM stain/jena-fuseki

# Copy configuration files and ontology into the container
COPY config.ttl /fuseki/config.ttl
COPY shiro.ini /fuseki/shiro.ini

# Expose the port Fuseki runs on
EXPOSE 3030

# Start Fuseki server with the configuration file
CMD ["/jena-fuseki/fuseki-server", "--config=/fuseki/config.ttl"]