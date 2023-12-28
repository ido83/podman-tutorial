# docker run -it --rm -d -p 8080:80 --name web nginx
# Open your favorite browser and navigate to http://localhost:8080   You should see the following NGINX welcome page.
# Dockerfile Maintainer
MAINTAINER Ido N
FROM nginx:alpine
COPY *.html /usr/share/nginx/html/
## Copy a new configuration file setting listen port to 8080
COPY ./default.conf /etc/nginx/conf.d/
EXPOSE 8080
CMD ["nginx", "-g", "daemon off;"]
