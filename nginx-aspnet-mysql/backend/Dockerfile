FROM --platform=$BUILDPLATFORM mcr.microsoft.com/dotnet/sdk:6.0 AS base

WORKDIR /src

COPY aspnetapp.csproj ./
RUN dotnet restore

FROM base AS builder

COPY . .
RUN dotnet build -c Release -o /build

FROM builder AS dev-envs

RUN apt-get update && apt-get install -y git

RUN useradd -s /bin/bash -m vscode && \
    groupadd docker && \
    usermod -aG docker vscode

# install Docker tools (ONLY if really needed)
# (remove if not required)
# COPY --from=gloursdocker/docker / /

FROM builder AS publisher

RUN dotnet publish -c Release -o /build

FROM mcr.microsoft.com/dotnet/aspnet:6.0

WORKDIR /app

COPY --from=publisher /build .

CMD ["dotnet", "aspnetapp.dll"]

RUN ["dotnet", "publish", "-c", "Release", "-o", "/build"]

FROM --platform=$BUILDPLATFORM mcr.microsoft.com/dotnet/aspnet:6.0

WORKDIR /app
COPY --from=publisher /build .

CMD ["dotnet", "aspnetapp.dll"]
