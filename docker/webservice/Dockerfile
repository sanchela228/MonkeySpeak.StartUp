FROM mcr.microsoft.com/dotnet/sdk:9.0 AS build
WORKDIR /src

RUN apt-get update && apt-get install -y git

# Устанавливаем EF Core CLI tools
RUN dotnet tool install --global dotnet-ef
ENV PATH="${PATH}:/root/.dotnet/tools"

ARG CACHE_BUST=1
RUN git clone https://gitlab.itstruct.ru/monkeyspeak/backend.git ./backend

WORKDIR /src/backend

RUN dotnet restore

RUN dotnet publish -c Release -o /app
RUN dotnet dev-certs https

FROM mcr.microsoft.com/dotnet/aspnet:9.0 AS runtime
WORKDIR /app

COPY aspnetcert.pfx .
ENV ASPNETCORE_Kestrel__Certificates__Default__Password=1234
ENV ASPNETCORE_Kestrel__Certificates__Default__Path=/app/aspnetcert.pfx

COPY --from=build /app .
ENTRYPOINT ["dotnet", "monkeyspeakbackend.dll"]