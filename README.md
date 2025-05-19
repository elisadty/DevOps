ReadMe TP1

1-1 For which reason is it better to run the container with a flag -e to give the environment variables rather than put them directly in the Dockerfile?
It's better to run the container with a flag -e because it permites to gives

1-2 Why do we need a volume to be attached to our postgres container?
When we stop our container without a volume, all the data of the db are deleted. With the volume, it's like an 'autosave' when we stop and start the container, we keep all of our work.

1-3 Document your database container essentials: commands and Dockerfile.
firts I build my image like this in my folder TP1: docker build -t my-postgres-db .
then I've created my container: docker run -d --net=app-network --name=pg-db -e POSTGRES_DB=db -e POSTGRES_USER=usr -e POSTGRES
docker network create app-network
docker run -p "8090:8080" --net=app-network --name=adminer -d adminer
To add the volume, I've stopped and deleted my container pg-db and I recreate it (this time named myPost): docker run -d --net=app-network --name=myPost -p 5432:5432 -v pgdata:/var/lib/postgresql/data my-postgres-db

