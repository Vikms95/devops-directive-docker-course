# use official images for language runtime as concrete as posible
FROM node:19.6-alpine

# specify working directory on init
WORKDIR /usr/src/app

# set env variables needed
ENV NODE_ENV production

# copy the exclusively required(here package and package-lock.json)
# also, first copying package json allows for not invalidating the cache of npm ci whenever the application
# code changes, allowing for bypassing npm ci if the dependencies have not changed
# otherwise, if we do COPY . ., and any application code changes (that which is now after the npm ci),
# on every application code change the package.json cache would get invalidated and Docker build would run npm
# ci everytime the image is build
COPY package*.json ./

# command run while the image is building, instead of doing raw npm i or npm ci, which is slow, cache 
RUN --mount=type=cache,target=/usr/src/app/.npm \
    npm set cache /usr/src/app/.npm && \
    npm ci --only=production

# node:19.6-alpine has a default user called node
USER node

# only allow the copy of files which user=node has access to
COPY --chown=node:node ./src .

# just for clarity, say which port we are exposing
EXPOSE 3000

# command to be used as soon as the image is built
CMD ["node","index.js"]