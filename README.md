# docker-test
Docker Test 1LB-2SINATRA-1REDIS

###Description:

Deploy a loadbalanced ruby application backed by a redis database, using docker.

The diagram: 
    
                              +--------------+
                    +--------->              +----------+
            PUBLIC |         |   RUBY APP   |          |
             +------+-+       |   TCP/4567   |     +----v------+
      PUBLIC |        |       +--------------+     |           |
    +-------->   LB   |         INTERNAL           |   REDIS   |
             | TCP/80 |                            |  TCP/6579 |
             +------+-+       +--------------+     +----^------+
                    |         |              |          |
                    |         |   RUBY APP   |          |
                    +--------->   TCP/4567   +----------+
                              +--------------+


Requirements:
 - Linux Docker Host or Docker Machine available (Tested on OSX/Windows - Docker Toolbox)
 - Docker Compose (Docker Toolbox)
 - Basic usage of scripting commands.

 * Software and libraries necessary (in the containers)
   - redis 
   - sinatra
   - haproxy
   - ruby
   - development tools

### Creating the containers
 For this test we will work on different ways:
 - Dockerfiles
 - docker-compose
 
The idea is to get more familiar with the usage of some docker and docker-compose commands:
 - docker build
 - docker tag
 - docker run
 - docker-compose
 
For the sake of this test, the  containers will be running in a single host, in Docker Machine using Virtualbox, so please if you are running that on Windows or OSX, install it first from:
 OSX - https://github.com/docker/toolbox/releases/download/v1.11.1b/DockerToolbox-1.11.1b.pkg
 WINDOWS - https://github.com/docker/toolbox/releases/download/v1.11.1b/DockerToolbox-1.11.1b.exe
 
 Then create your dev/test machines with the following command:
 $ docker-machine create -d virtualbox docker-machine-01
 Once the machine is running, load the variables to your current terminal session:
 ```
 $ eval $(docker-machine env docker-machine-01)
 $ docker-machine ls
   NAME                ACTIVE   DRIVER       STATE     URL                         SWARM   DOCKER    ERRORS
   docker-machine-01   *        virtualbox   Running   tcp://192.168.99.100:2376           v1.11.1
 ```






