## Python Network

### 检测端口是否打开

	import socket;
	sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
	result = sock.connect_ex(('127.0.0.1',80))
	if result == 0:
	   print "Port is open"
	else:
	   print "Port is not open"

### 有用的链接
- 如何发布包到pypi

	<http://peterdowns.com/posts/first-time-with-pypi.html>

- pypi mirror

	<http://www.pypi-mirrors.org/>

	国内推荐用douban的

	**Simple Usage**

		pip install -i http://pypi.douban.com/simple <package>

	**Global settings:**

	Add `~/.pip/pip.conf` that includes:

		[global]
		index-url = http://pypi.douban.com/simple

### Python TCP的使用

内容来自 <http://synack.me/blog/using-python-tcp-sockets>

It seems like there are a lot of people confused about how to make correct use of the BSD sockets interface without having data disappear in the middle of a session. For building servers, you can make use of the SocketServer module to hide some of the annoyances, but for clients you've either got to deal with sockets or use a third party library, which never ends up being as fun as it sounds.

We'll start with a simple example... Open a socket, send a string, then close it.

	from socket import socket

	sock = socket()
	sock.connect(('1.2.3.4', 1234))
	sock.send('Hello world!\n')
	sock.close()

This looks like a pretty simple example, but it exemplifies the pitfalls of sockets programming fairly well. The first problem that becomes apparent is that while the socket() constructor defaults to `AF_INET`, `SOCK_STREAM` (IPv4 TCP socket) arguments now, this may not be true in the future. The most likely change would be `AF_INET6` for IPv6 support. While this call probably won't fail on an IPv6 native system, the IP address passed to the connect method would end up being in the wrong format. The simple solution is to pass DNS names rather than IP addresses and ensure that you catch any exceptions that might arise from nonexistant names.

This leads to the next problem: exception handling. The socket constructor could throw an exception if the system encounters an "out of FDs" condition. While this is fairly uncommon, you'll want to catch it to make your code more robust. The connect method could throw an exception for a number of reasons. If the IP or hostname you pass in cannot be parsed or resolved, or if all available source ports have been exhausted (extremely unlikely, requires at least 65536 established connections). The send method could fail if the connection has been closed or interrupted in some manner, although it's more likely that it simply wouldn't send data. Finally, the close method will only fail if the socket has already been closed. In our example above, there's no other way the socket object could be closed, so we really don't need to worry much about that. Additionally, this line may even be extraneous because Python closes sockets upon garbage collection. But it's best to include it for correctness.

Sending data can be a bit tricky, especially if you're trying to send a lot of it. The socket's send() method is not guaranteed to send all of the data you pass it. Instead, it returns the number of bytes that were actually sent and expects your application to handle retransmission of the unsent portion. However, Python provides a convenience method called sendall() that makes sure all of your data is sent before returning. The right way:

	sock.sendall('Hello world\n')

If, for some reason, you need to know exactly what bytes were sent, even under an error condition, you can also do it the old-fashioned way:

	buffer = 'Hello world\n'
	while buffer:
		bytes = sock.send(buffer)
		buffer = buffer[bytes:]

This approach can be a bit inefficient memory-wise, but will allow you to access the unsent portion of buffer if sock.send throws an exception.

Time to try receiving some data! There are many ways to handle incoming data, depending on your application's needs. For now, I'll illustrate how you might handle a common line-based protocol.

	def readlines(sock, recv_buffer=4096, delim='\n'):
		buffer = ''
		data = True
		while data:
			data = sock.recv(recv_buffer)
			buffer += data

			while buffer.find(delim) != -1:
				line, buffer = buffer.split('\n', 1)
				yield line
		return

This method will continuously call `recv()` until the connection is closed, and supply a generator interface to the caller so that implementing your protocol becomes as simple as:

	for line in readlines(sock):
		# Do something with a line of data

The socket object also provides a `makefile()` method that returns a file object based on the socket's file descriptor, allowing implementations like this:

	for line in sock.makefile('r'):
		# Do something with a line of data

The makefile approach is likely more efficient and will perform better, but may not be as compatible on multiple platforms and does not give you as much control over the recv buffer size and delimiter.

Unfortunately, `recv()` is a blocking call. If you'd like your program to continue doing other things while waiting for data, you have several choices: call setblocking(False) on your socket, causing recv to throw an exception if there's no data available, or use the select module to occasionally poll the socket for data, or make use of platform-specific calls like epoll and kqueue, or just handle sockets in a separate thread or process. It all depends on what your application needs. Let's take a look at a simple `select()` call, as most of the other methods are variants of this.

	from select import select
	from socket import socket

	sock = socket()
	sock.connect(('1.2.3.4', 1234))

	while True:
		readable, writable, exceptional = select([sock], [], [], timeout=0)
		if readable:
			data = sock.recv(4096)
			# Handle the incoming data
		# Continue other tasks here

In this case, we're only using select to check the status of a single socket, but as you can see, it supports polling lists of sockets for readable, writable, and exception states. The return value is a three item tuple, containing any sockets that were found to be in the given states. Once the data is cleared from a socket, you can continue to perform other tasks in your application, eventually looping around to the select() call to poll for more data. This is an extremely efficient way to handle a large number of connections and use often used in implementing servers in order to avoid having to spawn new processes or threads for each connection, further consuming system resources.

I hope this post has cleared up some of the confusion around sockets programming. If you have questions or comments, don't be afraid to [let me know](http://synack.me/social).
