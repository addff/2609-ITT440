# MOHAMAD FAIZIZAHRI BIN MOHD FAIZAL
# ITT440 - Network Programming with Python

**Name:** Mohamad Faizizahri Bin Mohd Faizal  
**Student ID:** 2025164657 
**Program:** CDCS2554A (Computer Science Networking)  
**Course:** ITT440 - Network Programming  

## 1. Introduction
Network programming in Python involves using the `socket` module to create connections between devices over a network. Python's socket library provides a direct interface to the Berkeley sockets API, allowing developers to write Server and Client scripts to exchange data via TCP/IP protocols. This capability is highly beneficial for network engineers to automate tasks, manage device configurations, and build custom communication tools.

In this article, I will demonstrate a two-way interactive chat application between a Server and a Client using Python.

## 2. Prerequisites
To run this demonstration, you will need:
* A Linux environment (Kali Linux/Ubuntu) or Windows.
* Python 3 installed on your system.

## 3. Programming Example (Source Code)

### Server Code (`chat_server.py`)
The server script binds to a specific port and listens for incoming connections. Once a client connects, it enters a `while` loop to allow a continuous two-way message exchange.

```python
import socket

# Create socket and bind port
s = socket.socket()
port = 8888
s.bind(('', port))
s.listen(1)
print(f"Server is listening on port {port}...")

# Accept connection from client
c, addr = s.accept()
print(f"Connected successfully to: {addr}")

# Continuous loop for chatting
while True:
    data = c.recv(1024).decode()
    
    if not data or data.lower() == 'exit':
        print("Client has disconnected.")
        break
        
    print(f"Client: {data}")
    
    reply = input("Server (You): ")
    c.send(reply.encode())

c.close()
s.close()
```

### Client Code (`chat_client.py`)
The client script initiates a connection to the server's IP address and port. It also uses a `while` loop to continuously send and receive messages until the user types 'exit'.

```python
import socket

# Create socket and connect to server
s = socket.socket()
port = 8888

# Change '127.0.0.1' to the Server's actual IP if using different machines
s.connect(('127.0.0.1', port)) 
print("Connected to the server. Type 'exit' to quit the chat.")

# Continuous loop for chatting
while True:
    message = input("Client (You): ")
    s.send(message.encode())
    
    if message.lower() == 'exit':
        print("Disconnecting from server...")
        break
    
    data = s.recv(1024).decode()
    print(f"Server: {data}")

s.close()
```

## 4. Demonstration Instructions (Kali Linux)
1. Open two terminal windows side-by-side in Kali Linux.
2. In the first terminal, execute the server script: `python3 chat_server.py`
3. In the second terminal, execute the client script: `python3 chat_client.py`
4. Start typing messages from the Client terminal, press Enter, and reply from the Server terminal to see the two-way communication.
5. Type `exit` in the Client terminal to close the connection safely.

## 5. Video Demonstration
Watch the full practical demonstration and explanation on YouTube:  
▶️ https://youtu.be/iQP0_5hZtrI?si=oy3AG3vodMW5WVdV

## 6. References
1. Chou, E. (2023). *Mastering Python Networking* (4th ed.). Packt Publishing. Available at: https://www.packtpub.com/product/mastering-python-networking-fourth-edition/9781803230810
2. Rhodes, B., & Goerzen, J. (2014). *Foundations of Python Network Programming* (3rd ed.). Apress. Available at: https://link.springer.com/book/10.1007/978-1-4302-5855-1
3. Python Software Foundation. (n.d.). *Socket Programming HOWTO*. Retrieved from https://docs.python.org/3/howto/sockets.html
