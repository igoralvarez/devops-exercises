FROM mcr.microsoft.com/dotnet/core/sdk:3.1 AS build
WORKDIR /app
ENV MONGO_URI mymongouri
COPY ["backend.csproj", "./"]
COPY . .
RUN dotnet restore "./backend.csproj"
RUN dotnet build "backend.csproj" -c Release -o out

FROM build AS publish
RUN dotnet publish "backend.csproj" -c Release -o out

FROM mcr.microsoft.com/dotnet/core/aspnet:3.1
WORKDIR /app
EXPOSE 80
COPY --from=publish /app/out .
ENTRYPOINT ["dotnet", "backend.dll"]
