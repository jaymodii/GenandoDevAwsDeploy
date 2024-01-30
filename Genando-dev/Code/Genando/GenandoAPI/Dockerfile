FROM mcr.microsoft.com/dotnet/aspnet:6.0 AS base
WORKDIR /app
EXPOSE 5235

ENV ASPNETCORE_URLS=http://+:5235

# Creates a non-root user with an explicit UID and adds permission to access the /app folder
# For more info, please refer to https://aka.ms/vscode-docker-dotnet-configure-containers
RUN adduser -u 5678 --disabled-password --gecos "" appuser && chown -R appuser /app
USER appuser

FROM --platform=$BUILDPLATFORM mcr.microsoft.com/dotnet/sdk:6.0 AS build
ARG configuration=Release
WORKDIR /src
COPY ["Genando-dev/Code/Genando/GenandoAPI/GenandoAPI.csproj", "Genando-dev/Code/Genando/GenandoAPI/"]
RUN dotnet restore "Genando-dev/Code/Genando/GenandoAPI/GenandoAPI.csproj"
COPY . .
WORKDIR "/src/Genando-dev/Code/Genando/GenandoAPI"
RUN dotnet build "GenandoAPI.csproj" -c $configuration -o /app/build

FROM build AS publish
ARG configuration=Release
RUN dotnet publish "GenandoAPI.csproj" -c $configuration -o /app/publish /p:UseAppHost=false

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "GenandoAPI.dll"]
