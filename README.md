# Run Splunk, Splunk ES, and the Resilient Add-on with Docker

## Basic Usage

If you're just looking to test this out with an artitrary version of splunk, there is very little do do.

First things first you will need to obtain .spl files for any apps you wish to install.
Place those spl/tgz packages in the `./apps` directory here.

Once you have the .spl files in place, edit the below section of `docker-compose.yml` and change the two `./apps/` paths under volumes to match the names of the .spl files you obtained. The syntax is as follows: `- ./local/path/to/app.spl:/path/to/place/file/in/container/app.spl`. I've found that relative paths work best for the host and absolute paths work best for the container.

```yml
volumes:
  - ./apps/resilient-add-on.spl:/tmp/res.spl
  - ./apps/splunk-enterprise-security_620.spl:/tmp/es.spl
```

Now you're ready to rock! Run `docker-compose up` to build the splunk image. Splunk ES, the Resilient Add-on. You can configure a connection to the Resilient instance of your choice. Note that if you are accessing Resilient on localhost, use the special domain `host.docker.internal` when you setup the Resilient Add-on in Splunk. This special domain is resolved by Docker to the IP of it's host, in this case your machine.

## Advanced configuration
### Splunk version

If there is a specific version of Splunk that you would like to work with, all you need to do is change the tag in `docker-compose.yml` from `image: splunk/splunk:latest` to the desired version tag. See what's available on [Docker Hub](https://hub.docker.com/r/splunk/splunk/)

### Container Name

If desired, the name of the container and it's alias and be changed so you can start a second container with a new name instead of overwriting the original.

### sever.conf Settings

Taking a look at `docker-compose.yml` you'll notice there is one additional volume mount aside from the two apps. Splunk uses this file to do some basic server configuration at startup and whenever you start/restart `splunkd`. In this case, I've used it to start the server with `python3`. Another cool thing that mounting in this file will provide is a static server name. This is useful for the master/slave licensing relationship. A new slave license is indexed every time you start up a splunk instance with a new server name pointing to our master license. I'm hoping that mounting in the `server.conf` file with `serverName = brian-docker` with limit the amount of servers that appear under our master to reduce clutter. I have not yet had a chance to explore what happens if you spin up two containers with the same server name at the same time. Maybe change this setting if you plan to use this.

#### Note on server.conf

When you mount in a `server.conf` from your machine, that file does not like to be editted from an interactive shell inside the container. I've tried and it broke `splunkd`. If you have changes that you need to make, edit `server.conf` locally.

### Port Forwarding

Use the `ports:` block at the bottom of `docker-compose.yml` if you want to utilize a different port mapping scheme bewtween your container and machine. You'll need to do this if you are running multiple containers, as no two containers can map to the same port on the host. You can randomize the port mapping by the following
```yml
ports:
  - 8000:8000
```
By not specifying a specfic port to which port 8000 on the container, Docker will select one for you. After starting the container, run `docker ps` to show your new container and it's port mapping.
