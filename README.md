# Gitlab Server

This is mostly a docker compose file that'll let me spin up a GitLab server with a Postgres database and a Redis
server in separate containers.


### Stuff you'll need to do

The postgres container will run as user `postgres` with **UID** and **GID** of `999:999`.
When it spins up, it's going to attempt to take ownership (via chown) of the 
`/var/lib/postgresql/data` directory that we're mounting into the container.  Since we're
mounting an NFS share, the whole permission situation gets a little sticky.  Everyone appears 
happiest if the following accommodations are made:

* Create a `postgres` user on the NAS server with UID of **999** and a corresponding
  primary group with a GID of **999**.
* Create a shareable directory, volume or dataset that's owned by the `postgres` user
  and its corresponding group.  This is what we're going to share and it's postgres is
  going to store its data.
* Create an NFS share to share that freshly created directory, volume or dataset and map
  all incoming requests to the `postgres` user and its associated primary group.
  
Here's what this might look like if you're using [FreeNAS](http://www.freenas.org/):

#### Create a `postgres` user:
![create postgres user](images/add-user.png)

#### Create a [ZFS Dataset](https://doc.freenas.org/9.3/freenas_storage.html#create-dataset), set ownership and permissions:
![create ZFS dataset](images/dataset-permissions.png)

#### Create an NFS share, map all users and groups to **postgres**
![create NFS share](images/nfs-mapping.png)