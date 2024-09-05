Recall that network applications are just processes running that send messages to each other by way of their sockets. Thus making a simple network application can be done by simply reading to and writing from sockets

### UDP socket programming
The thing to note about UDP sockets is that a connection isn't established between a client and a server. And so the application can't just drop messages into its socket and expect it to get to the receiving process. It has to create a packet and write down the IP address / hostname and the port number of the receiving socket.

**UDP client**
1. Create variables to specify the address of the server socket
```python
server_name = "127.0.0.1"
server_port = 12_000
```

2. Create the UDP socket. There is no need to bind a port number to a socket since you'll just let the OS use a random port number for you.
```python
client_socket = socket(AF_INET, SOCK_DGRAM)
```

3. Send the message to the socket, specifying the address of the receiving socket
```python
client_socket.sendto("hello world".encode(), (server_name, server_port))
```

4. Wait for the reply in the same socket
```python
reply, server_addr = client_socket.recvfrom(2048)
```

5. Access the reply and close the socket
```python
print("Reply from: " + server_addr + " " + reply.decode())
client_socket.close()
```

**UDP server**
1. Create variables to specify the port number of the server socket
```python
server_port = 12_000
```

2. Create the UDP socket. In this case, there's a need to bind a port number to a socket so clients will always know where to find this network application
```python
server_socket = socket(AF_INET, SOCK_DGRAM)
server_socket.bind(("", server_port))
```

3. Wait for connections with a `while` loop. Once you receive a connection, receive it
```python
while True:
	message, client_addr = server_socket.recvfrom(2048)
```

4. Send the reply to the client
```python
server_socket.sendto(message.upper(), client_addr)
```

### TCP socket programming
A big difference between UDP and TCP sockets is that a reliable connection is established before bytes are exchanged. When a client initiates a connection, it "knocks" on the server's door (a welcome socket). After hearing the knocking, the server creates an independent socket where it can then exchange bytes with the client (a connection socket). In this manner, a client need not specify the destination address in its message anymore, it can simply "drop" the message into the socket, and TCP will deliver it.

**TCP client**
1. Create variables to specify the address of the server socket
```python
server_name = "127.0.0.1"
server_port = 12_000
```

2. Create the TCP socket
```python
client_socket = socket(AF_INET, SOCK_STREAM)
```

3. Establish a connection with the server TCP socket (doing the "knocking")
```python
client_socket.connect((server_name, server_port))
```

4. Send the message to the socket. Note that we didn't need to specify the destination address
```python
client_socket.send("hello world".encode())
```

5. Wait for the reply
```python
reply = client_socket.recv(2048)
```

6. Access the reply and close the client socket
```python
print(reply.decode())
client_socket.close()
```

**TCP server**
1. Create variable for the port
```python
server_port = 12_000
```

2. Create the TCP "welcome" socket
```python
welcome_socket = socket(AF_INET, SOCK_STREAM)
```

3. Bind the port number to the socket
```python
welcome_socket.bind(("", server_port))
```

4. Listen for clients that come knocking at the welcome socket
```python
welcome_socket.listen(1)
```

5. Wait for connections with a `while` loop. Once you receive a connection, accept the connection. Accepting a connection returns a connection socket you can use to exchange messages with the client
```python
while True:
	connection_socket, addr = welcome_socket.accept()
```

6. Receive the bytes from the client
```python
message = connection_socket.recv(1024).decode()
```

7. Send the reply to the connection socket
```python
connection_socket.send(message.encode())
```

8. Close the connection socket and wait for more connections
```python
connection_socket.close()
```