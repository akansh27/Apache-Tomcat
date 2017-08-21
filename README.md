# Apache-Tomcat
## Apache Tomcat Server Important topics

Tomcat is a web server and servlet container. It implements several Java Enterprise Edition specifications including Java servlet, JSPs,
Java EL, Websocket and provides ‘pure java’ web HTTP server environment for java codes to run in.

Tomcat is one of the most popular Servlet and JSP Container servers. It’s used by some of following high traffic websites:

LinkedIn.com

Dailymail.co.uk

Comcast.net

Wallmart.com

Reuters.com

Meetup.com

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

## Apache Tomcat Hardening and Security Guide
Having default configuration may have much sensitive information, which helps hacker to prepare for an attack the Tomcat server. This practical guide provides you the necessary skill set to secure Apache Tomcat server.

### 1. Remove Server Banner
Removing Server Banner from HTTP Header is one of the first things to do as hardening. Having server banner expose the product you are using and leads to information leakage vulnerability.

![Alt text](https://github.com/farashahamad/Apache-Tomcat/blob/master/tomcat-server-banner.png?raw=true "Optional Title")

Implementation:

Go to $tomcat/conf folder
Modify server.xml by using vi
Add following under Connector port and save the file
```
Server =” “
```
Ex: –
```
<Connector port="8080" protocol="HTTP/1.1" 
connectionTimeout="20000" 
Server =" " 
redirectPort="8443" />
```

Verification:

Open Firefox with firebug
Access Tomcat application
You will notice Server value is blank now.

![Alt text](https://github.com/farashahamad/Apache-Tomcat/blob/master/tomcat-server-banner-verification.png?raw=true "Optional Title")

### 2. Starting Tomcat with a Security Manager
Security Manager protects you from an untrusted applet running in your browser. Running Tomcat with a security manager is definitely better than running without one. Tomcat has very good documentation on Tomcat Security Manager. 

Implementation:

All you got to do is to start tomcat with –security argument.

```
root# ./startup.sh -security
Using CATALINA_BASE:   /opt/tomcat
Using CATALINA_HOME:   /opt/tomcat
Using CATALINA_TMPDIR: /opt/tomcat/temp
Using JRE_HOME:        /System/Library/Java/JavaVirtualMachines/1.6.0.jdk/Contents/Home
Using CLASSPATH:       /opt/tomcat/bin/bootstrap.jar:/opt/tomcat/bin/tomcat-juli.jar
Using Security Manager root#
```

### 3. Enable access log logging
The default configuration doesn’t capture access logs. The access log is very useful in troubleshooting to check request type, requester IP address, status code, etc.

Implementation:

Go to $tomcat/conf
Modify server.xml by using vi
Go to the end of the file and uncomment Valve entry for valves.AccessLogValue
```
<Valve className="org.apache.catalina.valves.AccessLogValve"
directory="logs" 
prefix="localhost_access_log."
suffix=".txt"
pattern="common" resolveHosts="false"/>
```
Restart Tomcat server and you should see localhost_access_log is created under $tomcat/logs folder

### 4. Enforced HTTPS
It’s good to force redirect all HTTP requests to HTTPS to ensure web application data transmission are over SSL Certification.

Implementation:

Go to $tomcat/conf folder
Modify web.xml by using vi
Add following before </web-app> syntax
```
<security-constraint> 
<web-resource-collection> 
<web-resource-name>Protected Context</web-resource-name> 
<url-pattern>/*</url-pattern>
</web-resource-collection> 
<user-data-constraint> 
<transport-guarantee>CONFIDENTIAL</transport-guarantee> 
</user-data-constraint> 
</security-constraint>
```
Restart Tomcat and access web application to verify.
Note: ensure Tomcat is configured to run on SSL else it will break the application accessibility.

### 5. Add Secure flag in cookie
It is possible to steal or manipulate web application session and cookies without having a Secure flag in HTTP Header as Set-Cookie.

Implementation:

Go to $tomcat/conf folder
Modify server.xml by using vi
Add following in Connector port
```
Secure="true"
```

Ex:
```
<Connector port="8080" protocol="HTTP/1.1" 
connectionTimeout="20000" 
Server=" " 
Secure="true" 
redirectPort="8443" />
Verification:
```

Open Firefox with firebug
Access your application and check HTTP response header, you should see Secure flag

![Alt text](https://github.com/farashahamad/Apache-Tomcat/blob/master/tomcat-header-secure-flag.png?raw=true "Optional Title")
### 6. Add HttpOnly in cookie
Best practice to have this enabled at application code level. However, due to bad programming or developer’s unawareness, it comes to Web Infrastructure.

Implementation:

Go to $tomcat/conf folder
Modify context.xml by using vi
Add following  in Context directive
```
usehttponly=”true”
```
Ex:-
```
<context usehttponly="true">...</context>
```
### 7. Enable Secure Socket Layer (SSL)
To enable Tomcat to listen over HTTPS protocol, you must configure tomcat with SSL. If you are new to SSL, you can refer to Beginner’s Guide to SSL. This assumes you have SSL Certificate imported under keystore.

Implementation:

Go to $tomcat/conf folder
Modify server.xml by using vi
Add following under Connector port
```
SSLEnabled=”true” scheme=”https” keystoreFile="conf/keystore" keystorePass="password"
```
Ex:
```
<Connector port="8080" protocol="HTTP/1.1" 
connectionTimeout="20000" 
Server=" " 
Secure="true" 
SSLEnabled="true" scheme="https" keystoreFile="conf/keystore" keystorePass="password" clientAuth=”false” sslProtocol=”SSLv3” 
redirectPort="8443" />
```
### 8. Run Tomcat from non-privileged account
It’s good to use a separate non-privileged user for Tomcat. The idea here is to protect other services running in case of any security hole.

Implementation:

Create a UNIX user
Change $tomcat ownership to newly created UNIX user

### 9. Remove default/unwanted applications
By default, Tomcat comes with following web applications, which may or not be required in a production environment. You can delete them to keep it clean and avoid any known security risk with Tomcat default application.

ROOT – Default welcome page
Docs – Tomcat documentation
Examples – JSP and servlets for demonstration
Manager, host-manager – Tomcat administration

### 10. Change SHUTDOWN port and Command
By default, tomcat is configured to be shutdown on 8005 port. Do you know you can shutdown tomcat instance by doing a telnet to IP:port and issuing SHUTDOWN command?

```
Root # telnet localhost 8005
Trying ::1... telnet:
connect to address ::1:
Connection refused Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
SHUTDOWN Connection closed by foreign host.
Root #
```
You see having default configuration leads to high-security risk. It’s recommended to change tomcat shutdown port and default command to something unpredictable.

Implementation:

Go to $tomcat/conf folder
Modify server.xml by using vi
```
<Server port="8005" shutdown="SHUTDOWN">
```
8005 – Change to some other unused port

SHUTDOWN – Change to something complicated

Ex-
```
<Server port="8867" shutdown="NOTGONNAGUESS">
```
### 11. Replace default 404, 403, 500 page
Having default page for not found, forbidden, server error exposes Tomcat version and that leads to security risk if you are running with vulnerable version. Let’s look at default 404 page.

![Alt text](https://github.com/farashahamad/Apache-Tomcat/blob/master/tomcat-default-404.png?raw=true "Optional Title")
To mitigate, you can first create a general error page and configure web.xml to redirect to general error page.

Implementation:

Go to $tomcat/webapps/$application
Create an error.jsp file
```
#vi error.jsp 
<html>
<head> 
<title>404-Page Not Found</title>
</head>
<body> That's an error! </body>
</html>
```
Go to $tomcat/conf folder
Add following in web.xml by using vi. Ensure you add before </web-app> syntax
```
<error-page> 
<error-code>404</error-code> 
<location>/error.jsp</location>
</error-page>
<error-page> 
<error-code>403</error-code> 
<location>/error.jsp</location>
</error-page>
<error-page> 
<error-code>500</error-code> 
<location>/error.jsp</location>
</error-page>
```
Restart tomcat server. Now, let’s test it.

![Alt text](https://github.com/farashahamad/Apache-Tomcat/blob/master/tomcat-custom-error.png?raw=true "Optional Title")
As you can see tomcat information is no more exposed.

You can do this for java.lang.Exception as well. This will help in not exposing tomcat version information if any java lang exception.

Just add following in web.xml and restart tomcat server.
```
<error-page> 
<exception-type>java.lang.Exception</exception-type> 
<location>/error.jsp</location>
</error-page>
```
