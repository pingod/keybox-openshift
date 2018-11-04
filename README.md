# KeyBox in Docker and OpenShift

[![Docker Build Status](https://img.shields.io/docker/build/tobru/keybox.svg)](https://hub.docker.com/r/tobru/keybox/)
[![Docker Automated builid](https://img.shields.io/docker/automated/tobru/keybox.svg)](https://hub.docker.com/r/tobru/keybox/)

This repository provides a Dockerfile and an OpenShift template
for [KeyBox](https://github.com/skavanagh/KeyBox).

**Please note**: KeyBox runs without TLS! Terminating TLS is the job of the
loadbalancer / reverse proxy in front of it (like the OpenShift router).

## Docker run

Example for running KeyBox in Docker:

```
docker build -t local/keybox .
docker run --name keybox \
  -d \
  --user 1000001:root \
  -e DB_PASSWORD=blablubb \
  -p 8080:8080 \
  -v keybox_data:/opt/keybox/jetty/keybox/WEB-INF/classes/keydb \
  tobru/keybox
```

## Configuration

On startup the KeyBox configuration file `KeyBoxConfig.properties` is
generated by `dockerize`. The only mandatory paramater is
`DB_PASSWORD` or else the container won't start properly.
To see the defaults and available parameters, have a look into
`KeyBoxConfig.properties.tpl`.

## Deploy on OpenShift

### 0 Create OpenShift project

Create an OpenShift project if not already provided by the service

```
PROJECT=keybox
oc new-project $PROJECT
```

### 1 Deploy KeyBox

```
oc process -f https://raw.githubusercontent.com/tobru/keybox-openshift/master/keybox-template.yaml | oc -n $PROJECT create -f -
```

### 2 Configure KeyBox

Navigate to the generated URL and login with the KeyBox default credentials.
Change the admin password now!

**Hint**

The template is configured with an `emptyDir` storage. You might want to
replace this with a persistent storage volume (PVC) or else you'll lose
your configuration when the Pod restarts.

## Maintenance of this Repo

This repo triggers an automated Docker Hub build and always builds the latest
tag from master - there is no release process involved. If a new KeyBox
release is available, the Dockerfile needs to be updated and pushed.
This will only be done occasionally. So if it's not done in time, feel
free to send a PR - this can speed up things.
