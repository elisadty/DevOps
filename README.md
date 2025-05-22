# README TP1

### 1-1 For which reason is it better to run the container with a flag -e to give the environment variables rather than put them directly in the Dockerfile?
It's better to run the container with a flag -e (environment) because it permites to keep confidentiality of the password, user etc... , it also permit to reuse the same image in a different environment.

### 1-2 Why do we need a volume to be attached to our postgres container?
When we stop our container without a volume, all the data of the db are deleted. With the volume, it's like an 'autosave' when we stop and start the container, we keep all of our work.

### 1-3 Document your database container essentials: commands and Dockerfile.
Firts I build my image like this in my folder TP1: docker build -t my-postgres-db .
Then I've created my container: docker run -d --net=app-network --name=pg-db -e POSTGRES_DB=db -e POSTGRES_USER=usr -e POSTGRES
docker network create app-network
docker run -p "8090:8080" --net=app-network --name=adminer -d adminer
To add the volume, I've stopped and deleted my container pg-db and I recreate it (this time named myPost): docker run -d --net=app-network --name=myPost -p 5432:5432 -v pgdata:/var/lib/postgresql/data my-postgres-db

### 1-4 Why do we need a multistage build? And explain each step of this dockerfile.
We need a multistage build to separate the compilation and the runtime, which results in a lighter final image, containing only what the application needs to run, not the build tools.

### 1-5 Why do we need a reverse proxy?
We need a reverse proxy because it permits (external users) to not directly access to the server (internal), it makes a "barrier" before accessing to the servers. It also improves security.

### 1-6 Why is docker-compose so important?
Docker compose is important because it permits to manage several containers. With one file (docker-compose.yml) we can 'centralize' the run of many containers without doing it manually.

### 1-7 Document docker-compose most important commands.
docker compose up -d --build (start/create/run)
docker compose logs -f (explore if problems)
docker compose down (stop/rm containers)

### 1-8 Document your docker-compose file.

services:
  // Backend service (Spring Boot application)
  backend:
    build:
      context: backend_api/demo  # Folder containing the Dockerfile for the backend
    networks:
      - app-network              # Connects to the shared network
    depends_on:
      - database                 # here database is the folder that contains the dockerfile db

  // PostgreSQL database service
  database:
    build:
      context: ./database        # Folder with Dockerfile or database setup
    environment:
      POSTGRES_DB: db           
      POSTGRES_USER: usr         
      POSTGRES_PASSWORD: pwd     
    volumes:
      - db-data:/var/lib/postgresql/data  # Volume for persisting data
    networks:
      - app-network              

  // HTTP server
  httpd:
    build:
      context: ./Http_server     # Folder containing Apache configuration
    ports:
      - "8082:80"                # Maps local port 8082 to container's port 80
    networks:
      - app-network              
    depends_on:
      - backend                  # Waits for the backend to be available

// Define the shared network between services
networks:
  app-network:

// Define a named volume for persistent database storage
volumes:
  db-data:


### 1-9 Document your publication commands and published images in dockerhub.
I've typed those commands: 
docker tag my-postgres-db elisadty/my-database:1.0
docker tag simple_api_backend elisadty/my-backend:1.0
docker tag my-custom-apache elisadty/my-httpd:1.0

And just after, I push like this:
docker push elisadty/my-database:1.0
docker push elisadty/my-backend:1.0
docker push elisadty/my-httpd:1.0

On docker, I can see my images with the corresponding tag that I set. The tag allows to rollback to a previous version if needed, it's like a landmark.

### 1-10 Why do we put our images into an online repo?
We put it on an online repo because this way, other team members or servers can pull (directly from the resgistery for servers) and use the same image.

### 2-1 What are testcontainers?
It's a Java library that use Docker containers in automated testing. 

### 2-2 For what purpose do we need to use secured variables ?
We need to use secured variables to store sensitive data (like our SSH keys, the token, and password) securely in GitHub, without divulgin it in code.

### 2-3 Why did we put needs: build-and-test-backend on this job? Maybe try without this and you will see!
We put needs to make sure this job runs only if the backend was built and tested successfully. Without it, the job might run even if the build fails, which would break the CI process.

### 2-4 For what purpose do we need to push docker images?
We push Docker images to Docker Hub so they can be downloaded and run on the server using Ansible.


