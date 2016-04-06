<properties
   pageTitle="Connect to an Azure Container Service cluster | Microsoft Azure"
   description="Connect to an Azure Container Service cluster by using an SSH tunnel."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, Containers, Micro-services, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="rogardle"/>


# Connect to an Azure Container Service cluster

The Mesos and Swarm clusters that are deployed by Azure Container Service expose REST endpoints. However, these endpoints are not open to the outside world. In order to manage these endpoints, you must create a Secure Shell (SSH) tunnel. Once an SSH tunnel has been established, you can run commands against the cluster endpoints and view the cluster UI through a browser on your own system. This document walks you through creating an SSH tunnel from Linux, OS X, and Windows.

>[AZURE.NOTE] You can create an SSH session with a cluster management system. However, we don't recommend this. Working directly on a management system exposes the risk for inadvertent configuration changes.   

## Create an SSH tunnel on Linux or OS X

The first thing that you do when you create an SSH tunnel on Linux or OS X is to locate the public DNS name of load-balanced masters. To do this, expand the resource group so that each resource is being displayed. Locate and select the public IP address of the master. This will open up a blade that contains information about the public IP address, which includes the DNS name. Save this name for later use. <br />

![Public DNS name](media/pubdns.png)

Now open a shell and run the following command where:

**PORT** is the port of the endpoint that you want to expose. For Swarm, this is 2375. For Mesos, use port 80.   
**USERNAME** is the user name that was provided when you deployed the cluster.  
**DNSPREFIX** is the DNS prefix that you provided when you deployed the cluster.  
**REGION** is the region in which your resource group is located.  

```
ssh -L PORT:localhost:PORT -N [USERNAME]@[DNSPREFIX]man.[REGION].cloudapp.azure.com -p 2200
```
### Mesos tunnel

To open a tunnel to the Mesos-related endpoints, execute a command that is similar to the following:

```
ssh -L 80:localhost:80 -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

You can now access the Mesos-related endpoints at:

- Mesos: `http://localhost/mesos`
- Marathon: `http://localhost/marathon`
- Chronos: `http://localhost/chronos`

Similarly, you can reach the rest APIs for each application through this tunnel: Marathon--`http://localhost/marathon/v2`. For more information on the various APIs that are available, see the Mesosphere documentation for the [Marathon API](https://mesosphere.github.io/marathon/docs/rest-api.html). See the [Chronos API](https://mesos.github.io/chronos/docs/api.html) and the
Apache documentation for the [Mesos Scheduler
API](http://mesos.apache.org/documentation/latest/scheduler-http-api/).

### Swarm tunnel

To open a tunnel to the Swarm endpoint, execute a command that looks similar to the following:

```
ssh -L 2375:localhost:2375 -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Now you can set your DOCKER_HOST environment variable as follows and continue to use your Docker command-line interface (CLI) as normal.

```
export DOCKER_HOST=:2375
```

## Create an SSH tunnel on Windows

There are multiple options for creating SSH tunnels on Windows. This document will describe how to use PuTTY to do this.

Download PuTTY to your Windows system and run the application.

Enter a host name that is comprised of the cluster admin user name and the public DNS name of the first master in the cluster. The **Host Name** will look like this: `adminuser@PublicDNS`. Enter 2200 for the **Port**.

![PuTTY configuration 1](media/putty1.png)

Select `SSH` and `Authentication`. Add your private key file for authentication.

![PuTTY configuration 2](media/putty2.png)

Select `Tunnels` and configure the following forwarded ports:
- **Source Port:** Your preference--use 80 for Mesos or 2375 for Swarm.
- **Destination:** Use localhost:80 for Mesos or localhost:2375 for Swarm.

The following example is configured for Mesos, but will look similar for Docker Swarm.

>[AZURE.NOTE] Port 80 must not be in use when you create this tunnel.

![PuTTY configuration 3](media/putty3.png)

When you are finished, save the connection configuration, and connect the PuTTY session. When you connect, you can see the port configuration in the PuTTY event log.

![PuTTY event log](media/putty4.png)

When you have configured the tunnel for Mesos, you can access the related endpoint at:

- Mesos: `http://localhost/mesos`
- Marathon: `http://localhost/marathon`
- Chronos: `http://localhost/chronos`

When you have configured the tunnel for Docker Swarm, you can access the Swarm cluster through the Docker CLI. You will first need to configure a Windows environment variable named `DOCKER_HOST` with a value of ` :2375`.

## Troubleshooting

### After creating the tunnel and browsing to the mesos or marathon url I get 502 Bad gateway..
The easiest way to resolve it is simply to delete your cluster and re-deploy it. Alternatively you can do the following to force Zookeeper to repair itself:

Login to each master and do the following:

```
sudo service nginx stop
sudo service marathon stop
sudo service chronos stop
sudo service mesos-dns stop
sudo service mesos-master stop 
sudo service zookeeper stop
```

Then once all services stopped on all masters:
```
sudo mkdir /var/lib/zookeeperbackup
sudo mv /var/lib/zookeeper/* /var/lib/zookeeperbackup
sudo service zookeeper start
sudo service mesos-master start
sudo service mesos-dns start
sudo service chronos start
sudo service marathon start
sudo service nginx start
```
Shortly after all services have restarted you should be able to work with your cluster as described in the documentation.

## Next steps

Deploy and manage containers with Mesos or Swarm.

- [Working with Azure Container Service and Mesos](./container-service-mesos-marathon-rest.md)
