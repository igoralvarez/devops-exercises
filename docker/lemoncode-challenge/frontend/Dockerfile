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
