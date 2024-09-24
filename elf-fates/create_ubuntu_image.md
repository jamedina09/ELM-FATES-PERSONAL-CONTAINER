## Build an Ubuntu Image

1. Change to the directory where the Dockerfile is located:

```bash
cd /path/to/your/dockerfile/directory
````

2. We are creating an image to get elm-fates.

I’m updating what Serbing did. I am using Ubuntu 20.04 (this can be changed later).

3. So far, I have Ubuntu and GCC installed.

I need to continue installing the rest of the packages.

4. Regarding the versions, check the appropriate versions:

   - For example, GNU releases: [GCC Releases](https://gcc.gnu.org/releases.html)
     - Version 11.2 was released on July 28, 2021.

   - For OpenMPI version history: [OpenMPI Versions](https://www.open-mpi.org/software/ompi/versions/timeline.php)
     - OpenMPI v4.0.7 was released in November/December 2021.

   You may install that version and continue in this manner for the rest of the packages.

## To Create the Image

```bash
podman build -t elm-fates-ubuntu-image .
````

- Check Ubuntu version:

```bash
cat /etc/os-release
````

## To Open the Container

```bash
podman run --name ubun_test -it elm-fates-ubuntu-image /bin/bash
````

## To Load a Working Directory as a Volume

1. Create a `docker-compose.yml` file.

```bash
services:
  my_service:
    image: elm-fates-ubuntu-image  # Use your existing image name here
    container_name: personal-elm-fates  # Specify your custom container name
    volumes:
      - /Users/MedinaJA/projects:/projects_mirror
    tty: true
    user: elm-user  # Run the container as the non-root user

volumes:
  projects:
````

## To commit and test exercises as user-elm

1. Start a Container from the Existing Image:

```bash
podman run -it --name temp_container localhost/elm-fates-ubuntu-image /bin/bash
````

2. Add the user:

```bash
useradd -ms /bin/bash elm-user
````

3. Exit

```bash
exit
````

4. Commit the changes:

```bash
podman commit temp_container localhost/elm-fates-ubuntu-image
````

5. Remove the temporary container:

```bash
podman rm temp_container
````

6. Load the working directory as a volume:

```yaml
podman compose up -d
````

7. open the container:

```bash
podman exec -it personal-elm-fates /bin/bash
````

8. To close the container:

```bash
exit
````

```bash
podman compose down
````

9. To remove the container

```bash
podman rm personal-elm-fates
````

## To add new features to the container

1. Open the container:

```bash
podman run --name ubun_test -it elm-fates-ubuntu-image /bin/bash
````

# dpkg -L zlib1g-dev

2. Add the featur

```bash
apt-get update
apt-get install -y m4
apt-get clean
rm -rf /var/lib/apt/lists/*
````

3. Commit it

```bash
podman commit -m "Added Zlib" ubun_test localhost/elm-fates-ubuntu-image
````

4. Open a new container to test the changes:

```bash
podman run -it --name temp_container localhost/elm-fates-ubuntu-image /bin/bash
````

5. Remove the temporary container:

```bash
podman rm ubun_test
````

## Check the final container

1. To start your service again:

```bash
podman compose up -d
````

2. To access the container:

```bash
podman exec -it personal-elm-fates /bin/bash
````

## Close the final container

```bash
podman compose down
````
