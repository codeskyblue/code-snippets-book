## Netcat使用技巧

内容来自<http://www.terminally-incoherent.com/blog/2007/08/07/few-useful-netcat-tricks/>

## Port Scanner

Netcat can be a port scanner. It does not have as many features as say nmap, but if you just want to see what ports are open on a given machine, you can simply do:

	nc -v -w 1 localhost -z 1-3000

The command above will scan all the ports in the range 1-3000 on localhost.

## File Transfer

Let’s say you want to transfer a big zip file from machine A to machine B but neither one has FTP, and using email or IM is out of the question due to file size, or other restrictions. What do you do? You can use netcat as a makeshift file transfer software.

On machine B do the following, where 1337 is some unused port on which you want to send the file:

	nc -lp 1337 > file.zip

Assuming that the IP of machine B is 10.48.2.40 go to machine A and do:

	nc -w 1 10.48.2.40 1337 < file.zip

That’s it. The file will be magically transfered over the network socket.

## Chat Server

Have you even needed an improvised one-on-one chat? Netcat can do that too. You simply start listening to connections on some port like this:

	nc -lp 1337

Then on another machine simply connect to that port:

	nc 10.48.2.40 1337

Now start typing on either machine. When you press enter, the line will immediately show up on the other machine.

## Telnet Server

Nectat can also be used to set up a telnet server in a matter of seconds. You can specify the shell (or for that matter any executable) you want netcat to run at a successful connection with the -e parameter:

	nc -lp 1337 -e /bin/bash

On windows you can use:

	nc -lp 1337 -e cmd.exe

Then on a client machine simply connect to port 1337 and you will get full access to the shell, with the permissions of the user who ran nc on the server.

## Spoofing HTTP Headers

You can use netcat to connect to a server using completely spoofed headers. You can actually type out your user agent, referrer and etc. It’s useful when you want to generate bunch of hits that can be easily found in the logs or something like that:

	$ nc google.com 80
	GET / HTTP/1.1
	Host: google.com
	User-Agent: NOT-YOUR-BUSINESS
	Referrer: YOUR-MOM.COM
	
Note that your request won’t be sent until you generate a blank line. So hit return twice when your are done typing. You will get a response of headers and HTML streaming down your screen:

	HTTP/1.1 200 OK
	Cache-Control: private
	Content-Type: text/html; charset=ISO-8859-1
	Set-Cookie: PREF=ID=79f8f28c854d90ec:TM=1186369443:LM=1186369443:S=UIiTvi68MtmbcmGl; expires=Sun, 1
	-Jan-2038 19:14:07 GMT; path=/; domain=.google.com
	Server: GWS/2.1
	Transfer-Encoding: chunked
	Date: Mon, 06 Aug 2007 03:04:03 GMT
	 
	738

I deleted the HTML that followed the response – but you get the idea. It is also a good way of looking at headers. Some sites have nice surprises there (like slashdot’s X-Bender and X-Fry headers). Seriously, check them out!

## Web Server

I think this is my favorite trick. Did you ever need to set up simple makeshift webserver that would serve a single page? I know I did. In the past when my web server at work melted down, I set up laptop with this simple script:

	while true; do nc -l -p 80 -q 1 < error.html; done

The error.html page was just a very simple error message notifying our users about the outage, and giving them an estimate of when it would be fixed. It took me 3 minutes to set up, and probably saved us many angry support calls.

## Cloning Hard Drive Partitions Over the Network

This trick was submitted by Craig in the comments. On a system you want to clone do:

	dd if=/dev/sda | nc 192.168.0.1 9000

Where 9000 is some random port. On the receiving side di:

	nc -l -p 9000 | dd of=/dev/sda

Of course you need to have the cloned partitions unmounted on both systems. So if you are cloning / you will have to boot from a live distro like Knoppix. Note that you can use this technique to clone NTFS partitions as well – just need to use a live Linux distro on both sides.

## Summary

Despite being able to do all that netcat still conforms to the Unix philosophy of doing one thing, and doing it well. Netcat was designed for a single purpose – to read and write data packets over network sockets. And because of it’s singular purpose it can be used in such a myriad of ways.

It is ironic, but it is of ten the case that the more features you add to your application, the more specialized it gets. And of course, GUI is the ultimate functionality killer. If netcat had a GUI I doubt it would be half as useful as it is right now.

I’ve been told that [socat](http://www.dest-unreach.org/socat/) is a more powerful netcat fork which has even more functionality. Personally, I haven’t played with it at all. It does seem to have a different syntax, and it is not as mature or well known, and popular as it’s predecessor.