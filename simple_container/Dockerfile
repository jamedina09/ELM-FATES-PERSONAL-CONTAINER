# Use Ubuntu 22.04 as the base image
FROM ubuntu:22.04

# Set the environment variable to avoid interactive prompts during installation
ENV DEBIAN_FRONTEND=noninteractive

# Update the package list and install necessary packages
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
    curl \
    vim \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# Create a user and group with specific UID and GID
ARG UID=1000  # Allow setting the UID for the user
ARG GID=1000  # Allow setting the GID for the group
ENV USER=elm-user

# Check if the group exists, if not create it; then create the user
RUN if ! getent group ${GID} >/dev/null; then groupadd -g ${GID} elmgroup; fi && \
    if ! id -u ${UID} >/dev/null 2>&1; then useradd -m -u ${UID} -g ${GID} ${USER}; fi && \
    mkdir -p /projects_mirror && \
    chown -R ${UID}:${GID} /projects_mirror

# Switch to the new user
USER ${USER}

# Set the default command to run when the container starts
CMD ["/bin/bash"]

## This works well
# podman build -t my-ubuntu-container .
# podman compose up -d
# podman exec -it test_ubuntu /bin/bash
# echo "Hello from the container!" > /projects_mirror/tst.txt



## this will fix it:
## chmod +x /home/ja-medina-vega/projects/sc.sh
## you need to make it executable in the host, so it will be executable in the container. duuhh
