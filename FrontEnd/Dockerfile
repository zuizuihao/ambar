FROM node:10.16.3-alpine as builder

#Specify a working directory
RUN mkdir -p /usr/src/app
WORKDIR /usr/src/app

#Copy the dependencies file
COPY package.json /usr/src/app

RUN apk add --no-cache python make g++

#Copy remaining files
COPY . /usr/src/app

#Install dependencies
RUN npm install && npm run deploy:prod

FROM nginx:latest

RUN apt-get update && apt-get install --no-install-recommends --no-install-suggests -y curl

# Set a timezone
ENV TZ=UTC
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

COPY default /etc/nginx/conf.d/default.conf
COPY --from=builder /usr/src/app/dist /usr/share/nginx/html

CMD echo $api > /usr/share/nginx/html/apiUrl.txt && nginx -g "daemon off;"

HEALTHCHECK --interval=5s --timeout=30s --retries=50 \
  CMD curl -f localhost:80 || exit 1