# Nginx reverse proxy  
  
Simple docker + nginx configuration to make reverse proxy with HTTPS support  

## How to use  
__1.__ Place `docker-compose.yml` and `nginx.tmpl` in one folder.  
__2.__ Create external network nginx-proxy
```bash
$ docker network create nginx-proxy
```
__3.__ And start service with 
```bash
$ docker-compose up -d
```
__4.__ Then start service container(s).

__IMPORTANT:__ Proxy containers and service containers **must be in common external network** (nginx-proxy in example).  

__________
## Example of docker-compose file for service  
```yaml
version: '3'

services:
  flask:
    container_name: flask
    restart: always
    build:
      context: .
      dockerfile: Dockerfile-flask
    networks:
      - nginx-proxy
    environment:
      - VIRTUAL_HOST=domain.com,www.domain.com
      - LETSENCRYPT_HOST=domain.com
      - LETSENCRYPT_EMAIL=admin@domain.com
      - VIRTUAL_PORT=5000
    volumes:
      - "./:/app"
    entrypoint: /flask-entrypoint.sh
    expose:
      - 5000

networks:
  default:
    external:
      name: nginx-proxy

```

## List of environment parameters:  
* `VIRTUAL_HOST=domain.com,www.domain.com` - your domain (add www alias if you need to)  

* `VIRTUAL_PORT=5000`  - port of your service (must be exposed)

* `LETSENCRYPT_HOST=domain.com` - host to generate LetsEncrypt sertificate (matches your domain)  

* `LETSENCRYPT_EMAIL=admin@domain.com` - email to generate LetsEncrypt sertificate  

__________
### Source  
Repository was based on [this article](https://medium.com/@francoisromain/host-multiple-websites-with-https-inside-docker-containers-on-a-single-server-18467484ab95) by [François Romain](https://francoisromain.com/).
