![logo](logo.png)
#ELK-in-a-Box

##What is this?
[ELK stack](http://www.elasticsearch.org/overview/) (ElasticSearch, LogStash, Kibana) - containerized. 

Uses Docker and Fig to build and run a local ELK stack that can be used to aggregate and process logs in your local development environment.

It is **not** intended to be, by any stretch of the imagination, used to monitor your logs in production.

##Prereqs

You'll need:

- [Docker](https://docs.docker.com) or [Boot2docker](http://boot2docker.io) if you're on OSX or Windows (tho' I haven't actually tried running this on Windows) - see notes below for some workarounds
- [Fig](http:www.fig.sh) to build and run the stack

##Getting Started

###Building the images

`fig build`

###Running 

`fig up`

###Stopping

`fig stop`

###Removing the stopped containers

`fig rm`

…in other words, your regular [fig commands](http://www.fig.sh/cli.html)

##ElasticSearch

ElasticSearch is exposed on ports 9200 and 9300. The Docker image is built with CORS enabled.

##Logstash

You'll find the configuration file for Logstash in `conf`. By default, Fig will mount this directory into the Logstash container into `/etc/conf`. The provided `logstash.conf` will consume any log files in `/var/logs`, which Fig will mount the local `logs` directory into. 

Initially, the idea was to symlink any logs or log directories into this folder, but this doesn't appear to work with Docker. At the moment, the (admittedly shitty) workaround is to [mount](http://superuser.com/questions/842642/how-to-make-a-symlinked-folder-appear-as-a-normal-folder) the directory instead of linking it. This works ok for Linux, but not for OSX.

On Linux:

To mount:

    mount -o bind <source directory> <target directory>

To unmount:

    umount <target directory>

Once this is done, update your `logstash.conf` accordingly so that you can grab the input from the logs and apply any filters, etc. 

The other workaround is to update `fig.yml` and mount the paths that you're interested in - which, I suppose, isn't so bad (since you have to update `logstash.conf` anyway)…

##Kibana

Once the everything is up and running, you should be able to find [Kibana](http://www.elasticsearch.org/overview/kibana/) at [http://localhost:8888](http://localhost:8888) or, if you're using boot2docker, the IP of the Boot2Docker VM - for example [http://192.168.59.103:8888](http://192.168.59.103:8888).

##Boot2docker tips/workarounds

- You'll probabably need to be running boot2docker 1.3 minimum - at least on OSX, since shared directories actually work the way you'd [expect them to work](https://blog.docker.com/2014/10/docker-1-3-signed-images-process-injection-security-options-mac-shared-directories/).    
- Currently, Fig seems to have problems verifying the cert(?) - if you add an entry into your `/etc/hosts` file to add entry to resolve `boot2docker` to your boot2docker ip (i.e. `boot2docker ip`), it seems to make Fig happy

