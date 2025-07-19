## Topics covered:-
1. Docker
2. Advantages
3. Docker with linux/windows/mac
4. Virtual box and Virtual Machine
5. Docker containers
6. Containers vs VMs
7. Creating VM using BOTO

# What is Docker Architecture?
Think of Docker like a shipping company. Just as a shipping company has different parts working together (trucks, warehouses, tracking systems), Docker has different components that work together to run your applications.
# Main Components of Docker
## 1. Docker Engine (The Heart)
This is the main program that runs on your computerIt's like the engine of a car - it makes everything workIt has three main parts:Docker Daemon: The background service that does the actual workREST API: How other programs talk to DockerDocker CLI: The command-line tool you use to give instructions
## 2. Docker Client (Your Interface)
This is what you use when you type docker commandsIt's like a remote control for your TV - you press buttons, and it tells the TV what to doWhen you type docker run, the client sends this request to the Docker daemon
## 3. Docker Registry (The Store)
This is where Docker images are storedDocker Hub is like an app store for Docker imagesYou can download (pull) images from here or upload (push) your own
# What is a Docker Container?
A Docker container is like actually cooking the meal using the recipe:It's a running instance of an imageIt's the actual application working and doing its jobEach container is isolated from othersYou can have multiple containers from the same image.

![alt text](Screenshot 2025-07-19 174748.png)
