# legendary-happiness


Project look.
```
api/                      # project root
├── Program.cs            # minimal API entrypoint
├── api.csproj            # .NET project file
├── Dockerfile            # build + runtime image
├── .dockerignore         # ignore local build files
└── docker-compose.yml    # compose file to build & run
```

## Program.cs
```csharp
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

app.MapGet("/", () => "Hello World!");

app.Run("http://0.0.0.0:5000");
```

I wont have none of that weather forecast stuff :P



## api.csproj
```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
  <PropertyGroup>
    <TargetFramework>net8.0</TargetFramework>
    <ImplicitUsings>enable</ImplicitUsings>
    <Nullable>enable</Nullable>
  </PropertyGroup>
</Project>
```

## dockerfile
```dockerfile
# 1. Build stage
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /src

# copy csproj and restore
COPY ["api.csproj", "./"]
RUN dotnet restore "api.csproj"

# copy everything and publish
COPY . .
RUN dotnet publish "api.csproj" -c Release -o /app/publish

# 2. Runtime stage
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS runtime
WORKDIR /app
COPY --from=build /app/publish ./

ENTRYPOINT ["dotnet", "api.dll"]
```

## .dockerignore
```.dockerignore
bin/
obj/
.vs/
.git/
*.user
*.suo
docker-compose.*
```

## docker compose
```docker compose
services:
  api:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - "5000:5000"
    restart: unless-stopped
```

# Run it ll

1. go into your folder and run

## bash
```docker compose
$ docker compose up -d
```

-d is for detached. I just like to have control of my terminal afterwards.


2. Navigate to [http://localhost:5000](http://localhost:5000) to see the "Hello World!" response.

This setup isolates build and runtime, keeps the image lean, and maps port 5000 on your host to the container.
