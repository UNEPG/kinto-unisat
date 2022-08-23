# Kinto-UniSat

A kinto clone, yet modified to run on UniSat ARM processors. Kinto is A generic JSON store ( in UniSat we deliver and synch telemetry data ) with sharing and synchronisation capabilities.

`kinto-unisat` is based on `kinto` from Mozilla Foundation which originally only supports 64 bit Intel and AMD CPUS. To make it run also on ARM devices which is used on UniSat devices, we have modified some portion of the code, rebuild and also created and pushed the public container images which can be pulled from [here](https://hub.docker.com/r/azataiot/kinto-arm).

![CleanShot 2022-08-23 at 08.28.17](https://raw.githubusercontent.com/azataiot/images/master/2022/08/upgit_20220823_1661232513.png)

NOTES:  Theoritically, you can use `kinto-unisat`  to build your own version for any of your ARM devices, and we have mainly tested on `Raspberry Pi 3/3B+, Raspberry Pi 4B and RPi CM4 with running 64 bit Debian)

## Requirements

On UniSat kinto runs on top of containers such as `docker` or `podman`. 

- Docker (Docker Compose)
- Python3.9 + (Tested)
- DB:
  - memcached:1
  - postgres:14

## Basic Comcepts About Kinto

For detailed info, please refer to the original [documentation](https://docs.kinto-storage.org/en/latest/index.html) :

### Concepts

Basically, *Kinto* is a service where client applications can store and retrieve JSON data.

In order to provide synchronisation and sharing features for these data, *Kinto* introduces some basic concepts.

*Kinto* objects in brief:

| Object         | Description                                                  |
| -------------- | ------------------------------------------------------------ |
| **bucket**     | Buckets can be seen as namespaces: collection names won’t collide if stored in different buckets. |
| **collection** | A collection of records                                      |
| **record**     | The actual stored data                                       |
| **group**      | A named list of [principals](https://docs.kinto-storage.org/en/latest/api/1.x/permissions.html#api-principals) to define permissions. |

###  Buckets, Collections, and Records

A **record** is the smallest unit of data. By default, there is no schema, and the JSON can contain anything.

A **collection** is a group of records. Records are manipulated as a list and can be filtered or sorted. Clients can obtain the list of changes that occured on the collection records since a certain revision (e.g. *last synchronisation*). A JSON schema can optionally be defined on the collection.

A **bucket** is an abstract notion used to organize collections and their permissions.

![_images/concepts-general.png](https://raw.githubusercontent.com/azataiot/images/master/2022/08/upgit_20220823_1661232889.png)

Every kind of object manipulated by *Kinto* shares some common properties:

- A unique identifier.
- A revision number, automatically incremented on change.
- A set of permissions.

## Unisat Use Case

According to the Telemetry over Terminal Socket Based Data Publishing and Subscription Protocol ([PSoTT](https://azat.cc/2022/08/17/telemetry-over-terminal-socket-based-data-publishing-and-subscription-protocol-(psott).html) protocol we need two database system, and synchronize them periodically, in the case of UniSat Data Provider/Subscription network, this is achived by using kinto (kinto-unisat/kinto-arm) more specifically. 

![CleanShot 2022-08-17 at 10.03.26](https://raw.githubusercontent.com/azataiot/images/master/2022/08/upgit_20220823_1661233099.png)

The IoT device (UniSat Nano-satellite) is connected with the local publisher (A RPi 3B), local publisher runs two developed backend programs :

- Dockernized kinto backend  (kinto works in this layer)
- Python based backend for logic (also named as unisat-data-local : https://github.com/UNEPG/unisat_data_local)

Python based backend includes 3 paralell threads:

- Serial Communication Thread (read and decode data from the serial port (connected to the IoT device))
- Data handling Thread (communicates with local and remote database services (kinto instances running locally and remotely on the server))
- Data Synching (in case of network error or temporary failfure, data is writeed to local database and synced to the cloud when network availale again)

![CleanShot 2022-08-23 at 09.04.36](https://raw.githubusercontent.com/azataiot/images/master/2022/08/upgit_20220823_1661234724.png)





 

