# nodejs-compose

Pretty basic example of using docker compose to deploy a NodeJS into a very simple production environment.

We are deploying:

* [Ghost](https://ghost.org/) blogging platform
* [nginx](http://nginx.org/en/) web server

# setup

We are going to deploy this host using docker-machine, using the Digital Ocean service to host our server.

To create a configured docker host on Digital Ocean run the following command. This creates a reasonably sized virtual machine in the San Francisco Data center using the 14.04 LTS release of Ubuntu Linux.

```
docker-machine create -d "digitalocean" \
    --digitalocean-region "sfo1" --digitalocean-size "1gb" \
    --digitalocean-image "ubuntu-14-04-x64" ghostbox-02
```

Now to setup your shell to use this docker server.
```
eval $(docker-machine env ghostbox-02)
```

# deployment

To deploy the ghost blogging platform to this newly minted docker machine we are going to use docker compose as follows.

```
docker-compose up
```

If all is well we can CTRL+c that and then run it for real.

```
docker-compose up -d
```

Get the IP address of the docker machine.

```
docker-machine ip ghostbox-02
```

Check the site is working.

```
open http://$(docker-machine ip ghostbox-02)
```

# security updates

Once the host is up lets just enable automatic security updates.

To access the host you can use docker-machine.

```
docker-machine ssh ghostbox-02
```

Then once logged in run the following command and select Yes.

```
dpkg-reconfigure -plow unattended-upgrades
```

Apply some upgrades.

```
unattended-upgrade -d
```

# monitoring

For standalone servers I want to run I typically just use new relic server monitoring.

```
dpkg-reconfigure -plow unattended-upgrades
echo deb http://apt.newrelic.com/debian/ newrelic non-free >> /etc/apt/sources.list.d/newrelic.list
wget -O- https://download.newrelic.com/548C16BF.gpg | apt-key add -
apt-get update
apt-get install newrelic-sysmond
nrsysmond-config --set license_key=<Provided in web portal>
/etc/init.d/newrelic-sysmond start
```

# todo

* Add [letsencrypt](https://letsencrypt.org/) example
* Setup some log streaming and metrics
* S3 Backup Container
