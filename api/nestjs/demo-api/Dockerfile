FROM node:19-alpine AS development

WORKDIR /usr/src/app


RUN apk add --no-cache --virtual .gyp \
        python3 \
        make \
        g++

RUN apk add git


COPY ./package*.json ./

RUN npm install glob rimraf
RUN npm install --only=development

COPY . .
RUN npm run build

FROM node:19-alpine AS migration

WORKDIR /usr/src/app

RUN apk add --no-cache --virtual .gyp \
        python3 \
        make \
        g++

RUN apk add git

COPY ./package*.json ./

RUN npm config set legacy-peer-deps true
RUN npm install glob rimraf
RUN npm install

COPY . .
RUN npm run build


FROM node:19-alpine as production

ARG NODE_ENV=production
ENV NODE_ENV=${NODE_ENV}

WORKDIR /usr/src/app


RUN apk add --no-cache --virtual .gyp \
        python3 \
        make \
        g++

RUN apk add git


COPY ./package*.json ./

RUN npm install --only=production

COPY . .

COPY --from=development /usr/src/app/dist ./dist

CMD ["node", "dist/main"]
