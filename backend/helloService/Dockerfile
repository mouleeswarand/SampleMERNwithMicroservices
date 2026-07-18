FROM node:26-alpine3.23
WORKDIR /app 
COPY . .
RUN npm install
ENV PORT=3001
EXPOSE 3001
CMD [ "index.js", "node" ]

