![logo](logo.png)
#ELK-in-a-Box

##What is this?
[ELK stack](http://www.elasticsearch.org/overview/) (ElasticSearch, LogStash, Kibana) - containerized. 

Uses Docker and Docker-compose to build and run a local ELK stack that can be used to aggregate and process logs in your local development environment.

The setup uses a Logstash log shipper, which ships logs to a Logstash indexer via a Redis broker.

It is **not** intended to be, by any stretch of the imagination, used to monitor your logs in production.

##Prereqs

You'll need:

- [Docker](https://docs.docker.com)
- [Docker Machine](https://docs.docker.com/machine/) or  [Boot2docker](http://boot2docker.io) to provision Docker hosts
- [Docker Compose](https://docs.docker.com/compose/) to build and run the stack

##Getting Started

###Building the images

`docker-compose build`

###Running 

`docker-compose up`

###Stopping

`docker-compose stop`

###Removing the stopped containers

`docker-compose rm`

…in other words, your regular [docker-compose commands](https://docs.docker.com/compose/)

##Redis

The stack includes a Redis broker, which is exposed on port 6379. Logstash log shippers can be condocker-composeured to ship their logs to the Redis broker - this works both via container linking (which is what 
is done "out-of-the-box"), as well as from other hosts.

For example, to ship logs into the ELK stack from another host, you'd need to install Logstash (obviously) and run:

    logstash -e 'input {stdin {}} output { redis { host=>"[docker host ip or name]" data_type=>"list" key=>"logstash"}}'

#ElasticSearch

ElasticSearch is exposed on ports 9200 and 9300. The Docker image is built with CORS enabled.

##Logstash

You'll find the configuration files for Logstash shipper and indexer  in `conf`. By default, docker-compose will mount these directory into the Logstash container into `/etc/conf`. The provided `logstash.conf` for the shipper  will consume any log files in `/var/logs`, which docker-compose will mount the local `logs` directory into. The provided `logstash.conf` for the indexer just consumes log messages off of the Redis broker.

Initially, the idea was to symlink any logs or log directories into this folder, but this doesn't appear to work with Docker. At the moment, the (admittedly shitty) workaround is to [mount](http://superuser.com/questions/842642/how-to-make-a-symlinked-folder-appear-as-a-normal-folder) the directory instead of linking it. This works ok for Linux, but not for OSX.

On Linux:

To mount:

    mount -o bind <source directory> <target directory>

To unmount:

    umount <target directory>

Once this is done, update your `logstash.conf` accordingly so that you can grab the input from the logs and apply any filters, etc. 

The other workaround is to update `docker-compose.yml` and mount the paths that you're interested in - which, I suppose, isn't so bad (since you have to update `logstash.conf` anyway)…

Probably the more straightforward way to do this is to just run `logstash` and ship logs via the Redis broker.

##Kibana

Once the everything is up and running, you should be able to find [Kibana](http://www.elasticsearch.org/overview/kibana/) at [http://localhost:8888](http://localhost:8888) or, whatever the IP of your Docker host VM is (if you're using docker-machine or Boot2Docker to provision a VM).

##Note: re. Docker-Compose + Docker-Machine

Re. [docker-machine](https://docs.docker.com/machine/). There is a [known issue](https://github.com/docker/machine/issues/641) with host volume mounts when using the VMWare driver, which looks a lot like the Boot2Docker issue described above. Obviously volume mounts aren't going to be "a thing" with cloud drivers. 

