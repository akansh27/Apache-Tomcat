# Apache-Tomcat
## Apache Tomcat Server Important topics

Tomcat is a web server and servlet container. It implements several Java Enterprise Edition specifications including Java servlet, JSPs,
Java EL, Websocket and provides ‘pure java’ web HTTP server environment for java codes to run in.

Developer: Apache Software Foundation
Stable Release: 8.0.18 (23-01-2015)
Written in: Java
Type: Servlet container, HTTP web server

### Features
The Servlet 3.1 is an incremental update that includes a couple new features and further clarifications to the specification.  One of those
new features is non-blocking I/O. This is a performance improvement and allows data to be managed asynchronously through a ReadListener and
a WriteListener interface. This also applies to the new Websockets capabilities. The other big addition an HTTP protocol upgrade that 
allows the client to specify switching protocols. Under the hood, this is the same capability that is used by WebSockets to upgrade the 
HTTP connection, although this allows for a broader range of protocols. See this example of the protocol upgrade in the Servlet 3.1 for 
sample code of this feature in action.
For JSP 2.3, there was not much in the way of new capabilities or features. This release was more focused on maintenance and clarifications.
For E.L 3.0, there were some significant enhancements, and Tomcat 8 takes these into account.
•	First of all, EL can now run on it’s own in standalone mode, outside of servlets and JSP pages. In a nutshell, the enhancement allows you
to call an ELProcessor class directly within your application without a servlet container.
•	In addition, there is now support for lambda expressions, similar to what will be in Java 8. These expressions can simplify code and are 
comprised of a set of parameters, a lambda operator, and a function body. See the following examples and overview for more information.
•	Collection operators will allow for the construction of Sets, Lists, and Maps.
•	The Java EL stream API includes filters like forEach, reduce, filter, and map. Here, you can take a collection or list, invoke a string 
method, and run additional filtering or aggregate functions on the list. For example, you could sum a string.
•	Developers will be able to directly access static fields and methods of any classes imported to the EL context.
Websockets 1.0 as a standard API is now supported. Tomcat 7 had support, but it was Tomcat-specific support because the specification 
wasn’t finalized. Most websites today run HTTP in half-duplex mode—this means that communications can happen in both directions, but only 
one at a time, like a walkie-talkie. The Websockets protocol is full duplex—this means that bidirectional data can flow at the same time. 
It was originally proposed with HTML5.
