# devops-exercises
1) Crear una red llamada lemoncode-challenge:
docker network create lemoncode-challenge

2)Creo un mongo asociado a la red lemoncode-challenge
docker run --name mymongo -p 27017:27017 -d --network lemoncode-challenge --mount source=my-mongo-data,target=/data/db mongo

En mongoDB Compass: mongodb://localhost:27017

Para hacer la build del back despues de generar el Dockerfile en VSCode:

FROM mcr.microsoft.com/dotnet/core/aspnet:3.1 AS base
WORKDIR /app
EXPOSE 5000

FROM mcr.microsoft.com/dotnet/core/sdk:3.1 AS build
WORKDIR /src
COPY ["backend.csproj", "./"]
RUN dotnet restore "backend.csproj"
COPY . .
WORKDIR "/src/."
RUN dotnet build "backend.csproj" -c Release -o /app/build

FROM build AS publish
RUN dotnet publish "backend.csproj" -c Release -o /app/publish

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "backend.dll"]
Lanzo la instrucción para construir la imagen del backend:
docker build . -t backend

#Ejecutar la imagen metiendolo en la red lemoncode-challenge:

docker run -d --name my-backend --network lemoncode-challenge -p 5000:80 backend
Hay que pasarle el valor de MONGO_URI:
docker run -d --name my-backend --network lemoncode-challenge -p 5000:80 -e "MONGO_URI=mongodb://mymongo:27017" backend

#comprobar la red lemoncode-challenge para comprobar que tenemos el mongo y el backend en la misma red:
docker inspect lemoncode-challenge

Para hacer la imagen del front:
Genero el Dockerfile del front desde VSCode añadiendo la variable de entorno REACT_APP_API_URL:

FROM node:12.18-alpine
ENV NODE_ENV=production
ENV REACT_APP_API_URL http://localhost:5000/api/topics
WORKDIR /usr/src/app
COPY ["package.json", "package-lock.json*", "npm-shrinkwrap.json*", "./"]
RUN npm install --production --silent && mv node_modules ../
COPY . .
EXPOSE 80
CMD ["npm", "start"]


Lanzo la instrucción para construir la imagen del frontend:
docker build . --tag=frontend

Probar primero sin meter en la misma red el front:
docker run -d --name my-frontend -p 3000:3000 frontend

docker run -d --name my-frontend --network lemoncode-challenge -p 3000:3000 frontend




TopicstoreDb
TopicsCollectionName