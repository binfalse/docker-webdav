# Docker:WebDAV

This [Dockerfile](Dockerfile) compiles to an image that

* is based on a [debian:testing](https://hub.docker.com/r/_/debian/)
* has the latest [Apache webserver](http://httpd.apache.org/) installed
* has the [Apache WebDAV modules](https://httpd.apache.org/docs/2.2/mod/mod_dav.html) enabled

It is available through the Docker Hub at [binfalse/webdav](https://hub.docker.com/r/binfalse/webdav/).

## Running

To run the images you still need to deploy a proper virtual host configuration that serves a directory tree through webdav.
A sample configuration is included in the repository as [default.conf](default.conf).
You just need to mount it to `/etc/apache2/sites-available/000-default.conf` to apply it to a running container.
This example assumes that you also mount

* an `.htpasswd` for user authentication to `/dav/.htpassword`
* the actual directory tree to serve using WebDAV to `/dav/share`

Thus, a commandline may look like this:

    docker run --rm -it \
        -v default.conf:/etc/apache2/sites-available/000-default.conf \
        -v /path/to/dav-files:/dav/share:ro \
        -v /path/to/.htpasswd:/dav/.htpassword:ro \
        -p 8888:80 binfalse/docker-webdav

If you're using Docker-Compose:

    webdav:
        restart: always
        image: binfalse/docker-webdav
        volumes:
            - /path/to/dav-files:/dav/share
            - /path/to/.htpasswd:/dav/.htpassword:ro
            - /path/to/apache/default.conf:/etc/apache2/sites-available/000-default.conf
            - /path/to/letsencrypt/challenges:/acme/.well-known/acme-challenge:ro

Skip the *letsencrypt* part, if you're not using it ;-)

