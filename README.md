# Demo Docker

The objective of this project is to try Docker with different applications and
in a clusterized solution, using Rancher for that.

## Requirements

* Vagrant

## Quick start guide

```
vagrant up
```

The preceding command will create three virtual machines using Vagrant:
* rancher-server: this machine will have both Rancher server and CLI client.
* node1: node that will join Rancher cluster and will be available to run
  containers on.
* node2: node that will join Rancher cluster and will be available to run
  containers on.

## Applications

So far there is just one application, called phpMyFAQ. To start it, after both
nodes have correctly joined to Rancher cluster:

```
vagrant ssh rancher-server
sudo su
cd /vagrant/applications/phpmyfaq
rancher up
```

That will bring phpMyFAQ up and available at either:

```
http://192.168.90.11:8080
http://192.168.90.12:8080
```

## Troubleshooting

If the preexisting MySQL dump haven't been correctly loaded the following errors
will start appearing after node1 is created.

```
==> node1: ERROR: http://192.168.90.10/v1 is not accessible (The requested URL returned error: 404 Not Found)
==> node1: ERROR: http://192.168.90.10/v1 is not accessible (The requested URL returned error: 404 Not Found)
```

To solve them:

```
vagrant ssh rancher-server
sudo su
docker exec -i rancher mysql cattle < /vagrant/src/rancher.sql
```

## License

This project is released under the terms of the MIT license. However, several
third-party products are used, so please refer to each product website to check
its license.

## Authors

* Author:: Leandro Di Tommaso <leandro.ditommaso@mikroways.net>
