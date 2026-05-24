# Threaded-Port-Scanner-in-Python


Objective 🎯

In this project I aimed to build a fast, threaded port scanner in Python to efficiently scan multiple ports simultaneously. Using multithreading, my goal was to improve speed and accuracy while keeping the tool lightweight and user-friendly.

Skills Learned 🧠

Python socket programming for network scanning
Multithreading to improve performance
Concurrency handling for efficient scanning
Parsing and handling IP addresses & port ranges
Implementing timeouts and exception handling

Tools Used ⛏

Py Charm

freeCodeCamp.org
https://www.youtube.com/watch?v=FGdiSJakIS4&t=2635s




Steps

1. The first I created an socket which is used to connect to a target IP on a specific port. If the query is succefull it returns as truf if not false.
   
![1](https://github.com/user-attachments/assets/a707e8e6-f4c5-43dc-9a13-4bc9d72c0a46)

2. This second step I create a for loop that scans all the standardized ports (at this point this scanner is very slow but I will fix it later with multithreading)

   ![2](https://github.com/user-attachments/assets/a6d9169d-b8f8-4266-b7d0-0e8ed2539185)

3. To complete multithreading we need to import some libraries (Socket will be used for connection attempts to a specific port, Threading will run multiple scanning funcions simultaneously and
   queue is a data structure that will help manage the access of multiple threads)

   ![3](https://github.com/user-attachments/assets/d7f36680-f0c3-4597-b9c0-5f4621d76d1d)

4. This step is very simple I just set the target IP of the host im scanning which was my static ip I set for this lab we also ad the queue = Queue() which will be filled with the port we want to scan

   
![4](https://github.com/user-attachments/assets/a7423694-d6b6-46a6-a655-8d4f168f7208)


5. This is a basic try-except block which  means if we try to connect to our target on a specific port if it works we return true which means the port is open if not we return false also in this step we define a worker fuction that is responsible for getting the port numbers from the queue that scans and prints them

   ![5](https://github.com/user-attachments/assets/a9106a37-3a23-4f6c-a239-b0094c70281a)

6. In this fuction there are two parameters the first one is for the amount of threads we want to start and the second one is our mode. We load our ports, depending on the mode we have chosen and we create a new empty list for our threads. Then, we create the desired amount of threads, assign them our worker function and add them to the list. After that, we start all our threads and let them work. They are now scanning all the ports

   ![6](https://github.com/user-attachments/assets/af43e949-0c06-4f5e-a095-5955c4e54648)

7. As you can see below our terminal prints which ports are open after our scan

   ![7](https://github.com/user-attachments/assets/23913fbe-f9cd-45e6-98a6-f85687f41044)



   
