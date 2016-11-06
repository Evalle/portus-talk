# Portus' playground
It is possible to use docker-compose to spin up a small development/demo environment. For that, you need docker-compose 1.6 or later. The environment consists of three Docker containers linked together:

- **web**: this is the container running Portus. It's based on the official rails Docker image.
- **db**: this is the container running the database required by Portus. It's based on the official mariadb Docker image.
- **registry**: this is a the container running the latest version of the Docker registry (aka distribution). It's based on the official registry Docker image.

This environment is meant for development/playground purposes. Known limitations:

- Portus uses a public and passwordless certificate stored inside of this git repository.
- Registry is not secured, everything is transmitted over http.
- The Docker host has two open two ports in order to make Portus and the registry reachable.

### Initial setup

First of all ensure you have docker-compose installed. Note that this setup is known to fail on NFS. Then do:
```
$ ./compose-setup.sh -e my.hostname.ip
```
The -e parameter provides this setup the IP to be used as the hostname. We used to deduce this value, but this proved to be prone to lots of bugs from different setups. Instead, we now require you to pass it for us. One usually correct value is the IP as given by the ip address show docker0 command.

This will:

- Download the rails Docker image from the Docker Hub.
- Build the portus_web Docker image.
- Download the mariadb Docker image from the Docker Hub.
- Start the portus_db container and link it against a running instance of the web container.
- Download the registry Docker image from the Docker Hub
- Start the portus_registry container and link it against a running instance of the web container.
- Initialize Portus' database: database creation, run the migrations.
Once the setup is done there are a couple of manual operations to perform on Portus:

- Create your account. The first user is going to be an administrators. Administrators are special users, they can do everything, including pushing to the global namespace of your registry.
- Associate your on-premise instance of the Docker registry.
The setup script will print all these informations on the console.

Portus' UI will be accessible on http://<docker host>:3000. The registry will be listening on `:5000.

### Normal usage

Once the initial setup is done you can use docker-compose to handle everything.

You can do:

- docker-compose up to start the whole environment
- docker-compose stop to stop the whole environment
All the changes (database, registry) are stored into Docker volumes.

### Changing the host

Whenever you change the host, remember to remove two generated files: docker/environment and docker/registry/config.yml. Not doing so is a common pitfall, and it will result in Portus not working reliably. Instead, you will need to remove these files and execute the compose-setup.sh script again.

### Remote Host

The compose-setup.sh script respects previous values of the $DOCKER_HOST environment variable. Therefore, if you are targeting a remote host, you can call this script like this:

$ DOCKER_HOST=my.docker.host ./compose-setup.sh
Before doing so, if you had executed this script before, just remember to clean your previous environment.

### Known Issues

- Docker compose 1.5.1 has an upstream bug which causes Portus to not function correctly. If you've tried to run Portus with 1.5.1, remove the docker/registry/config.yml and docker/environment files and upgrade to the latest version of Docker compose.
- If you are using openSUSE, it's known that the firewall clashes with Docker's networking. You might need to stop the firewall and restart the docker daemon.

