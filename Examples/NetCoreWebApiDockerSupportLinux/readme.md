# Project creation

This project has been created with the template for ASP NET Core Web Application (included in the .NET 6.0 SDK (v6.0.301) instalation)
- Type: Web API
- Language: C#
- Framework: 6.0
- Auth: no auth
- Docker support: linux

# Building an image of my project
Requires: 
- Docker Desktop previously installed.
- .NET 6.0 SDK (v6.0.301) installed.
- IDE: Visual Studio or Rider

[Tutorial: Containerize a .NET app](https://docs.microsoft.com/en-us/dotnet/core/docker/build-container?tabs=windows)

Command for building the image: ```docker build -t netcorewebapi -f Dockerfile .```
(executed from the root folder of the project)

## Error 1. Files not found

When running an error appears due to incorrect .csproj path mapping. It is not found.

In this case I manually change the paths (fixing them with relative path from the folder where I am running the command which is the root folder of the project)

## Error 2. Not finding suitable entry point executable 
Then another error appears:
```
> [build 7/7] RUN dotnet build "NetCoreWebApiDockerSupportLinux.csproj" -c Release -o /app/build:
#15 0.755 Microsoft (R) Build Engine version 17.2.0+41abc5629 for .NET
#15 0.755 Copyright (C) Microsoft Corporation. All rights reserved.
#15 0.755
#15 1.426   Determining projects to restore...
#15 1.982   All projects are up-to-date for restore.
#15 3.818 CSC : error CS5001: Program does not contain a static 'Main' method suitable for an entry point [/src/NetCoreWebApiDockerSupportLinux/NetCoreWebApiDockerSupportLinux.csproj]
#15 3.835
#15 3.835 Build FAILED.
#15 3.835
#15 3.835 CSC : error CS5001: Program does not contain a static 'Main' method suitable for an entry point [/src/NetCoreWebApiDockerSupportLinux/NetCoreWebApiDockerSupportLinux.csproj]
#15 3.835     0 Warning(s)
#15 3.835     1 Error(s)
#15 3.835
#15 3.835 Time Elapsed 00:00:03.00
------
executor failed running [/bin/sh -c dotnet build "NetCoreWebApiDockerSupportLinux.csproj" -c Release -o /app/build]: exit code: 1

```

I looked in the internet and I found a solution in [stackoverflow](https://stackoverflow.com/questions/52991469/getting-program-does-not-contain-a-static-main-method-suitable-for-an-entry-p):

"I struggled with this, and in my case I had already made most of the adjustments to the path as correctly advised in the answer. It turns out the one thing that was breaking my build step was that I had the COPY . . command before the WORKDIR command after the restore. Once I corrected this, my build step worked. Thanks for your help, hopefully my additional pointer helps someone as well. –
CoderSivu"

After changing the order of workdir and copy in build stage all worked fine:

From this: 

```
FROM mcr.microsoft.com/dotnet/sdk:6.0 AS build
WORKDIR /src
COPY ["NetCoreWebApiDockerSupportLinux.csproj", "NetCoreWebApiDockerSupportLinux/"]
RUN dotnet restore "NetCoreWebApiDockerSupportLinux/NetCoreWebApiDockerSupportLinux.csproj"
COPY . .
WORKDIR "/src/NetCoreWebApiDockerSupportLinux"
RUN dotnet build "NetCoreWebApiDockerSupportLinux.csproj" -c Release -o /app/build
```

To this:

```
FROM mcr.microsoft.com/dotnet/sdk:6.0 AS build
WORKDIR /src
COPY ["NetCoreWebApiDockerSupportLinux.csproj", "NetCoreWebApiDockerSupportLinux/"]
RUN dotnet restore "NetCoreWebApiDockerSupportLinux/NetCoreWebApiDockerSupportLinux.csproj"
WORKDIR "/src/NetCoreWebApiDockerSupportLinux"
COPY . .
RUN dotnet build "NetCoreWebApiDockerSupportLinux.csproj" -c Release -o /app/build
```

## Success

Final Dockerfile

```
FROM mcr.microsoft.com/dotnet/aspnet:6.0 AS base
WORKDIR /app
EXPOSE 80
EXPOSE 443

FROM mcr.microsoft.com/dotnet/sdk:6.0 AS build
WORKDIR /src
COPY ["NetCoreWebApiDockerSupportLinux.csproj", "NetCoreWebApiDockerSupportLinux/"]
RUN dotnet restore "NetCoreWebApiDockerSupportLinux/NetCoreWebApiDockerSupportLinux.csproj"
WORKDIR "/src/NetCoreWebApiDockerSupportLinux"
COPY . .
RUN dotnet build "NetCoreWebApiDockerSupportLinux.csproj" -c Release -o /app/build

FROM build AS publish
RUN dotnet publish "NetCoreWebApiDockerSupportLinux.csproj" -c Release -o /app/publish

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "NetCoreWebApiDockerSupportLinux.dll"]
```

Final cli

```
docker build -t netcorewebapi -f Dockerfile .
```

# Running the image in a Docker container locally

Finally once I got the image it is time to run it in a docker container

```
docker run -dp 80:80 netcorewebapi
```

## Error 1. Not defualt page to view

If I navigate to http://localhost/ I don't see anything.

If I try to navigate to http://localhost/swagger/index.html I don`t see anything as well. That's because swagger is only active for development build. After enabling swagger for every enviroment:

```
// if (app.Environment.IsDevelopment())
// {
    app.UseSwagger();
    app.UseSwaggerUI();
// }
```

## Success

If I rebuild de image and run it in a container:

```
docker build -t netcorewebapi -f Dockerfile .
docker run -dp 80:80 netcorewebapi
```

I can see my web app navigating to: 

http://localhost/swagger/index.html

Or requesting my controller endpoint:

http://localhost/WeatherForecast
