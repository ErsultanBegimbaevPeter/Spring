# Stage 1: Базовый образ
FROM mcr.microsoft.com/dotnet/aspnet:6.0 AS base
WORKDIR /app
EXPOSE 80
EXPOSE 443

# Настройка часового пояса
ENV TZ=Asia/Almaty
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# Stage 2: Сборка проектов
FROM mcr.microsoft.com/dotnet/sdk:6.0 AS build
WORKDIR /src

# Копируем проект TerminalApi и восстанавливаем зависимости
COPY ["NeoTerminal/TerminalApi.csproj", "NeoTerminal/"]
COPY ["NeoTerminal/nuget.config", "NeoTerminal/"]

WORKDIR /src/NeoTerminal
RUN dotnet restore "TerminalApi.csproj" --configfile ./nuget.config

# Копируем остальные файлы и собираем проект TerminalApi
COPY . .
RUN dotnet build "TerminalApi.csproj" -c Release -o /app/build

# Stage 3: Публикация проекта TerminalApi
FROM build AS publish
RUN dotnet publish "TerminalApi.csproj" -c Release -o /app/publish

# Stage 4: Финальный образ для TerminalApi
FROM base AS final-api
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "TerminalApi.dll"]

# Stage 5: Финальный образ для TerminalWorker
FROM mcr.microsoft.com/dotnet/sdk:6.0 AS build-worker
WORKDIR /src

# Копируем проект TerminalWorker и восстанавливаем зависимости
COPY ["TerminalWorker/TerminalWorker.csproj", "TerminalWorker/"]
COPY ["NeoTerminal/nuget.config", "TerminalWorker/"]

WORKDIR /src/TerminalWorker
RUN dotnet restore "TerminalWorker.csproj" --configfile ./nuget.config

# Копируем остальные файлы и собираем проект TerminalWorker
COPY . .
RUN dotnet build "TerminalWorker.csproj" -c Release -o /app/build

# Stage 6: Публикация проекта TerminalWorker
FROM build-worker AS publish-worker
RUN dotnet publish "TerminalWorker.csproj" -c Release -o /app/publish

# Stage 7: Финальный образ для TerminalWorker
FROM base AS final-worker
WORKDIR /app
COPY --from=publish-worker /app/publish .
ENTRYPOINT ["dotnet", "TerminalWorker.dll"]
