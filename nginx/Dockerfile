# docker build -t rightrun/nginx:1 .
FROM nginx:latest

# Fixes some weird terminal issues such as broken clear / CTRL+L
ENV TERM=linux
# Ensure apt doesn't ask questions when installing stuff
ENV DEBIAN_FRONTEND=noninteractive
COPY default.conf /etc/nginx/conf.d/default.conf

EXPOSE 8000
