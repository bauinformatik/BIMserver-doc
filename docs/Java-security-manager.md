# Java Virtual Machine (JVM) Security Manager

The Java Security Manager is a framework that controls the permission of Java applications. It is essential to run BIMserver in environments where security restrictions are required.

**Note:** Tomcat does have support for running with the Java Security Manager, but historically, it hasn't been extensively tested in production environments with the Security Manager enabled. The Tomcat team has recognized that the Security Manager might lead to unexpected issues or require significant customization. As a result, enabling the Security Manager might lead to unexpected errors or behaviors. We recommend thorough testing in your environment.

Below is the list of permissions that should be added to your security policy file to allow BIMserver to function correctly. The policy file should be updated as follows. Please let us know if things are missing or not required anymore:

```
grant { 
        // Read only file permissions on eclipse workspace, you wont need this on an application server
        permission java.io.FilePermission "..", "read";
        permission java.io.FilePermission "../-", "read";

        // Read only file permissions on local git repository, you wont need this on an application server
        permission java.io.FilePermission "C:/Users/Ruben/git/-", "read";

        // Read/Write/Delete file permissions on home directory
        permission java.io.FilePermission "home/-", "read, write, delete";

        // Needed to catch all exceptions
        permission java.lang.RuntimePermission "setDefaultUncaughtExceptionHandler";

        // Permissions needed for OSGI (needed for Eclipse code that parses Java code to AST)
        // TODO this sucks, eclipse its OSGI implementation is requesting all property permissions (read and write!!)
        permission java.util.PropertyPermission "*", "read, write";

        // Needed to suppress logging to sysout/syserr of IFC schema parser (which uses antlr)
        permission java.lang.RuntimePermission "setIO";

        // Allow the webserver to accept incoming connections on port 8080
        permission java.net.SocketPermission "*", "listen, accept, connect, resolve";

        // Needed for java reflection
        permission java.lang.RuntimePermission "accessClassInPackage.sun.reflect.generics.reflectiveObjects";

        // Needed for Jetty (Embedded Webserver)
        permission java.lang.RuntimePermission "setContextClassLoader";

        // Needed for BerkeleyDB
        permission java.util.logging.LoggingPermission "control";
        permission java.lang.management.ManagementPermission "monitor";
        
        // Needed for JAXB serialization/deserialization, it sounds very broad, but its not really a security issue
        permission java.lang.reflect.ReflectPermission "suppressAccessChecks";
        permission java.lang.RuntimePermission "accessDeclaredMembers";
        
        // Needed by CXF (Web Services)
        permission javax.xml.ws.WebServicePermission "publishEndpoint";
        
        // Needed by EMF
        permission java.lang.RuntimePermission "getClassLoader";
        permission java.lang.RuntimePermission "accessClassInPackage.com.sun.org.apache.xerces.internal.parsers";

        // Needed for PluginManager
        permission java.lang.RuntimePermission "createClassLoader";

        // Permissions to read system properties
        permission java.util.PropertyPermission "java.version", "read";
        permission java.util.PropertyPermission "java.vendor", "read";
        permission java.util.PropertyPermission "java.vendor.url", "read";
        permission java.util.PropertyPermission "java.class.version", "read";
        permission java.util.PropertyPermission "java.class.path", "read";
        permission java.util.PropertyPermission "os.name", "read";
        permission java.util.PropertyPermission "os.version", "read";
        permission java.util.PropertyPermission "os.arch", "read";
        permission java.util.PropertyPermission "file.separator", "read";
        permission java.util.PropertyPermission "path.separator", "read";
        permission java.util.PropertyPermission "line.separator", "read";
        permission java.util.PropertyPermission "user.dir", "read";
        
        permission java.util.PropertyPermission "java.specification.version", "read";
        permission java.util.PropertyPermission "java.specification.vendor", "read";
        permission java.util.PropertyPermission "java.specification.name", "read";
        
        permission java.util.PropertyPermission "java.vm.specification.version","read";
        permission java.util.PropertyPermission "java.vm.specification.vendor","read";
        permission java.util.PropertyPermission "java.vm.specification.name", "read";
        permission java.util.PropertyPermission "java.vm.version", "read";
        permission java.util.PropertyPermission "java.vm.vendor", "read";
        permission java.util.PropertyPermission "java.vm.name", "read";
};
```