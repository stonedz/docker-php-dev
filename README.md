
Fork of https://github.com/8ctopus/apache-php-fpm-alpine with my own mods.

Light Apline Linux docker container with php8-fpm and apache2. 

Below is the original README at the time of the fork.

## project description

A super light docker web server with Apache and php-fpm on top of Alpine Linux for development purposes

- Apache 2.4.48 with SSL
- php-fpm 8.0.8 or 7.4.21
- Xdebug 3.0.4 - debugger and profiler
- [SPX prolifer 0.4.10](https://github.com/NoiseByNorthwest/php-spx)
- composer 2.1.3
- zsh 5.8

The docker image size is 58 MB.

Note: for the web server plus MariaDB, check https://github.com/8ctopus/php-sandbox

## cool features

- php 8 or 7
- Apache and php configuration files are exposed on the host.
- Just works with any domain name.
- https is configured out of the box.
- All changes to config files are automatically applied (hot reload).
- Xdebug is configured for remote debugging (no headaches).

## start container

Starting the container with `docker-compose` offers all functionalities.

```shell
# start container (php 8 by default)
docker-compose up

# CTRL-Z to detach

# stop container
docker-compose stop
```

Alternatively the container can also be started with `docker run`.

```shell
# php 8
docker run -p 80:80 -p 443:443 --name web 8ct8pus/apache-php-fpm-alpine:latest

# php 7.4
docker run -p 80:80 -p 443:443 --name web 8ct8pus/apache-php-fpm-alpine:1.1.3

CTRL-Z to detach

docker stop container
```

## access website

    http://localhost/
    https://localhost/

The source code is located inside the `html` directory.

## set website domain name

To set the domain name to www.test.com, edit the environment variable in the docker-compose file

    environment:
      - DOMAIN=www.test.com

Add this line to the system host file. Editing the file requires administrator privileges.

    C:\Windows\System32\drivers\etc\hosts

    127.0.0.1 test.net www.test.net

## add https

To remove "Your connection is not private" nag screens, import the certificate authority file under ssl/certificate_authority.pem in the browser's certificates under Trusted Root Certification Authorities.

guide: https://support.globalsign.com/digital-certificates/digital-certificate-installation/install-client-digital-certificate-windows-using-chrome

## Xdebug debugger

This repository is configured to debug php code in Visual Studio Code.
To start debugging, open the VSCode workspace then select `Run > Start debugging` then open the site in the browser.

For other IDEs, set the Xdebug debugging port to 9001.

To troubleshoot debugger issues, check the `log\xdebug.log` file.

If `host.docker.internal` does not resolve within the container, update the xdebug client host within `etc\php\conf.d\xdebug.ini` to the docker host ip address.

```
xdebug.client_host          = 192.168.65.2
```

## Xdebug profiler

To start profiling, add the `XDEBUG_PROFILE` variable to the request as a GET, POST or COOKIE.

    http://localhost/?XDEBUG_PROFILE

Profiles are stored in the `log` directory and can be analyzed with tools such as [webgrind](https://github.com/jokkedk/webgrind).

## SPX profiler

To start profiling with SPX:

- Access the [SPX control panel](http://localhost/?SPX_KEY=dev&SPX_UI_URI=/)
- Check checkbox `Whether to enable SPX profiler for your current browser session. No performance impact for other clients.`
- Run script to profile
- Refresh the SPX control panel tab and the report will be available at the bottom of the screen. Click it to show the report in a new tab.

_Note_ Disable Xdebug debugger `xdebug.remote_enable` for accurate measurements.

## access container through command line

```shell
docker exec -it web zsh
```

## build docker development image

```shell
docker build -t apache-php-fpm-alpine:dev .
```

## extend docker image

In this example, we add the php-curl extension.

```shell
docker-compose up --detach
docker exec -it web zsh
apk add php-curl
exit
docker-compose stop
docker commit web apache-php-fpm-alpine-curl:dev
```

To use this image, update the reference in `docker-compose.yml`.

## more info on php-fpm

    https://php-fpm.org/about/

## notes

In Windows hot reload doesn't work with WSL 2, you need to use the legacy Hyper-V.
