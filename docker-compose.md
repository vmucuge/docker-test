# Docker Exercise


###Description:

Deploy a loadbalanced ruby application backed by a redis database, using docker.

You will have to create the needed Dockerfiles for the ruby app, the load balancer (depending on the solution you choose, perhaps you don't need this part.) and a redis docker.

The diagram: 
    
                              +--------------+
                    +--------->              +----------+
                    |         |   RUBY APP   |          |
             +------+-+       |              |     +----v------+
             |        |       +--------------+     |           |
    +-------->   LB   |                            |   REDIS   |
             |        |                            |           |
             +------+-+       +--------------+     +----^------+
                    |         |              |          |
                    |         |   RUBY APP   |          |
                    +--------->              +----------+
                              +--------------+


The ruby app just needs those gems:

   - redis 
   - sinatra

Just run `gem install redis sinatra`.

The application listens on port 4567/http and accepts to env vars: 

- REDIS_HOST: redis container host (default: 127.0.0.1)
- REDIS_PORT: redis container port (default: 6379)


###You must use:

  - Docker

###You can use:

  - Docker Compose
  - Openshift (www.openshift.org) (extra points!) 
  - k8s (www.kubernetes.io) (extra points)
  - Any scheduler you can think of that runs on top of linux... 

###Requirements: 

   - Full description of how to get the application running using the method you choose.
   - A github repo with your dockerfiles, compose files, or all the info needed.
   - You must provide a single endpoint to access the application, we will use `curl http(s)://ENDPOINT_PROVIDED` 
