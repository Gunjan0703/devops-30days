# Docker Volumes
Volumes are like external hard drives that Docker manages for you. They're stored in a special Docker area on your computer.
## How it works:
- Docker creates a storage space
- You give it a name (like database_data)
- You can attach it to any container
- The data stays even if you delete the container
 ### Commands
- Create a volume **docker volume create database_data**
- Use the volume in a container **docker run -v database_data:/var/lib/mysql mysql**
- Get detailed information **docker volume inspect my_volume**
- Remove a volume **docker volume rm my_volume**
- List all volumes **docker volume ls**
- Remove all unused volumes **docker volume prune**

# Docker Networking
### Why Docker Networking Matters
When you have multiple containers, they need to talk to each other. 
 For example:
- Your web application needs to connect to a database
- Your API needs to connect to a cache like Redis
- Your load balancer needs to reach your application servers
### Types of Docker Networks
1. Bridge --> This is like having a private network inside your computer where containers can talk to each other.
2. Host --> The container uses your computer's network directly. It's like the container is running directly on your machine.
3. none
4. Custom bridge --> Used when you have Docker containers running on multiple computers that need to communicate. This is advanced and used in production clusters.
  ## commands
1. list network --> docker network inspect my_network
2. create network --> docker network create my_network
3. inspect network --> docker network inspect my_network
4. connect container to network --> docker network connect my_network my_container
5. disconnect container to network --> docker network disconnect my_network my_container
