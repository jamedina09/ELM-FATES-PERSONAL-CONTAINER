## Build an Ubuntu Image that runs FATES-ELM

Change to the directory where the Dockerfile is located:

```bash
cd /Users/XXXX/ELM-FATES-PERSONAL-CONTAINER/elm-fates-personal-build
````

Important information regarding the versions of the packages that need to be installed; check the appropriate versions:

- For example, GNU releases: [GCC Releases](https://gcc.gnu.org/releases.html)
- Version 11.2 was released on July 28, 2021.

- For OpenMPI version history: [OpenMPI Versions](https://www.open-mpi.org/software/ompi/versions/timeline.php)
- OpenMPI v4.0.7 was released in November/December 2021.

You may install that version and continue in this way for the rest of the packages.

## To create and share the container image as a package

I want to use github container registry. So, to store packages, I need to create a token with write and read packages permissions. 

More info here:

<https://medium.com/@deepak1812002/get-started-with-github-ghcr-an-alternative-of-dockerhub-f7d5b2198b9a>

Then, after you have all that, you have to login to the registry:

```bash
podman login ghcr.io
```

It will ask for username and password. The password is the token. So, its better you stored somewhere safe, and you remember where it is.

- Push the image to the registry

Go to the directory where the Dockerfile is located, build it, and push it to the registry:

```bash
podman build -t jamedina09/personal-elm-fates-image .
podman push localhost/jamedina09/personal-elm-fates-image:latest ghcr.io/jamedina09/personal-elm-fates-image:latest

```

## To create the image in the local host (your computer)

This step is for development. To run the container in other computers follow description above.

```bash
podman build -t personal-elm-fates-image .
````

- Check Ubuntu version:

```bash
cat /etc/os-release
````

## Download the container

```bash
podman pull ghcr.io/jamedina09/personal-elm-fates-image:latest
````

## To open and run the Container
Remember that is bad practice to attribute a name, you can let podman create a random name so you don't run into conflicts later on.

```bash
podman run --name personal-elm-fates -it personal-elm-fates-image /bin/bash
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
