#Kibana Docker

This repo contains the Docker file and other bits and pieces for buidling a Docker image that serves [Kibana](git@github.com:hyleung/docker-kibana.git) (v3.1.2) via Nginx. 

Requires ElasticSearch to be running, with the usual ports (9200, 9300) exposed.

##Building the image

    docker build -t <tag> .

##Running

    docker run -d -p 8888:8888 <image name>

##Ports

The container exposes port 8888 (configured via `files/nginx.conf`). At the moment, only able to get this working if the same port is mapped when running (hence `-p 8888:8888`)
