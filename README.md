# Netbyte

![Version 1.0](http://img.shields.io/badge/version-v1.0-orange.svg)
![Python 2.7](http://img.shields.io/badge/python-2.7-blue.svg)
![MIT License](http://img.shields.io/badge/license-MIT%20License-blue.svg)
[![sc0tfree Twitter](http://img.shields.io/twitter/url/http/shields.io.svg?style=social&label=Follow)](https://twitter.com/sc0tfree)

Netbyte is a Netcat-style tool that facilitates manual probing, fuzzing and exploitation of TCP and UDP services.
It is lightweight, fully interactive and allows for input as well as output in both hexadecimal and ASCII.

## Why

When testing proprietary or custom-written services, I’ve frequently been disappointed with my toolkit's 
ease of usability to reverse engineer or fuzz these protocols.

I have traditionally used Netcat with Wireshark and/or hexdump.
However, due to truncation issues with using hexdump (e.g.: `nc domain.com 1234 | hexdump -C`)
and Wireshark’s tedious process, I decided to create Netbyte as quick and easy alternative when opening unknown ports.

Additionally, I wanted to be able to send hex back to the server and begin testing edge cases using fuzzing.
To address this, I integrated Python string evaluation capabilities inside of Netbyte, allowing the user to send 
strings of crafted bytes to the server.

## Install

Clone the git:
```
git clone https://github.com/sc0tfree/netbyte.git
```
Enter the directory:
```
cd netbyte
```
Run setup.py script with 'install':
```
python setup.py install
```

## Basic Usage

<pre><code>$ <span style="font-weight: bold">netbyte example.com 12345</span>
<span style="color: green">Connection Established</span>
<span style="color: magenta">������!��'</span>
<span style="color: blue">FF FB 01 FF FB 03 FF FD 21(!) FF FD 27(')</span>


<span style="color: magenta">Enter your user id:</span>
<span style="color: blue">0D 0A(\n)
0D 0A(\n)
45(E) 6E(n) 74(t) 65(e) 72(r) 20 79(y) 6F(o) 75(u) 72(r) 20 75(u) 73(s) 65(e) 72(r) 20 69(i) 64(d) 3A(:) 20 07</span>
<span style="font-weight: bold">admin</span>
<span style="color: magenta">user password:</span>
<span style="color: blue">61(a) 64(d) 6D(m) 69(i) 6E(n) 0D 0A(\n)
75(u) 73(s) 65(e) 72(r) 20 70(p) 61(a) 73(s) 73(s) 77(w) 6F(o) 72(r) 64(d) 3A(:) 20</span>
<span style="font-weight: bold">admin</span>

<span style="color: magenta">Invalid user or password</span>

<span style="color: green">Connection closed</span>
</code></pre>

You can also pipe input into netbyte:

<pre><code>$ <span style="font-weight: bold">echo &quot;GET /&quot; | netbyte test.com 80</span>
<span style="color: green">Connection Established</span>
<span style="color: magenta">&lt;html&gt;
&lt;head&gt;&lt;title&gt;302 Found&lt;/title&gt;&lt;/head&gt;
&lt;body bgcolor=&quot;white&quot;&gt;
&lt;center&gt;&lt;h1&gt;302 Found&lt;/h1&gt;&lt;/center&gt;
&lt;hr&gt;&lt;center&gt;nginx/1.13.4&lt;/center&gt;
&lt;/body&gt;
&lt;/html&gt;</span>

<span style="color: blue">3C(&lt;) 68(h) 74(t) 6D(m) 6C(l) 3E(&gt;) 0D 0A(\n)
3C(&lt;) 68(h) 65(e) 61(a) 64(d) 3E(&gt;) 3C(&lt;) 74(t) 69(i) 74(t) 6C(l) 65(e) 3E(&gt;) 33(3) 30(0) 32(2) 20 46(F) 6F(o) 75(u) 6E(n) 64(d) 3C(&lt;) 2F(/) 74(t) 69(i) 74(t) 6C(l) 65(e) 3E(&gt;) 3C(&lt;) 2F(/) 68(h) 65(e) 61(a) 64(d) 3E(&gt;) 0D 0A(\n)
3C(&lt;) 62(b) 6F(o) 64(d) 79(y) 20 62(b) 67(g) 63(c) 6F(o) 6C(l) 6F(o) 72(r) 3D(=) 22 77(w) 68(h) 69(i) 74(t) 65(e) 22 3E(&gt;) 0D 0A(\n)
3C(&lt;) 63(c) 65(e) 6E(n) 74(t) 65(e) 72(r) 3E(&gt;) 3C(&lt;) 68(h) 31(1) 3E(&gt;) 33(3) 30(0) 32(2) 20 46(F) 6F(o) 75(u) 6E(n) 64(d) 3C(&lt;) 2F(/) 68(h) 31(1) 3E(&gt;) 3C(&lt;) 2F(/) 63(c) 65(e) 6E(n) 74(t) 65(e) 72(r) 3E(&gt;) 0D 0A(\n)
3C(&lt;) 68(h) 72(r) 3E(&gt;) 3C(&lt;) 63(c) 65(e) 6E(n) 74(t) 65(e) 72(r) 3E(&gt;) 6E(n) 67(g) 69(i) 6E(n) 78(x) 2F(/) 31(1) 2E 31(1) 33(3) 2E 34(4) 3C(&lt;) 2F(/) 63(c) 65(e) 6E(n) 74(t) 65(e) 72(r) 3E(&gt;) 0D 0A(\n)
3C(&lt;) 2F(/) 62(b) 6F(o) 64(d) 79(y) 3E(&gt;) 0D 0A(\n)
3C(&lt;) 2F(/) 68(h) 74(t) 6D(m) 6C(l) 3E(&gt;) 0D 0A(\n)</span>

<span style="color: green">Connection closed</span>
</code></pre>

## Manual Fuzzing and Exploitation

Netbyte is able to send evaluated Python expressions by using `!!` at the beginning of any input. This is useful for manual fuzzing and even exploitation.

*Note: using `!!` mode does not automatically include a newline ('\n') at the end of the sent string.*

### Newlines

To include a newline automatically at the end of an evaluated expression, use `!!!` at the beginning of any input.

### Examples:
| Expression                | Result                                |
|:--------------------------|:--------------------------------------|
| `!! "A" * 250`            | Send 250 A's                          |
| `!! "\x41" * 250`         | Send 250 A's                          |
| `!! "A" * 250 + "\n"`     | Send 250 A's and a newline ('\n')     |
| `!!! "A" * 250`           | Send 250 A's and a newline ('\n')     |
| `!!! "abc" * 2 + "def"`   | Send 'abcabcdef' and a newline ('\n') |


Let's see it in action by crashing a PCMan FTP Server:

<pre><code>$ <span style="font-weight: bold">netbyte pcmanserver.com 21</span>
<span style="color: green">Connection Established</span>
<span style="color: magenta">220 PCMan's FTP Server 2.0 Ready.</span>

<span style="color: blue">32(2) 32(2) 30(0) 20 50(P) 43(C) 4D(M) 61(a) 6E(n) 27(') 73(s) 20 46(F) 54(T) 50(P) 20 53(S) 65(e) 72(r) 76(v) 65(e) 72(r) 20 32(2) 2E 30(0) 20 52(R) 65(e) 61(a) 64(d) 79(y) 2E 0D 0A(\n)</span>

<span style="font-weight: bold">USER anonymous</span>
<span style="color: magenta">331 User name okay, need password.</span>

<span style="color: blue">33(3) 33(3) 31(1) 20 55(U) 73(s) 65(e) 72(r) 20 6E(n) 61(a) 6D(m) 65(e) 20 6F(o) 6B(k) 61(a) 79(y) 2C(,) 20 6E(n) 65(e) 65(e) 64(d) 20 70(p) 61(a) 73(s) 73(s) 77(w) 6F(o) 72(r) 64(d) 2E 0D 0A(\n)</span>

<span style="font-weight: bold">!!! &quot;PASS &quot; + &quot;A&quot; * 6400</span>
<span style="color: magenta">230 User logged in</span>

<span style="color: blue">32(2) 33(3) 30(0) 20 55(U) 73(s) 65(e) 72(r) 20 6C(l) 6F(o) 67(g) 67(g) 65(e) 64(d) 20 69(i) 6E(n) 0D 0A(\n)</span>

</code></pre>

</body></html>
*The exploit for this buffer overflow can be found [here](https://www.exploit-db.com/exploits/27277/).*

## Test Server

Netbyte includes a built-in test server to better view its functionality.

The server has three tests:

* Echo Test - server echoes back a user-specified string
* Display Hex Test - server sends a string of random bytes with user-specified length
* Byte Count Test - server counts number of input bytes sent from client

To run the test server on default port 12345:
```
$ netbyte --testserver
```
In another terminal, connect to the test server using netbyte:
```
$ netbyte localhost 12345
```

## License and Contributions

Netbyte is under the MIT License.

Questions, comments and suggestions are always welcomed!

## Future Work

* Listen option to interact with custom-built clients
* Proper unit tests
