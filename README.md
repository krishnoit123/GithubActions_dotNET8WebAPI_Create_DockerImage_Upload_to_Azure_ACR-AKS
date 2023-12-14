# GithubActions: How to create .NET 8 Web API Docker image and Upload it to Azure Container Registry (ACR)

## 1. Create a .NET 8 Web API in Visual Studio Community Edition

Do not forget to Enable Docker support for automatically create the Dockerfile when creating the application

```dockerfile
#See https://aka.ms/customizecontainer to learn how to customize your debug container and how Visual Studio uses this Dockerfile to build your images for faster debugging.

FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
USER app
WORKDIR /app
EXPOSE 8080
EXPOSE 8081

FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src
COPY ["WebApplication1.csproj", "."]
RUN dotnet restore "./././WebApplication1.csproj"
COPY . .
WORKDIR "/src/."
RUN dotnet build "./WebApplication1.csproj" -c $BUILD_CONFIGURATION -o /app/build

FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "./WebApplication1.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "WebApplication1.dll"]
```

Do not forget to add the Kubernetes manifest files in your application: deployment.yml and service.yml 

**deployment.yml**

```yaml
  selector:
    matchLabels:
      app: webapplication1
  template:
    metadata:
      labels:
        app: webapplication1
    spec:
      containers:
      - name: webapplication1
        image: luiscoco/webapplication1:latest  # Replace with your image path
        ports:
        - containerPort: 8080
```

**sevice.yml**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: webapplication1-service
spec:
  type: LoadBalancer
  ports:
  - port: 80
    targetPort: 8080
  selector:
    app: webapplication1
```

## 2. Create a Github repository in Visual Studio 2022 an upload the .NET 8 Web API source code


## 3. Create Azure Container Registry ACR service for storing your Docker image



## 4. Create the Github Actions Workflow


## 5. Deploy your application docker image in Azure Kubernetes AKS


## 6. Verify the application endpoints






















