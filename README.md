# Redmine Docker Image for Raspbian

This docker image based on [resin/rpi-raspbian](https://hub.docker.com/r/resin/rpi-raspbian/) image from resin to build for ARM.
This build use by default a PostgresSQL server. Docker image builds a redmine with apache2 passenger.

## Prerequisite

* PostgresSQL server for docker ARM
  * [tobi312/rpi-postgresql](https://hub.docker.com/r/tobi312/rpi-postgresql/)

## Build Container

```
git clone https://github.com/Nepitwin/DockerRedminePI.git
cd DockerRedminePI
```

Before you build your container modify database.yml with correct databas credentiels.

```
production:
  adapter: postgresql
  database: redmine
  host: <HOST_IP>
  port: 5432
  username: <Username>
  password: <Password>
  encoding: utf8
```

Build container after database.yml is configured.

```
docker build --rm=true --tag=pi/redmine .
```

After build is complete create a docker container and expose port 80.

```
docker run -d -p 80:80 pi/redmine
```

After running DokuWiki can be configured by following URL. Default username is 'admin' and password is 'admin'.
  - www.DockerIP/redmine/

## License

The MIT License (MIT)

Copyright 2017 Andreas Sekulski

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
