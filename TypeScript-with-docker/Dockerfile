##############
#
# MULTI-STAGE
#
##############

### STAGE 1
#load base image
FROM node:16.13-alpine AS builder
# show stage build in console
RUN echo "started base build"
#set up container
WORKDIR /app
#copy package config
COPY package*.json ./
#copy ts config to workdir
COPY tsconfig*.json ./
#copy src folder
COPY src src
#copy prisma folder
COPY prisma prisma
#copy distribution folder to app folder
COPY swagger.json /app/
#copy environment variables
COPY .env /app/
#run prisma generate command
RUN npx prisma generate
#migrate to the database
RUN npx prisma migrate deploy
#run npm command to install all the module packages excluding dev dependencies and run build
RUN  npm ci && npm run build





# Build Stage 2
# This build takes the production build from staging build
#
FROM node:16.13-alpine
#set up container
WORKDIR /app
#copy package config
COPY package*.json ./
#run npm command to install all the module packages excluding dev dependencies
RUN npm install --production
#copy lighweight version from builder
COPY --from=builder /app/dist/ dist/
COPY --from=builder  /app/prisma/ prisma/
COPY --from=builder  /app/swagger.json /app/
#run prisma generate command
RUN npx prisma generate
#copy environment variables
COPY .env /app/
#expose application port
EXPOSE 4000
# show stage buildin console
RUN echo "app transfer completed successfully!"
#execute startup command
CMD ["node", "dist/src/app.js"] --start-service