FROM nginx:latest

COPY ./nginx.conf /etc/nginx/conf.d/nginx.conf

RUN unlink /etc/nginx/conf.d/default.conf && \
	apt-get update && \
	apt-get install -y apt-utils

CMD ["nginx", "-g", "daemon off;"]
