# Use a minimal base image
FROM alpine:latest

# Set the working directory
WORKDIR /app

# Create a text file with some content
RUN echo "Hello, Podman!" > hello.txt

# Command to keep the container running (optional, if you want to keep it alive)
CMD ["tail", "-f", "/dev/null"]