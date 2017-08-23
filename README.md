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

## An Overview of Tomcat Servlet Container

![Alt text](https://github.com/farashahamad/Apache-Tomcat/blob/master/tomcat6-article3-image01.png?raw=true "Optional Title")

The objective of this article is to cover the primary request processing components that are present in this image. Advanced topics, such as clustering and security, are shown as shaded in this image and are not covered.

In this image, the '+' symbol after the Service, Host, Context, and Wrapper instances indicate that there can be one or more of these elements. For instance, a Service may have a single Engine, but an Engine can contain one or more Hosts. In addition, the whirling circle represents a pool of request processor threads.

Tomcat's architecture follows the construction of a Matrushka doll from Russia. In other words, it is all about containment where one entity contains another, and that entity in turn contains yet another.

In Tomcat, a 'container' is a generic term that refers to any component that can contain another, such as a Server, Service, Engine, Host, or Context.

Of these, the Server and Service components are special containers, designated as Top Level Elements as they represent aspects of the running Tomcat instance. All the other Tomcat components are subordinate to these top level elements.

The Engine, Host, and Context components are officially termed Containers, and refer to components that process incoming requests and generate an appropriate outgoing response.

Nested Components can be thought of as sub-elements that can be nested inside either Top Level Elements or other Containers to configure how they function. Examples of nested components include the Valve, which represents a reusable unit of work; the Pipeline, which represents a chain of Valves strung together; and a Realm which helps set up container-managed security for a particular container.

Other nested components include the Loader which is used to enforce the specification's guidelines for servlet class loading; the Manager that supports session management for each web application; the Resources component that represents the web application's static resources and a mechanism to access these resources; and the Listener that allows you to insert custom processing at important points in a container's life cycle, such as when a component is being started or stopped.

Not all nested components can be nested within every container.

A final major component, which falls into its own category, is the Connector. It represents the connection end point that an external client (such as a web browser) can use to connect to the Tomcat container.

Before we go on to examine these components, let's take a quick look at how they are organized structurally.

![Alt text](https://github.com/farashahamad/Apache-Tomcat/blob/master/tomcat6-article3-image02.png?raw=true "Optional Title")

When Tomcat is started, the Java Virtual Machine (JVM) instance in which it runs will contain a singleton Server top level element, which represents the entire Tomcat server. A Server will usually contain just one Service object, which is a structural element that combines one or more Connectors (for example, an HTTP and an HTTPS connector) that funnel incoming requests through to a single Catalina servlet Engine.

The Engine represents the core request processing code within Tomcat and supports the definition of multiple Virtual Hosts within it. A virtual host allows a single running Tomcat engine to make it seem to the outside world that there are multiple separate domains (for example, www.my-site.com and www.your-site.com) being hosted on a single machine.

Each virtual host can, in turn, support multiple web applications known as Contexts that are deployed to it. A context is represented using the web application format specified by the servlet specification, either as a single compressed WAR (Web Application Archive) file or as an uncompressed directory. In addition, a context is configured using a web.xml file, as defined by the servlet specification.

A context can, in turn, contain multiple servlets that are deployed into it, each of which is wrapped in a Wrapper component.

The Server, Service, Connector, Engine, Host, and Context elements that will be present in a particular running Tomcat instance are configured using the server.xml configuration file.

### Top Level Components

The Server and Service container components exist largely as structural conveniences. A Server represents the running instance of Tomcat and contains one or more Service children, each of which represents a collection of request processing components.

#### Server

A Server represents the entire Tomcat instance and is a singleton within a Java Virtual Machine, and is responsible for managing the life cycle of its contained services.

The following image depicts the key aspects of the Server component. As shown, a Server instance is configured using the server.xml configuration file. The root element of this file is <Server> and represents the Tomcat instance. Its default implementation is provided using org.apache.catalina.core.StandardServer, but you can specify your own custom implementation through the className attribute of the <Server> element.

![Alt text](https://github.com/farashahamad/Apache-Tomcat/blob/master/tomcat6-article3-image03.png?raw=true "Optional Title")

A key aspect of the Server is that it opens a server socket on port 8005 (the default) to listen a shutdown command (by default, this command is the text string SHUTDOWN). When this shutdown command is received, the server gracefully shuts itself down. For security reasons, the connection requesting the shutdown must be initiated from the same machine that is running this instance of Tomcat.

A Server also provides an implementation of the Java Naming and Directory Interface (JNDI) service, allowing you to register arbitrary objects (such as data sources) or environment variables, by name.

At runtime, individual components (such as servlets) can retrieve this information by looking up the desired object name in the server's JNDI bindings.

While a JNDI implementation is not integral to the functioning of a servlet container, it is part of the Java EE specification and is a service that servlets have a right to expect from their application servers or servlet containers. Implementing this service makes for easy portability of web applications across containers.

While there is always just one server instance within a JVM, it is entirely possible to have multiple server instances running on a single physical machine, each encased in its own JVM. Doing so insulates web applications that are running on one VM from errors in applications that are running on others, and simplifies maintenance by allowing a JVM to be restarted independently of the others. This is one of the mechanisms used in a shared hosting environment (the other is virtual hosting, which we will see shortly) where you need isolation from other web applications that are running on the same physical server.

### Service

While the Server represents the Tomcat instance itself, a Service represents the set of request processing components within Tomcat.

![Alt text](https://github.com/farashahamad/Apache-Tomcat/blob/master/tomcat6-article3-image04.png?raw=true "Optional Title")

A Server can contain more than one Service, where each service associates a group of Connector components with a single Engine.

Requests from clients are received on a connector, which in turn funnels them through into the engine, which is the key request processing component within Tomcat. The image shows connectors for HTTP, HTTPS, and the Apache JServ Protocol (AJP).

There is very little reason to modify this element, and the default Service instance is usually sufficient.

A hint as to when you might need more than one Service instance can be found in the above image. As shown, a service aggregates connectors, each of which monitors a given IP address and port, and responds in a given protocol. An example use case for having multiple services, therefore, is when you want to partition your services (and their contained engines, hosts, and web applications) by IP address and/or port number.

For instance, you might configure your firewall to expose the connectors for one service to an external audience, while restricting your other service to hosting intranet applications that are visible only to internal users. This would ensure that an external user could never access your Intranet application, as that access would be blocked by the firewall.

The Service, therefore, is nothing more than a grouping construct. It does not currently add any other value to the proceedings.

### Connectors

A Connector is a service endpoint on which a client connects to the Tomcat container. It serves to insulate the engine from the various communication protocols that are used by clients, such as HTTP, HTTPS, or the Apache JServ Protocol (AJP).

Tomcat can be configured to work in two modes—Standalone or in Conjunction with a separate web server.

![Alt text](https://github.com/farashahamad/Apache-Tomcat/blob/master/tomcat6-article3-image05.png?raw=true "Optional Title")

In standalone mode, Tomcat is configured with HTTP and HTTPS connectors, which make it act like a full-fledged web server by serving up static content when requested, as well as by delegating to the Catalina engine for dynamic content.

Out of the box, Tomcat provides three possible implementations of the HTTP/1.1 and HTTPS connectors for this mode of operation.

The most common are the standard connectors, known as Coyote which are implemented using standard Java I/O mechanisms.

You may also make use of a couple of newer implementations, one which uses the non-blocking NIO features of Java 1.4, and the other which takes advantage of native code that is optimized for a particular operating system through the Apache Portable Runtime (APR).

Note that both the Connector and the Engine run in the same JVM. In fact, they run within the same Server instance.

![Alt text](https://github.com/farashahamad/Apache-Tomcat/blob/master/tomcat6-article3-image06.png?raw=true "Optional Title")

In conjunction mode, Tomcat plays a supporting role to a web server, such as Apache httpd or Microsoft's IIS. The client here is the web server, communicating with Tomcat either through an Apache module or an ISAPI DLL. When this module determines that a request must be routed to Tomcat for processing, it will communicate this request to Tomcat using AJP, a binary protocol that is designed to be more efficient than the text based HTTP when communicating between a web server and Tomcat.

On the Tomcat side, an AJP connector accepts this communication and translates it into a form that the Catalina engine can process.

In this mode, Tomcat is running in its own JVM as a separate process from the web server.

In either mode, the primary attributes of a Connector are the IP address and port on which it will listen for incoming requests, and the protocol that it supports. Another key attribute is the maximum number of request processing threads that can be created to concurrently handle incoming requests. Once all these threads are busy, any incoming request will be ignored until a thread becomes available.

By default, a connector listens on all the IP addresses for the given physical machine (its address attribute defaults to 0.0.0.0). However, a connector can be configured to listen on just one of the IP addresses for a machine. This will constrain it to accept connections from only that specified IP address.

Any request that is received by any one of a service's connectors is passed on to the service's single engine. This engine, known as Catalina, is responsible for the processing of the request, and the generation of the response.

The engine returns the response to the connector, which then transmits it back to the client using the appropriate communication protocol.

### Container components

In this section, we'll take a look at the key request processing components within Tomcat; the engine, virtual host, and context components.

### Engine

An Engine represents a running instance of the Catalina servlet engine and comprises the heart of a servlet container's function. There can only be one engine within a given service. Being a true container, an Engine may contain one or more virtual hosts as children.

![Alt text](https://github.com/farashahamad/Apache-Tomcat/blob/master/tomcat6-article3-image07.png?raw=true "Optional Title")

Being the primary request processing component, it receives objects that represent the incoming request and the outgoing response. Its main function is to delegate the processing of the incoming request to the appropriate virtual host. If the engine has no virtual host with a name matching the one to which the request should be directed, it consults its defaultHost attribute to determine the host that should be used.

## Virtual host

A virtual host in Tomcat is represented by the Host component, which is a container for web applications, or, in Tomcat parlance, contexts.

Two key concepts come into play when working with virtual hosts—the host's domain name and its application base folder.

* Domain name:Each virtual host is identified by the domain name that you registered for use with this host. This is the value that you expect the client browser to send in the Host: request header. A host's name is required to be unique within its containing engine.
* Application base folder: This folder is the location that contains the contexts that will be deployed to this host. This folder location can either be specified as an absolute path or as a path relative to CATALINA_BASE.

*CATALINA_HOME is an environment variable that references the location of the Tomcat binaries. The CATALINA_BASE environment variable makes it possible to use a single binary installation of Tomcat to run multiple Tomcat instances with different configurations (which are primarily determined by the contents of the conf folder).

*In addition, the use of a CATALINA_BASE location that is separate from CATALINA_HOME keeps the standard binary distribution separate from your installation. This has the beneficial effect of making it easy to upgrade to a newer Tomcat version, without having to worry about clobbering your existing web applications and related configuration files.

### Virtual host techniques

There are two common ways to set up virtual hosting:

* IP-based virtual hosting
* Name-based virtual hosting

### IP-based virtual hosting

With this technique, each FQHN resolves to a separate IP address. However, each of these IP addresses resolves to the same physical machine.

![Alt text](https://github.com/farashahamad/Apache-Tomcat/blob/master/tomcat6-article3-image08.png?raw=true "Optional Title")

You can achieve this by using either of the following mechanisms:

* A multi-homed server, that is, a machine that has multiple physical Network Interface Cards (NICs) installed, each of which has an assigned IP address.
* Using operating system facilities to set up virtual network interfaces by dynamically assigning multiple IP addresses to a single physical NIC.

In either case, the downside is that we need to acquire multiple IP addresses, and these addresses (at least for IPv4) are a limited resource.

The web server is configured to listen on ports that are assigned to each of these IP addresses, and when it detects an incoming request on a particular IP address, it generates the response appropriate for that address.

For example, you can have a web server that is running on a particular physical host that is monitoring port 80 on both 11.156.33.345 and 11.156.33.346. It is configured to respond to requests that are coming in on the former IP address with content that is associated with a particular host name, say www.host1.com, whereas it is www.host2.com for the latter.

When a request comes in on 11.156.33.346, the server knows that it should serve content from the www.host2.com, and does so. To the user, this is indistinguishable from an entirely separate physical server.

### Name-based virtual hosting

![Alt text](https://github.com/farashahamad/Apache-Tomcat/blob/master/tomcat6-article3-image09.png?raw=true "Optional Title")

This is a newer technique that lets you map different domain names to the same IP address. The domain names are registered as normal, and multiple DNS entries exist to map these domain names to the same IP address.

The HTTP/1.1 protocol requires that every request must contain a Host: header that carries the fully qualified host name, as well as the port number (if specified) to which the user wishes to connect. The web server that runs on the host at the IP address will receive this request and will read this header to determine the specific virtual host that should handle this request.

Name-based virtual hosting is preferred for its simplicity and for the fact that it does not use up IP addresses needlessly.

However, you may have to use IP-based virtual hosting when you are using virtual hosts together with SSL. The reason is that the negotiation protocol commits to a certificate before it pays heed to the specific virtual host for which the request is being made. This is because the SSL protocol layer works at a lower level than the HTTP protocol, and the module negotiating this handshake with the client cannot read the HTTP request header until the handshake is complete.

*You may be able to use name-based virtual hosting with SSL if your web server and client supports the Server Name Indication extension as specified in RFC 3546—Transport Layer Security Extensions (http://www.ietf.org/rfc/rfc3546.txt). Using this extension, during the SSL negotiation, the client also transmits the host name to which it is trying to connect, thereby allowing the web server to handle the handshake appropriately by returning the certificate for the correct host name.

#### Virtual host aliasing

Aliasing works by informing the web server that if it sees the aliased domain name in the Host: header, it should be treated in exactly the same manner as the virtual host's domain name.

For example, if you set up swengsol.com as an alias for the www.swengsol.com virtual host, then typing either domain name in the URL will result in the same virtual host being used to process the request.

This works well when a particular host may be known by more than one domain name, and you don't want to clutter your configuration file by creating one set of entries per alias that a user may use to connect to that host.

### Context

A Context, or web application, is where your application specific code (servlets and JSPs) live. It provides a neat way to organize the resources that comprise a given web application.

![Alt text](https://github.com/farashahamad/Apache-Tomcat/blob/master/tomcat6-article3-image10.png?raw=true "Optional Title")

A context maps to a ServletContext instance within the servlet specification. In many ways, the servlet specification is primarily concerned with this context component. For instance, it mandates the format for deploying a context, and dictates the contents of the deployment descriptor.

Important attributes for a context include:

* Document base: This is the path name, either absolute or relative to its containing host's application base, to where its WAR file or exploded folder (its content root) are located.
* Context path: It represents the portion of the URL that uniquely identifies a web application within a given host. It helps the host container to determine which of its deployed contexts should be responsible for handling an incoming request.

One of your contexts may be identified as the default context. This context is then the application that will be invoked when no context path is specified on the URL. This default context is identified by specifying an empty string as its context path, and as such, can be referenced by using a URL that only specifies a hostname. The default application is identified in Tomcat by a folder named ROOT in the application base folder for a given host.

* Automatic reload: A context's resources can be monitored for changes, and the context reloaded automatically when any changes are detected. While this is remarkably useful during development, this is an expensive operation and should be turned off in production.

##### Context configuration

A Context is unique because it has multiple options when it comes to its configuration. We have already noted the presence of the conf/server.xml file that is used to set up the overall structure of the Tomcat instance. While this file's <Context> element can be used to configure a context, this is no longer recommended.

Instead, Tomcat lets you configure a Context by letting you extract the <Context> element from the server.xml file and move it into a separate file called a context fragment file. Context fragments are monitored and reloaded by Tomcat at runtime.

Note that the server.xml file is only ever loaded once at startup.

To ensure a clear separation of contexts by host and engine, Tomcat expects to find context fragments using a specific directory path CATALINA_HOME/conf/<EngineName>/<HostName>/. The context fragments for contexts deployed into this host are found within this folder and are named <ContextPath>.xml.

For the default case, that is, an engine named Catalina and a host named localhost, this works out to be the folder CATALINA_HOME/conf/Catalina/localhost. However, the name of the host could be any valid domain name, for example, www.swengsol.com, resulting in a folder named CATALINA_HOME/conf/Catalina/www.swengsol.com.

In addition, context fragments may also be found embedded within the META-INF folder of a web application's WAR file or exploded directory. In such cases, the fragment must be named context.xml.

Contexts can also be configured using the web application deployment descriptor, web.xml. While the fragment file is proprietary to Tomcat, the deployment descriptor is described by the servlet specification, and therefore is portable across Java EE compliant servlet containers.

### Wrapper

A Wrapper object is a child of the context container and represents an individual servlet (or a JSP file converted to a servlet). It is called a Wrapper because it wraps an instance of a javax.servlet.Servlet.

![Alt text](https://github.com/farashahamad/Apache-Tomcat/blob/master/tomcat6-article3-image11.png?raw=true "Optional Title")

This is the lowest level of the Container hierarchy, and any attempt to add a child to it will result in an exception being thrown.

A wrapper is responsible for the servlet that it represents, including loading it, instantiating it, and invoking its lifecycle methods such as init(), service(), and destroy().

It is also responsible, through its basic valve, for the invocation of the filters that are associated with the wrapped servlet.

## Nested components

These components are specific to the Tomcat implementation, and their primary purpose is to enable the various Tomcat containers to perform their tasks.

### Valve

A valve is a processing element that can be placed within the processing path of each of Tomcat's containers—engine, host, context, or a servlet wrapper. A Valve is added to a container using the <Valve> element in server.xml. They are executed in the order in which they are encountered within the server.xml file.

The Tomcat distribution comes with a number of pre-rolled valves. These include:

* A valve that logs specific elements of a request (such as the remote client's IP address) to a log file or database
* A valve that lets you control access to a particular web application based on the remote client's IP address or host name
* A valve that lets you log every request and response header
* A valve that lets you configure single sign-on access across multiple web applications on a specific virtual host

If these don't meet your needs, you can write your own implementations of org.apache.catalina.Valve and place them into service.

![Alt text](https://github.com/farashahamad/Apache-Tomcat/blob/master/tomcat6-article3-image12.png?raw=true "Optional Title")

A container does not hold references to individual valves. Instead, it holds a reference to a single entity known as the Pipeline, which represents a chain of valves associated with that container.

When a container is invoked to process a request, it delegates the processing to its associated pipeline.

The valves in a pipeline are arranged as a sequence, based on how they are defined within the server.xml file. The final valve in this sequence is known as the pipeline's basic valve. This valve performs the task that embodies the core purpose of a given container.

Unlike individual valves, the pipeline is not an explicit element in server.xml, but instead is implicitly defined in terms of the sequence of valves that are associated with a given container.

Each Valve is aware of the next valve in the pipeline. After it performs its pre processing, it invokes the next Valve in the chain, and when the call returns, it performs its own post processing before returning.

This is very similar to what happens in filter chains within the servlet specification.

In this image, the engine's configured valve(s) fire when an incoming request is received. An engine's basic valve determines the destination host and delegates processing to that host. The destination host's (www.host1.com) valves now fire in sequence. The host's basic valve then determines the destination context (here, Context1) and delegates processing to it. The valves configured for Context1 now fire and processing is then delegated by the context's basic valve to the appropriate wrapper, whose basic valve hands off processing to its wrapped servlet.

The response then returns over the same path in reverse.

A Valve becomes part of the Tomcat server's implementation and provides a way for developers to inject custom code into the servlet container's processing of a request. As a result, the class files for custom valves must be deployed to CATALINA_HOME/lib, rather than to the WEB-INF/classes of a deployed application.

As they are not part of the servlet specification, valves are non-portable elements of your enterprise application. Therefore, if you rely on a particular valve, you will need to find equivalent alternatives in a different application server.

It is important to note that valves are required to be very efficient in order not to introduce inordinate delays into the processing of a request.

### Realm

Container managed security works by having the container handle the authentication and authorization aspects of an application.

Authentication is defined as the task of ensuring that the user is who she says she is, and authorization is the task of determining whether the user may perform some specific action within an application.

The advantage of container managed security is that security can be configured declaratively by the application's deployer. That is, the assignment of passwords to users and the mapping of users to roles can all be done through configuration, which can then be applied across multiple web applications without any coding changes being required to those web applications.

##### Application Managed Security

The alternative is having the application manage security. In this case, your web application code is the sole arbiter of whether a user may access some specific functionality or resource within your application.

For Container managed security to work, you need to assemble the following components:

* Security constraints: Within your web application's deployment descriptor, web.xml, you must identify the URL patterns for restricted resources, as well as the user roles that would be permitted to access these resources.
* Credential input mechanism: In the web.xml deployment descriptor, you specify how the container should prompt the user for authentication credentials. This is usually accomplished by showing the user a dialog that prompts the user for a user name and password, but can also be configured to use other mechanisms such as a custom login form.
* Realm: This is a data store that holds user names, passwords, and roles, against which the user-supplied credentials are checked. It can be a simple XML file, a table in a relational database that is accessed using the JDBC API, or a Lightweight Directory Access Protocol (LDAP) server that can be accessed through the JNDI API. A realm provides Tomcat with a consistent mechanism of accessing these disparate data sources.

All three of the above components are technically independent of each other. The power of container based security is that you can assemble your own security solution by mixing and matching selections from each of these groups.

Now, when a user requests a resource, Tomcat will check to see whether a security constraint exists for this resource. For a restricted resource, Tomcat will then automatically request the user for her credentials and will then check these credentials against the configured realm. Access to the resource will be allowed only if the user's credentials are valid and if the user is a member of the role that is configured to access that resource.

### Executor

This is a new element, available only since 6.0.11. It allows you to configure a shared thread pool that is available to all your connectors. This places an upper limit on the number of concurrent threads that may be started by your connectors. Note that this limit applies even if a particular connector has not used up all the threads configured for it.

### Listener

Every major Tomcat component implements the org.apache.catalina.Lifecycle interface. This interface lets interested listeners to register with a component, to be notified of lifecycle events, such as the starting or stopping of that component.

A listener implements the org.apache.catalina.LifecycleListener interface and implements its lifecycleEvent() method, which takes a LifecycleEvent that represents the event that has occurred.

This gives you an opportunity to inject your own custom processing into Tomcat's lifecycle.

### Manager

Sessions allows 'applications' to be made possible over the stateless HTTP protocol. A session represents a conversation between a client and a server and is implemented by a javax.servlet.http.HttpSession instance that is stored on the server and is associated with a unique identifier that is passed back by the client on each interaction.

A new session is created on request and remains alive on the server either until it times out after a period of inactivity by its associated client, or until it is explicitly invalidated, for instance, by the client choosing to log out.

![Alt text](https://github.com/farashahamad/Apache-Tomcat/blob/master/tomcat6-article3-image13.png?raw=true "Optional Title")

The above image shows a very simplistic view of the session mechanism within Tomcat.

An org.apache.catalina.Manager component is used by the Catalina engine to create, find, or invalidate sessions. This component is responsible for the sessions that are created for a context and their life cycles.

The default Manager implementation simply retains sessions in memory, but supports session survival across server restarts. It writes out all active sessions to disk when the server is stopped and will reload them into memory when the server is started up again.

A <Manager> must be a child of a <Context> element and is responsible for managing the sessions associated with that web application context.

The default Manager takes attributes such as the algorithm that is used to generate its session identifiers, the frequency in seconds with which the manager should check for expired sessions, the maximum number of active sessions supported, and the file in which the sessions should be stored.

Other implementations of Manager are provided that let you persist sessions to a durable data store such as a file or a JDBC database.

### Loader

This element represents the class loader for a given web application. A class loader is a very sacred entity in Java. In its most basic form, it is responsible for locating the bytecode that represents a compiled Java class and interpreting it.

The bytecode for a given class may be found in a variety of locations, the most common being either on the local file system or over the network. A class loader's primary goal is to abstract away the process of how the bytes are obtained and reconstituted into a class in memory.

##### Delegation model

Since Java 2, the class loading mechanism has used a delegating model, where the class loaders within a JVM are organized in a parent-child hierarchy. It is recommended that each class loader first delegate the task of finding and loading a class to its parent before it may attempt to do so itself.

This delegation mechanism ensures that no application can load in a malicious version of a system class (such as java.lang.Object) that may then compromise the integrity of the applications that are running in the JVM.

At the top of this class loader hierarchy is the Bootstrap class loader, called the primordial class loader, which is written in native code and is part of the JVM itself. Being part of the JVM ensures that there is at least one class loader that can be relied upon to load the core Java classes, such as java.lang.Object. This class loader is responsible for loading classes from the core Java packages (such as java.lang or java.util). In the Sun JVM implementation, these classes are found in JAVA_HOME/jre/lib/rt.jar. The Bootstrap class loader is unique in that, it is at the top of the tree, and so has no parent class loader.

Next down the hierarchy is the Extension class loader which, in the Sun JVM, is a java.net.URLClassLoader that monitors the JAVA_HOME/jre/lib/ext folder for extension JARs. Any JARs placed in this folder are automatically loaded without needing to be on the class path.

Finally, there is the System class loader (or Application class loader), which is also a URLClassLoader in the Sun JVM implementation. It monitors the folders and JARs that are described in the CLASSPATH. This class loader is responsible for loading the application's main class.

If a normal application needs to load a class (such as java.lang.String), it will first ask the System class loader for it. The System class loader delegates to the Extension class loader, which in turn delegates to the Bootstrap class loader, which locates the String.class file in rt.jar, loads the class and makes it available as an instance of java.lang.Class.

If an application-specific class file, such as com.swengsol.UserModel.class, is requested, the delegation process works just as before. However, this time the Bootstrap class loader is unable to locate this class in rt.jar. Next, it is the turn of the Extension class loader, and it too is unsuccessful. Finally, the System class loader has a go, and locates the class on its CLASSPATH. This class is then loaded and made available for the JVM to use.

Caching occurs within each class loader, so each must first check its own cache to see if the class was loaded earlier. If a hit is found, then the class is returned right away.

In our previous example, if the application needed to use another String, then the Bootstrap class loader would return its cached instance of the String class.

##### Endorsed Standards Override Mechanism

*Both J2SE 1.4 and 1.5 include a Java API for XML Processing Parser. The classes for this parser are loaded by the Bootstrap class loader, and so take precedence over any parser that you might have installed on your classpath, even if you have a newer version of the parser classes installed. The Endorsed Standards Override Mechanism lets you place overrides to certain classes (CORBA and JAXP classes) in the JAVA_HOME/lib/endorsed folder. The Bootstrap loader will then load these preferentially over any classes that it might otherwise find. For details on this mechanism, see http://java.sun.com/j2se/1.5.0/docs/guide/standards/.

###### Some interesting points to note about class loading are as follows:

* A class is considered fully qualified only when it is described in terms of its package name, its class name, and the class loader instance that was used to load that class. In other words, the same class loaded by two different class loaders is treated as two distinct classes. This has implications for the assignment of instances of this class and treatment of static fields or singletons, even within a single JVM.
* Each class loader can only see the class locations that are above it in the hierarchy. For example, a JAR in the Extension folder cannot use a class file on the application's CLASSPATH. This is because the classes in the Extension folder can only see classes that are served up by either the Extension class loader or the Bootstrap class loader.
* When code in a class references another class, the referenced class is loaded using the same class loader that loaded the referencing class, called its defining class loader. The defining class loader for a class can be obtained using Class.getClassLoader().
* Every thread has a context class loader that can be accessed using Thread.currentThread().getContextClassLoader(). Every time a thread is created, its context class loader is set to that of its creating thread. The class loader for the main() thread is the System class loader, which is automatically propagated down to each worker thread, unless you intervene by invoking Thread.currentThread().setContextClassLoader().

##### Java EE class loading

The Java EE world throws in a bit of a twist into this model.

A servlet container is required to provide a restricted environment for its web applications.

If a servlet were to directly use the System class loader, then it would be able to see every class that was on the class path for the JVM command that was used to start Tomcat. This is potentially a security risk, as a malicious web application (as in a hosting vendor's deployment) may be able to load classes of its sibling web applications.

As a result, each web application must be given its very own class loader, which is placed at the bottom of the tree and preferentially loads classes that are found in the WEB-INF/classes and WEB-INF/lib folders of the web application directory.

This custom class loader will only delegate to its parent class loader when the class that is being requested is one of the standard Java classes.

When a web application needs any other class, instead of delegating to its parent, this custom class loader will first check within the WEB-INFclasses and WEB-INFlib folders.

Only if it is not found there will it delegate to its parent class loader, which will then follow the standard delegating pattern.

###### Tomcat's additional class loaders

![Alt text](https://github.com/farashahamad/Apache-Tomcat/blob/master/tomcat6-article3-image14.png?raw=true "Optional Title")

During startup, Tomcat first neutralizes the System class loader by clearing out the CLASSPATH and resetting it to point to CATALINA_HOME/bin/bootstrap.jar (for the classes required for Tomcat to start up), tomcat-juli.jar (for logging), and tools.jar (for the JSP compiler). This leaves the System class loader useful only for loading a minimal set of Tomcat-specific classes.

Tomcat also changes the endorsed directory to point to CATALINA_HOME/endorsed.

Below it, Tomcat establishes its own hierarchy of class loaders by appending the Server class loader, the Shared class loader, the Common class loader, and one web application class loader per deployed application.

When a web application needs to load a class, the request first comes to the web application class loader, which is responsible (as described above) for loading the classes in the web application's WEB-INF/classes and WEB-INF/lib folders.

This class loader first delegates to the System class loader to allow the delegation hierarchy to locate any core Java classes. If the requested class cannot be found, then the web application class loader attempts to locate the class within its own repositories. If the class is still not found, it will delegate to the Common class loader, or to the Shared class loader if it is installed.

The Shared class loader and the Server class loader are not instantiated by default. You can enable them by editing the CATALINA_HOME/conf/catalina.properties file and adding the shared.loader and server.loader entries.

The Common class loader monitors the contents of the CATALINA_HOME/lib folder, which contains commonly used JARs such as servlet-api.jar, jasper.jar, coyote.jar, and jsp-api.jar.

Classes that are placed in the Shared loader directory will be available to all web applications, but not to Tomcat's internal classes, whereas classes that are placed in the Server loader directory will be available only to Tomcat's internal classes.

##### Class reloading in web applications

Having a web application-specific class loader enables Tomcat to support class reloading.

When a context needs to be redeployed or when a class needs to be reloaded (such as when a recompiled class file is copied into WEB-INFclasses), the entire web application class loader is discarded, and a brand new instance is created to load all the classes for this web application.

This new class loader is now used to service all future requests.

### Logger

The Logger element in server.xml has been deprecated since Tomcat 5.5. Instead, logging in Tomcat 6 is based on the Java Logging API that was introduced in Java 1.4.

Java Logging could only be configured at the entire JVM level and not at the per class loader level. To allow a different configuration file per web application, Tomcat implemented its own Java Logging implementation, known as JULI and implemented in CATALINA_HOME/bin/tomcat-juli.jar.

The global CATALINA_HOME/conf/logging.properties file controls the debug log settings. In addition, each web application can have its own logging configuration file, WEB-INF/classes/logging.properties.

![Alt text](https://github.com/farashahamad/Apache-Tomcat/blob/master/tomcat6-article3-image15.png?raw=true "Optional Title")

As shown in the image above, logging is comprised of the following components:

* Logger: All logging requests are made to Logger objects. These objects are arranged in a hierarchy, rooted at a root logger. This hierarchy mirrors the package hierarchy of classes. Properties can be tied to any level within this hierarchy, and a child Logger inherits properties from its parent.
* Handler: It specifies the location where log messages should be sent. Options include a ConsoleHandler (which writes to the console), a FileHandler (which writes to a file), and a SocketHandler (which writes to a TCP socket).
* Level: This is one of seven levels, SEVERE, WARNING, INFO, CONFIG, FINE, FINER, FINEST, OFF (disabled), and ALL (all messages logged), that define which message types are logged.
* Formatter: This element determines how the information is formatted for display. Tomcat provides both a SimpleFormatter and an XMLFormatter for this purpose.

### Resources

The resources associated with a web application context include static resources such as classes, HTML, JSP, and CSS files. These resources may exist in a variety of storage formats. By default, Tomcat supports retrieval of resources from either a compressed WAR file, or from an exploded folder laid out in the WAR format.

It is conceivable that a context's resources may also be accessed from alternative storage mechanisms, such as a JDBC database. The Resources component makes this possible.

Tomcat provides a directory service implementation of the JNDI API, that supports access of resources in a storage-agnostic manner.

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
