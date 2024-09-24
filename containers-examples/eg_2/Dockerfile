# Use an official Ubuntu as a base image
FROM ubuntu:latest

# Install sudo package
RUN apt-get update && apt-get install -y sudo

# Create user 'elm-user', add to sudo group, and set password (without specifying UID)
RUN useradd -ms /bin/bash elm-user && \
    usermod -aG sudo elm-user && \
    echo 'elm-user:password123' | chpasswd

# Switch to 'elm-user'
USER elm-user

# Set default working directory
WORKDIR /home/elm-user

# Default shell
CMD ["/bin/bash"]
