# GithubActions: How to create .NET 8 Web API Docker image and Upload it to Azure Container Registry (ACR)

## 1. Create a .NET 8 Web API in Visual Studio Community Edition

Do not forget to Enable Docker support for automatically create the **Dockerfile** when creating the application

**Dockerfile**

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

Do not forget to add the Kubernetes manifest files in your application: **deployment.yml** and **service.yml** 

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

First, with Azure CLI, we create a new ResourceGroup in France Central region 

```
az group create --name myRG --location francecentral
```

Then we create the new Azure Container Registry ACR named "mycontainerazure1974"
```
az acr create --name mycontainerazure1974 --resource-group myRG --sku Basic --location francecentral
```

We set Admin User in our new Azure ACR

![image](https://github.com/luiscoco/GithubActions_Create_DockerImage_Upload_to_Azure_ACR_dotNET8WebAPI/assets/32194879/3f050394-d530-4cac-a5c1-dda0867d115a)

We copy the username and the password to store both values in Gihub repository secrets.

## 4. Create the Github repository secrets

We nagivate to the Setting menu option in our Github repository

We select Settings->Secrets and variables->Actions->New repository secret and we create the secrets for storing the Azure ACR username and password

![image](https://github.com/luiscoco/GithubActions_Create_DockerImage_Upload_to_Azure_ACR_dotNET8WebAPI/assets/32194879/61703f61-08b0-4249-8dc4-1af251b797a4)

![image](https://github.com/luiscoco/GithubActions_Create_DockerImage_Upload_to_Azure_ACR_dotNET8WebAPI/assets/32194879/4d5ec931-4914-447a-acc2-44e4f7b470fe)

![image](https://github.com/luiscoco/GithubActions_Create_DockerImage_Upload_to_Azure_ACR_dotNET8WebAPI/assets/32194879/f572a38e-f267-48f1-99bf-b9ac2950dbae)

These are the two secrets stored in Github

![image](https://github.com/luiscoco/GithubActions_Create_DockerImage_Upload_to_Azure_ACR_dotNET8WebAPI/assets/32194879/b7a88367-755e-4d98-a428-fb6ccb1a00e2)

## 4. Create the Github Actions Workflow


## 5. Deploy your application docker image in Azure Kubernetes AKS


## 6. Verify the application endpoints






















