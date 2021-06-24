# Getting Started

Goals for this lab:

## Prerequisites

make sure you have installed all the items from the section "Tools we will use"

# Inspect your Docker environment

Let's check whether your Docker Desktop tooling is correctly set up. Also, you might need to get familiar with the Docker tooling.

You should see the Docker tooling running. On Windows, you can check this by looking for a tray icon like this:

![Getting%20Started%201ceba38f6793478c95f3e08689ad6490/Untitled.png](Getting%20Started%201ceba38f6793478c95f3e08689ad6490/Untitled.png)

If you cannot find the Docker icon in the tray, you might need to start the Docker tooling installed for most of the time at the path:

```
C:\Program Files\Docker\Docker\Docker for Windows.exe
```

![Getting%20Started%201ceba38f6793478c95f3e08689ad6490/Untitled%201.png](Getting%20Started%201ceba38f6793478c95f3e08689ad6490/Untitled%201.png)

After starting the Docker tooling, the tray icon should be present.

- Open a terminal
- If on Windows, run:
This opens your default WSL distro - we installed in section "Prepare your development laptop" Ubuntu

    ```
    wsl
    ```

## Managing container instances

Let's start some container instances. 

The most common sample is "Hello World". 

This will start a container that should run if everything is configured correctly. From a command prompt, run the following command:

```
docker run hello-world
```

This should output a message that starts with the following:

```
Hello from Docker!
```

This message shows that your installation appears to be working correctly.

