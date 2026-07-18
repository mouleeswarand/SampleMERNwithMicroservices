FROM node:26-alpine3.23
WORKDIR /app
COPY . .
RUN npm install
ENV PORT=3002
ENV MONGO_URL="mongodb+srv://dmouleeswaran_db_user:3Dn8tFezX09qvY6t@cluster0.vsknysx.mongodb.net/profile"
EXPOSE 3002
CMD ["index.js", "node"]


