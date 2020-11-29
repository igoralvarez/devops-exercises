# devops-exercises
1) Crear una red llamada lemoncode-challenge:
docker network create lemoncode-challenge

2)Creo un mongo asociado a la red lemoncode-challenge
No poner usaurio y contraseña:
docker run --name mymongo -p 27017:27017 -d --network lemoncode-challenge --mount source=my-mongo-data,target=/data/db mongo

En mongoDB Compass: mongodb://localhost:27017

Para hacer la build del backend despues de generar el Dockerfile en VSCode:

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

Lanzamos la instrucción para construir la imagen del backend:
docker build . -t backend

#Ejecutar la imagen metiendolo en la red lemoncode-challenge:
#Hay que pasarle el valor de MONGO_URI:
docker run -d --name my-backend --network lemoncode-challenge -p 5000:80 -e "MONGO_URI=mongodb://mymongo:27017" backend

Si accedo a la url http://localhost:5000/api/topics veo que esta respondiendo con una lista vacía.

#comprobar la red lemoncode-challenge para comprobar que tenemos el mongo y el backend en la misma red:
docker inspect lemoncode-challenge

Para hacer la imagen del front:
Genero el Dockerfile del front desde VSCode añadiendo la variable de entorno REACT_APP_API_URL:

FROM node:latest
WORKDIR /app
COPY ["package.json", "package-lock.json*", "npm-shrinkwrap.json*", "./"]
ENV NODE_ENV production
ENV REACT_APP_API_URL http://localhost:5000/api/topics
#COPY --from=dependencies /app/package.json ./
RUN npm install --production --silent && mv node_modules ../
COPY . .
EXPOSE 3000
CMD ["npm", "start"]

Lanzo la instrucción para construir la imagen del frontend:
docker build . --tag=frontend

Probar primero sin meter en la misma red el front:
docker run -d --name my-frontend -p 3000:3000 frontend

Si accedo a localhost:3000 veo la página de front levantada pero sin datos:

Me conecto a la base de datos:
docker exec -it mymongo mongo

Introduzco unos datos:
use TopicstoreDb
db.Topics.insert({
    Name: "my first topic"
})
db.Topics.insert({
    Name: "my second topic"
})
db.Topics.insert({
    Name: "another topic"
})

Si llamo al servicio veo que se cargan devuelve los datos.

Si refresco la aplicación se me cargan los datos.

TopicstoreDb
TopicsCollectionName