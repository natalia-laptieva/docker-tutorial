# How to containerize your application

In this tutorial, you will learn how to prepare a Docker image, build it, and run it as a container on your local machine.

## Overview

[Docker](https://www.docker.com) allows you to package and run applications in isolated containers.

- A **Docker container** is a package that contains everything required to run an application, including code, dependencies, and system libraries. They can be run on local or virtual machines or deployed to the cloud.
- A **Docker image** is a template used to execute code in a Docker container.
- A **Dockerfile** is a file that contains instructions for building your source code. Docker builds images by reading the instructions from a Dockerfile.

<div align="center">

![Prepare, build, and run an application image](images/docker-process.svg)

</div>

## Create a sample application

1. Create a project directory for your application named *quickstart_docker* with the following structure:

   ```
   quickstart_docker/
   ├──application/
   └──docker/
      └──application/
   ```    

   - The *application* directory will contain your application files.
   - The *docker* directory will contain Docker-related files for your project.
   - The *docker/application* directory will contain a Dockerfile.

2. Create an empty file named *application.py* in the *quickstart_docker/application* directory.
3. Add a Python code sample to the file:

    ```python
    import http.server
    import socketserver
    
    PORT = 8000
    
    Handler = http.server.SimpleHTTPRequestHandler
    
    httpd = socketserver.TCPServer(("", PORT), Handler)
    
    print("serving at port", PORT)
    httpd.serve_forever()
    ```

## Create a Dockerfile for your application

1. Create an empty file named *Dockerfile* in the *quickstart_docker/docker/application* directory.
2. Add the following instructions to the [Dockerfile](https://docs.docker.com/engine/reference/builder):

   ```Dockerfile
   FROM python:3.5
   WORKDIR /app
   COPY ./application /app
   EXPOSE 8000
   CMD ["python", "/app/application.py"]
   ```

   - The `FROM` instruction sets the base image. In this example, it is an [official Python image from Docker Hub](https://hub.docker.com/_/python) with version 3.5.
   - The `WORKDIR` instruction sets the working directory. In this example, it is set to */app*.
   - The `COPY` instruction copies the directory contents into the container directory. In this example, the files are copied from the */application* directory to the */app* directory.
   - The `EXPOSE` instruction determines port assignments for operating container. In this example, it is port 8000.
   - The `CMD` instruction specifies what command to run within the container. In this example, the command is `python /app/application.py`.

      > There can only be one `CMD` instruction in a Dockerfile.

## Build an application image

> Ensure you have installed the [Docker engine](https://docs.docker.com/engine/install) in your operating system.

1. To build an image from your Dockerfile, run the [docker build](https://docs.docker.com/engine/reference/commandline/build) command with the following options:

    ```bash
    docker build --file docker/application/Dockerfile --tag exampleapp:v1.0 .
    ```

   - The `--file` flag specifies the name of the Dockerfile. In this example, it is *docker/application/Dockerfile*.
   - The `--tag` flag sets the image name and optionally its tag in the `name:tag` format. In this example, the name is *exampleapp* and the tag is *v1.0*.
   
      > Specifying an image version in the tag allows you to navigate between changes easily. If you do not specify the tag, an image will be tagged as `latest`. 
   
   - `.` specifies the [`PATH`](https://docs.docker.com/engine/reference/commandline/build/#build-with-path). In this example, it is your working directory.

2. To see the list of your images, run the [docker images](https://docs.docker.com/engine/reference/commandline/images) command:

    ```bash
    docker images
    ```

   If your image was created successfully, you will see your image in the following table:
   
   ```
   REPOSITORY                 TAG       IMAGE ID       CREATED        SIZE
   exampleapp                 v1.0      92f1c05cce6a   2 seconds agо  871MB
   ```

## Run the application image on your local machine

To start a container with your application image, run the [docker run](https://docs.docker.com/engine/reference/commandline/run) command:

 ```bash
 docker run --publish 127.0.0.1:8000:8000 exampleapp:v1.0
 ```

The `--publish` flag creates a port mapping between the host and the container. In this example, it is `127.0.0.1:8000` and `8000`.

You can confirm that your application container is running by checking http://localhost:8000. 
Once confirmed, push changes to the remote repository.

> If you prefer, you can also use the [Docker Desktop](https://www.docker.com/products/docker-desktop) graphical interface to build and run your image.

## Next steps

Learn how to [share your application image on Docker Hub](https://docs.docker.com/get-started/04_sharing_app)
and [deploy it to Kubernetes](https://docs.docker.com/get-started/kube-deploy).