![https://training.play-with-docker.com/images/ops-basics-hello-world.svg](https://training.play-with-docker.com/images/ops-basics-hello-world.svg)

To generate this message, Docker took the following steps:

1.  The Docker client contacted the Docker daemon.
2.  The Docker daemon pulled the "hello-world" image from the Docker Hub.
3.  The Docker daemon created a new container from that image which runs the executable that produces the output you are currently reading.
4.  The Docker daemon streamed that output to the Docker client, which sent it to your terminal.

To see if there are any running container instances, run the following command:

```bash
docker ps
```

It might return an empty result:

```
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES

```

This is because the **hello-world** container instance has already stopped after producing the console output. 

To see stopped containers change the previous docker command to, use 

```bash
docker ps -a 
```

to output all container instances, running and stopped.

### Something more advanced

Now let's see if we can run a useful tool inside a container. 

First, create a [volume](../Docker%20Wiki%205e2222d95bff46e99d248535cd21c043.md) to store container configuration data:

```
docker volume create portainer_data
```

Next, start a new container with:

```
docker run -d -p 8000:8000 -p 9000:9000 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce

```

Portainer is an open-source management UI for Docker. Portainer makes it easier for you to manage your Docker containers, it allows you to manage containers, images, networks, and volumes from the web-based Portainer dashboard.

This command runs a containerized web server at port 9000. The web server port is connected to the host machine (your laptop). This means you can now access the web server.
For example, try using the `curl` command to see if it works:

```
curl localhost:9000
```

cURL, which stands for client URL, is a command line tool that developers use to transfer data to and from a server. At the most fundamental, cURL lets you talk to a server by specifying the location (in the form of a URL) and the data you want to send. cURL supports several different protocols, including HTTP and HTTPS, and runs on almost every platform.

This should output raw HTML to your terminal.

In your browser, navigate to [http://localhost:9000](http://localhost:9000/) and create a login for your local machine. Choose to manage the Local Docker Environment. The management UI can come in useful to manage Docker, while you are learning about the Docker CLI. It should show (at least) 2 containers on your machine:

![Getting%20Started%201ceba38f6793478c95f3e08689ad6490/Untitled%202.png](Getting%20Started%201ceba38f6793478c95f3e08689ad6490/Untitled%202.png)

Feel free to spend some time in this environment for a minute or two. See if you can start the hello-world container again.

### Running an Nginx web server

Return to your command prompt and search the Docker Hub for public images. Try searching for `ngingx` which is an HTTP web server and reverse proxy.

Search for a container image (template) named 'nginx'

```bash
docker search nginx
```

Nginx, pronounced like “engine-ex”, is an open-source web server that, since its initial success as a web server, is now also used as a reverse proxy, HTTP cache, and load balancer.

Some high-profile companies using Nginx include Autodesk, Atlassian, Intuit, T-Mobile, GitLab, DuckDuckGo, Microsoft, IBM, Google, Adobe, Salesforce, VMWare, Xerox, LinkedIn, Cisco, Facebook, Target, Citrix Systems, Twitter, Apple, Intel, and many more (source).

Nginx was originally created by Igor Sysoev, with its first public release in October 2004. Igor initially conceived the software as an answer to the C10k problem, which is a problem regarding the performance issue of handling 10,000 concurrent connections.

As you can see in the output, there are quite a few, but only one is marked as 'official'. Anyone on the planet can create a container image named nginx and publish it. Make sure you get your images from trusted sources!

Get a local copy of the container image:

```
docker pull nginx
```

Create a container from the image and connect its network port 80 to your machines' port 8090:

```bash
docker run -it --name docker-nginx -p 8090:80 nginx
```

`/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Configuration complete; ready for start up`

The last command seems to block. That's okay. Navigate to [http://localhost:8090](http://localhost:8090/). You will notice that the output of the nginx container is now filling the console. The prompt is missing. This is because your terminal is now attached to the container, by specifying `-it` with the `run` command. To detach your terminal from the container, use the key combination `Ctrl+P, Ctrl+Q` to detach from the container and return to the prompt. You can avoid your terminal being attached by specifying `-d`, as we did with Portainer earlier.

Inspecting the running containers with `docker ps`, you should find that the nginx container is still running:

```
docker ps

CONTAINER ID   IMAGE                    COMMAND                  CREATED          STATUS          PORTS                                            NAMES
a9eb60b21543   nginx                    "/docker-entrypoint.…"   21 seconds ago   Up 19 seconds   0.0.0.0:8090->80/tcp                             docker-nginx
c60021dd30ca   portainer/portainer-ce   "/portainer"             22 minutes ago   Up 22 minutes   0.0.0.0:8000->8000/tcp, 0.0.0.0:9000->9000/tcp   portainer

```

### Running Microsoft SQL Server on Linux

Next, start a container to run an instance of SQL Server on Linux.
The image for SQL Server for Linux is located in the Docker Store, since it is an official image. Navigate to [https://hub.docker.com](https://hub.docker.com/) and search for [SQL Server](https://hub.docker.com/_/microsoft-mssql-server) there. Pull the image when you found its name:

```bash
docker pull mcr.microsoft.com/mssql/server:2019-latest
```

Use the command:

```bash
docker run -e ACCEPT_EULA=Y -e MSSQL_PID=Developer -e SA_PASSWORD="Pass@word" --name sqldocker -p 5433:1433 -d mcr.microsoft.com/mssql/server:2019-latest
```

The SQL Server container is started detached, so it will not show any output other than the container ID (yours will be different):

![Getting%20Started%201ceba38f6793478c95f3e08689ad6490/Untitled%203.png](Getting%20Started%201ceba38f6793478c95f3e08689ad6490/Untitled%203.png)

You can refer to this container by (part of) its ID, or its name `sqldocker`. You do not have to specify the entire container ID for commands with the Docker CLI. It is sufficient to provide enough characters from the start to give a unique match. In the example above this would be `4` or `4f` or `4f5`, depending on the IDs of other containers on your machine.

You can still examine the output of the container even though it is detached from the console. Simply run `docker logs <container-id>`, substituting the ID of the container, like so:

```bash
docker logs 4f5
```

### Using `SQLCMD` to connect to your database server

You can now attach a terminal to the server and run queries by using `sqlcmd`:

```
docker exec -it sqldocker /opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P "Pass@word"
```

Next, run the following command to show all tables in the master database.

```bash
1> SELECT * FROM INFORMATION_SCHEMA.TABLES WHERE TABLE_TYPE='BASE TABLE'
2> go

```

Run `exit` to close the container terminal connection, and return to local environment.

```bash
exit
```

### Using Visual Studio to connect to your database server

Start Visual Studio and examine the contents of the master database by adding a Data Connection in the Server Explorer. The database connection details are:

- Server: 127.0.0.1,5433 (notice the **comma** instead of a colon)
- Username: sa
- Password: Pass@word
- Database instance: master

![Getting%20Started%201ceba38f6793478c95f3e08689ad6490/Untitled%204.png](Getting%20Started%201ceba38f6793478c95f3e08689ad6490/Untitled%204.png)

Click on 'Test Connection' to verify your input. After that, you can interact with your containerized SQL Server from Visual Studio.

### Cleaning up

Inspect the running containers again with `docker ps -a` to include stopped containers (like hello-world).

```bash
docker ps -a
```

Stop and remove the `nginx` container with these commands:

```bash
docker stop docker-nginx
docker rm docker-nginx
```

Note: To issue a stop and rm command in one go, use docker rm -f

```bash
docker rm -f <container>
```

Do the same for the `hello-world` container by using its container ID or the generated two-part name, which is something like `loving_newton`.

Note that you can specify --rm with the docker run command to have the container automatically deleted when it exits.

## Working with container images

By running the examples above you will have downloaded some container images. You can see which ones are available on your machine by the following command:

```bash
docker images
```

Remove the image `hello-world`:

```bash
docker rmi hello-world
```

## Container state and volumes

You have already seen that containers support network connectivity. They also support disk operations. However, any data written to disk inside a container is only visible from within the container. It also shares the lifecycle of the container. This means that the data is lost when the container is deleted. There is a way to persist files generated by container. You do this by having the container write to a `volume`. You did this while creating the Portainer container earlier! A volume is a file or folder that exists outside of the container, for example on the host or on a network share. By storing files in volumes instead of inside the container, they will have a lifecycle that is not coupled that of the container; files remain and can be used by consecutive container instances.
In the case of Portainer, this means that you won't have to recreate the admin account every time you create a new container instance (for example, after upgrading to a newer version).

Go ahead and give it a try:

Stop and remove the running portainer image:

```bash
docker rm -f portainer
```

Recreate it:

```bash
docker run -d -p 8000:8000 -p 9000:9000 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce

```

and [log in](http://localhost:9000/) using the account you created earlier.

Now delete the container **and** the volume and repeat the process:

```bash
docker rm -f portainer
docker volume rm portainer_data
docker run -d -p 8000:8000 -p 9000:9000 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce

```

Portainer has lost its state by deleting the volume. It will now ask you to create an account as if it's the first time you ran Portainer when you [log in](http://localhost:9000/).

[Docker Wiki](../Docker%20Wiki%205e2222d95bff46e99d248535cd21c043.md)