FROM mcr.microsoft.com/dotnet/sdk:9.0 AS build
WORKDIR /src

ARG ConnectionStrings__DefaultConnection
ARG REMOTE_GIT_LINK_BACKEND
ARG ASPNETCORE_Kestrel__Certificates__Default__Password

ENV ConnectionStrings__DefaultConnection=${ConnectionStrings__DefaultConnection}

RUN apt-get update && apt-get install -y git

RUN dotnet tool install --global dotnet-ef
ENV PATH="${PATH}:/root/.dotnet/tools"

COPY aspnetcert.pfx .
ENV ASPNETCORE_Kestrel__Certificates__Default__Password=${ASPNETCORE_Kestrel__Certificates__Default__Password}
ENV ASPNETCORE_Kestrel__Certificates__Default__Path=/app/aspnetcert.pfx

ARG CACHE_BUST=1
RUN git clone ${REMOTE_GIT_LINK_BACKEND} ./backend

WORKDIR /src/backend

RUN dotnet ef database update

RUN dotnet restore

RUN dotnet publish -c Release -o /app
RUN dotnet dev-certs https

FROM mcr.microsoft.com/dotnet/aspnet:9.0 AS runtime
WORKDIR /app

COPY --from=build /app .
ENTRYPOINT ["dotnet", "monkeyspeakbackend.dll"]