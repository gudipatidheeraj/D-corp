FROM nginx:alpine
RUN rm -rf /usr/share/nginx/html/*
WORKDIR /usr/share/nginx/html
COPY . .
ENV APP_NAME=D-CORP
LABEL maintainer="DHEERAJ"
EXPOSE 80
