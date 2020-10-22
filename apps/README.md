# Apps Directory

Apps should be placed here to be installed. Splunk allows `.spl` and `.tar.gz` formats.
Use the volumes section of `docker-compose` to bring these files in the container in any location you wish.
I use the `/tmp` directory. Set the `SPLUNK_APPS_URL` environment variable to match the path(s) you mount your apps to.