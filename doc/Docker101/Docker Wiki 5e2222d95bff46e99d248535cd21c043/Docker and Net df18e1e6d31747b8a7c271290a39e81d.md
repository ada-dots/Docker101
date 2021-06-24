# Docker and .Net

**Tip: Step by step to containerize a demo .net core application**

 

# Checkout the demo solution

Download the solution from the shared folder.

Open the solution `ContainerWorkshop.sln` in Visual Studio. Take your time to navigate the code and familiarize yourself with the various projects in the solution. You should be able to identify these:

- `GamingWebApp`, an ASP.NET MVC Core frontend
- `LeaderboardWebAPI`, an ASP.NET Core Web API

# Connect to the Linux container instance

Update the connectionstring in the appsettings.json file to use the local ip address instead of localhost or 127.0.0.1. We will need this later.

```json
{
  "ConnectionStrings": {
    "LeaderboardContext": "Server=tcp:<<your-ip-address>>,5433;Database=Leaderboard;User Id=sa;Password=Pass@word;Trusted_Connection=False;"
  }

```

# Running the applications

Start in Debug LeaderBoardWebApi

Check the IP of the debug configuration of the LeaderBoardWebApi and make sure the same address is used in GamingWebApp appSettings

Start in Debug GamingWebApp  (url: [https://localhost:44326/](https://localhost:44326/))

Observe the data present. Even though we started with an empty database, some data was inserted by the WebApi.

Using Visual Studio and the command in WSL → check the data is actually present in the database.

Stop the application and check the data persistence in the Data Connection → Database: "Leaderboard".

Change one Gamer name and refresh the GamingWebApp

Using the console - query in the docker container. 

Check the presence of the Leaderboard database:

```json
select name from sys.databases
go
```

Check the modified record from Gamers

exit command to close the consode sqlcmd

Connect sqlcmd to the Leaderboard database

```json
docker exec -it sqldocker /opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P "Pass@word" -d Leaderboard
```

```json
select * from Gamers

```

Close the applications and check the state of the database

# Add Docker support

Visual Studio offers tooling for adding support to run your application in a Docker container. You will first add container support to the Web API project.

Have the Output window front and center

To get started you can right-click the **LeaderboardWebAPI** project and select `Add` → `Container Orchestrator Support` from the context menu. Choose `Docker Compose` as the local orchestrator from the dropdown.

In the next dialog, choose `Linux` as the target operating system.

Check the Output window for actions performed already in docker (pulled images and created the needed containers)

Observe the changes that Visual Studio makes to your solution.

Most noticeably you will see that a new Docker Compose project has been added with the name `docker-compose`. It is now your start project for the solution. *(If it's not, make sure to configure it as the startup project.)*

Run `docker ps` and observe there is already the container with the leaderboardwebapi

**Inspect** the contents of the `docker-compose.yml` and `docker-compose.override.yml` files. 

The compose file specifies 

- services (containers)
- volumes (data)
- networks (connectivity)

needed to be created and run. The `override` file is used for local debugging purposes.

For more detailed information on the structure of a dockerfile and a dockercompose check: 

[Docker Compose Tutorial: advanced Docker made simple](https://www.educative.io/blog/docker-compose-tutorial#:~:text=Docker%20Compose%20file%20structure)

Think of docker-compose as an automated multi-container workflow.

**Repeat** adding Docker support for the Web application project. More changes will be made to the YAML files.

To see the containers in Visual Studio you can choose from toolbar "View" →  "Other Windows" → "Containers"

Run your application again. Which projects are effectively started? If some project is not running, start it by choosing `Debug` > `Start new instance` from the right-click context menu of the project.

If you encounter the error 'The DOCKER_REGISTRY variable is not set. Defaulting to a blank string.', make sure you started Visual Studio as an administrator

(url: [https://localhost:44326/](https://localhost:44326/))

> Does the application still work?

Now that the projects are running from a Docker container, the application is not working anymore. You can try to find what is causing the issue, but do not spend too much time to try to fix it. We will do that next.

Some things to try if you feel like finding the cause: 

Inspect the running and stopped containers
Try to reach the Web API from https://localhost:44369/swagger.
Debug the call from the web page to the API by stepping through the code.Verify application settings for each of the projects.

The `docker-compose.override.yml` file contains port mappings, defining the ports inside the container. However, there is no mapping to the outside yet. Change the composition file to add the port numbers for the `gamingwebapp` and `leaderboardwebapi` to reflect these mappings:

```
version: '3.4'

services:
  leaderboardwebapi:
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
    ports:
      **- "5000:80"
      - "5001:443"**
    volumes:
      - ${APPDATA}/ASP.NET/Https:/root/.aspnet/https:ro
  gamingwebapp:
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
    ports:
      **- "5619:80"
      - "44326:443"**
    volumes:
      - ${APPDATA}/ASP.NET/Https:/root/.aspnet/https:ro

```

The setting for `LeaderboardWebApi:BaseUrl`  from **GamingWebApp appSettings.json** should now point to the new endpoint of the Web API with the internal address `http://leaderboardwebapi`.

> For now, notice that the URL is not referring to localhost but leaderboardwebapi, which is the name of the Docker container service as defined in the docker-compose.yml file.

we will not get into details with Networking in Docker

[https://docs.docker.com/network/](https://docs.docker.com/network/)

Make sure you use the HTTP endpoint, because hosting an HTTPS endpoint with self-signed certificates in a cluster does not work by default.

> Changing the setting in the appsettings.json file will work and you could choose to do so for now. It does mean that the setting for running without container will not work anymore.

In case you thought of changing the setting via environment variables, you can make this change inside of the `docker.override.yml` file.

```
gamingwebapp:
  environment:
    - ASPNETCORE_ENVIRONMENT=Development
    - LeaderboardApiOptions__BaseUrl=http://leaderboardwebapi

```

Make sure you changed the IP address of the connection string in the application settings for the Web API to be your local IP address (of your LAN) instead of `127.0.0.1` or `localhost`. 

Start the solution by pressing `F5`. See if it works correctly. 

Go to: [https://localhost:44326/](https://localhost:44326/)

If you get error messages indicating that ports are in use, shut down IIS Express from the tray icon. The switch to a Docker environment with the same port numbers created the conflict. 

# **Debugging with Docker container instances**

One of the nicest features of the Docker support in Visual Studio is the debugging support while running container instances. Check out how easy debugging is by stepping through the application like before.

Put a breakpoint at the first statement of the `OnGetAsync` method in the `IndexModel` class in the `GamingWebApp` project. Add another breakpoint in the `Get` method of the LeaderboardController in the Web API project. Run the application by pressing F5. You should be hitting the breakpoints and jump from one container instance to the other.

# Running SQL Server in a Docker container composition

Now that your application is running two projects in Docker containers, you can also run SQL Server in the same composition. This is convenient for isolated development and testing purposes. It eliminates the need to install SQL Server locally and to start the container for SQL Server manually.

Go ahead and add the definition for a container service in the `docker-compose.yml` file.

```
  sql.data:
    image: mcr.microsoft.com/mssql/server:2019-latest

```

The new container service also requires these same environment variables. Add them to the `docker-compose.override.yml` file under an entry for sql.data.

```
  sql.data:
    environment:
      - SA_PASSWORD=Pass@word
      - MSSQL_PID=Developer
      - ACCEPT_EULA=Y
    ports:
      - "1433"

```

> Which additional changes are needed?

You will need to change the connection string for the Web API to reflect the new way of hosting of the database. Add a new environment variable for the connection string of the leaderboard.webapi service in the `docker-compose.override.yml` file:

```
- ConnectionStrings__LeaderboardContext=Server=sql.data;Database=Leaderboard;User Id=sa;Password=Pass@word;Trusted_Connection=False

```

With this change, you should be able to run your applications in containers. Make sure you have stopped any containers related to the application. 

[Docker Wiki](../Docker%20Wiki%205e2222d95bff46e99d248535cd21c043.md)