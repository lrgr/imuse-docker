# iMuSE Docker Configuration

## Setup
To run [iMuSE](https://github.com/lrgr/imuse) and [iMuSE Server](https://github.com/lrgr/imuse-server) locally using docker:

Install Docker and Docker Compose:
- [Docker](https://docs.docker.com/) [for Mac](https://docs.docker.com/docker-for-mac/install/) or [for Windows](https://docs.docker.com/docker-for-windows/install/)
- [Docker Compose](https://docs.docker.com/compose/install/) (should come bundled with Docker)

Clone this repository
```
git clone https://github.com/lrgr/imuse-docker.git
```

Enter into this repository
```
cd imuse-docker
```

### The data directory
Notice the `obj` folder within `imuse-docker`.
This is where iMuSE-server will look for data.
Run `pwd` in this directory and make note of the absolute path.

The metadata files in the `obj` directory can be updated to specify custom signatures and/or mutation datasets. Please see the [wiki](https://github.com/lrgr/imuse-docker/wiki) for detailed instructions about file formats. 

### The database directory
Run the following command to create a `mysql` folder within `imuse-docker`:

```sh
mkdir mysql
```

This is where iMuSE-server will store the database used for saving/sharing iMuSE sessions and history.
Run `pwd` in this directory and make note of the absolute path.

### The docker-compose file
Notice the `docker-compose.yml` file within `imuse-docker`.

Update this file:

- Change the backend volumes path to the absolute path of your `obj` directory
- Change the db volumes path to the absolute path of your `mysql` directory

```
version: '2'
services:
  frontend:
    depends_on:
    - backend
    image: lrgr/imuse:latest
    ports:
    - "8000:80"
    environment:
    - IMUSE_SERVER_URL=http://localhost:8100/
  backend:
    depends_on:
    - db
    image: lrgr/imuse-server:latest
    ports:
    - "8100:80"
    links:
    - db
    volumes:
    - /path/to/your/obj:/obj                    <----------- UPDATE
  db:
    image: mysql:5.7
    environment:
    - MYSQL_ROOT_PASSWORD=root
    - MYSQL_DATABASE=imuse
    - MYSQL_USER=imuse
    - MYSQL_PASSWORD=imuse
    command: --default-authentication-plugin=mysql_native_password
    volumes:
    - /path/to/your/mysql:/var/lib/mysql        <----------- UPDATE
```

### Pull
Pull down the two docker containers specified in the docker-compose file
```
docker-compose pull
```


## Running

### Up
Run the two docker containers
```
docker-compose up
```

### Up - daemonized
Run the two docker containers in the background
```
docker-compose up -d
```
