Setting up Botpress using Docker involves several steps. Here is a guide to help you through the process:

### Prerequisites

1. **Docker**: Ensure Docker is installed on your machine. You can download it from the [Docker website](https://www.docker.com/get-started).

2. **Docker Compose**: This is optional but recommended for easier management of multi-container Docker applications.

### Steps to Set Up Botpress with Docker

1. **Pull the Botpress Image**

   Open a terminal and pull the Botpress Docker image:

   ```sh
   docker pull botpress/server
   ```

2. **Run Botpress Container**

   Create a directory on your host machine where Botpress will store its data:

   ```sh
   mkdir -p ~/botpress/data
   ```

   Now run the Botpress container:

   ```sh
   docker run -it -p 3000:3000 -v ~/botpress/data:/botpress/data --name botpress botpress/server
   ```

   - `-p 3000:3000` maps port 3000 of the container to port 3000 on your host machine.
   - `-v ~/botpress/data:/botpress/data` mounts the directory on your host machine to the container, so data is persisted.
   - `--name botpress` names the container "botpress" for easier management.

3. **Access Botpress**

   Once the container is running, you can access the Botpress dashboard by navigating to `http://localhost:3000` in your web browser.

4. **Using Docker Compose (Optional)**

   Create a `docker-compose.yml` file for easier setup and management:

   ```yaml
   version: '3'

   services:
     botpress:
       image: botpress/server
       ports:
         - "3000:3000"
       volumes:
         - ~/botpress/data:/botpress/data
       container_name: botpress
   ```

   Then, run the following command in the directory where your `docker-compose.yml` file is located:

   ```sh
   docker-compose up -d
   ```

   This will start the Botpress container in detached mode.

### Custom Configuration

If you need to customize the Botpress configuration, you can do so by editing the configuration files in the `~/botpress/data` directory on your host machine.

### Stopping and Restarting Botpress

To stop the Botpress container, you can use the following command:

```sh
docker stop botpress
```

To start it again:

```sh
docker start botpress
```

Or if you are using Docker Compose:

```sh
docker-compose down
docker-compose up -d
```

This setup should get you started with Botpress using Docker.
