
# Raspberry Pi Monitoring with Prometheus, Grafana & Cadvisor

First of all, let me say this project was created for a personal use. This is a basis for further work (running another containers on Pi). The Pi is located on a local network. Maybe someone will find the setup usefull, thus I'm publishing it.

## References

This work is derived mainly from official documentations as well as from the following sources:
* https://github.com/vegasbrianc/prometheus
* https://github.com/google/cadvisor/issues/1236#issuecomment-578093121

## Prerequisites

* Raspberry Pi 4B (4GB RAM)  
* Raspberry Pi OS Lite (Debian Buster)
* SSH access  
* proper firewall setup (open port 22 for SSH access from your machine)
* docker & docker-compose  

## Setup

### Copy the files to your Pi

Clone the repo to your Pi (users `home` dir) or to your host machine and then SCP to the Pi:

```
scp -r -i <location_of_your_ssh_key> <location_of_your_src_files> <user>@<ip>:<location>
```

e.g.:
```
scp -r -i ~/.ssh/id_rsa ~/rpi-monitoring/* pi@192.168.1.20:/home/pi/
```

### Create nginx certificates

You need to create self signed certificate in order to use HTTPS with you exposed proxy. You can use any guide, e.g. the one from [DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-create-a-self-signed-ssl-certificate-for-nginx-in-ubuntu-16-04).

The `.crt.` and `.key` files should reside in `./docker/proxy/`.

## Starting the services

SSH into your Pi using

```
ssh <user>@<ip> -i <location_of_your_ssh_key>
```

e.g.:
```
ssh pi@192.168.1.20 -i ~/.ssh/id_rsa
```

Next verify the files are in place (e.g. `ls -al`). You should see the copied files.
Then in the location of your `docker-compose.yml` file run:

`docker-compose up -d`  

and wait until the images are built and containers have started. Note: the Cadvisor image build will take a while.

Verify the containers are operational:

```
docker ps
```

## Accessing the proxy

The proxy is exposed only to the localhost, thus we can reach it via ssh tunel from our machine:

```
ssh -N -L 443:127.0.0.1:443 -i <location_of_your_ssh_key> <user@<ip>
```

e.g.:
```
ssh -N -L 443:127.0.0.1:443 -i ~/.ssh/id_rsa pi@192.168.1.20
```

Having the tunnel running, you can visit the proxy:

```
https://127.0.0.1/grafana
https://127.0.0.1/prometheus/graph
https://127.0.0.1/cadvisor/containers/
https://127.0.0.1/node-exporter/metrics
```

## Post Setup

When setting up a Grafana Data Source, use docker provided DNS resolution of a custom bridge networking (`http://prometheus:9090`). 

## References & Useful Links

* https://github.com/thundermagic/rpi_monitoring_with_prometheus
* https://github.com/prometheus/node_exporter#collectors
* https://grafana.com/docs/grafana/latest/installation/docker/#migration-from-a-previous-version-of-the-docker-container-to-5-1-or-later
* https://github.com/grafana/grafana/issues/14629#issuecomment-562743297
* https://devconnected.com/how-to-install-prometheus-with-docker-on-ubuntu-18-04/
* https://github.com/google/cadvisor/issues/1236#issuecomment-578093121
* https://dev.to/polarbit/how-docker-container-networking-works-mimic-it-using-linux-network-namespaces-9mj
* https://argus-sec.com/docker-networking-behind-the-scenes/