# ExploSig Docker Configuration

## Setup
To run [ExploSig](https://github.com/lrgr/explosig) and [ExploSig Server](https://github.com/lrgr/explosig-server) locally using docker:

Install Docker and Docker Compose:
- [Docker](https://docs.docker.com/) [for Mac](https://docs.docker.com/docker-for-mac/install/) or [for Windows](https://docs.docker.com/docker-for-windows/install/)
- [Docker Compose](https://docs.docker.com/compose/install/) (should come bundled with Docker)

Clone this repository
```
git clone https://github.com/lrgr/explosig-docker.git
```

Enter into this repository
```
cd explosig-docker
```

### The data directory
Notice the `obj` folder within `explosig-docker`.
This is where ExploSig Server will look for data.
Run `pwd` in this directory and make note of the absolute path.

The metadata files in the `obj` directory can be updated to specify custom signatures and/or mutation datasets. Please see the [wiki](https://github.com/lrgr/explosig-docker/wiki) for detailed instructions about file formats. 

### The database directory
Run the following command to create a `mysql` folder within `explosig-docker`:

```sh
mkdir mysql
```

This is where ExploSig Server will store the database used for saving/sharing ExploSig sessions and history.
Run `pwd` in this directory and make note of the absolute path.

### The docker-compose file
Notice the `docker-compose.yml` file within `explosig-docker`.

Update this file:

- Change the backend volumes path to the absolute path of your `obj` directory
- Change the db volumes path to the absolute path of your `mysql` directory

```
version: '2'
services:
  frontend:
    depends_on:
    - backend
    image: lrgr/explosig:latest
    ports:
    - "8000:80"
    environment:
    - EXPLOSIG_SERVER_URL=http://localhost:8100/
  backend:
    depends_on:
    - db
    image: lrgr/explosig-server:latest
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
    - MYSQL_DATABASE=explosig
    - MYSQL_USER=explosig
    - MYSQL_PASSWORD=explosig
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
