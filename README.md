# Variant Experience Server </br> Standard Server-side Extensions
### Release 0.9.3
#### Requires: Java 8 or later

[__Documentation__](https://www.getvariant.com/resources/docs/0-9/experience-server/reference/#section-4) | [__Javadoc__](https://getvariant.github.io/variant-extapi-standard/)

This project contains the library of standard extension objects for the [Variant Experience Server's](https://www.getvariant.com/resources/docs/0-9/experience-server/user-guide/) [server-side extension API, or ExtAPI](https://www.getvariant.com/resources/docs/0-9/experience-server/reference/#section-4). In it's built and packaged form, as a JAR file, it is included with the Variant Server distribution. 

Server-side extensions are run by and provide runtime customization for Variant Experience server. They can be one of two types: _Lifecycle Hooks_ and _Event Flushers_. 

### 1. Lifecycle Hooks 
Lifecycle hooks are callback methods subscribed to Variant server's life-cycle events. Whenever a lifecycle event is raised (e.g. the variation quealification event is raised when a user session is about to be qualified for a variation) all hooks subscribed to it are posted.  For more information, see [Variant User Guide](https://www.getvariant.com/resources/docs/0-9/experience-server/user-guide/#section-4.7.1) for more information.

#### [ChromeTargetingHook](https://github.com/getvariant/variant-extapi-standard/blob/master/src/main/java/com/variant/extapi/standard/hook/ChromeTargetingHook.java)

Illustrates the use of a targeting hook. Used in the [Demo application](https://github.com/getvariant/variant-java-demo). Assigns all Chrome traffic to the control experience.

Configuration:
```
   'hooks': [
      ...
      {
         'name': 'myHook',
         'class': 'com.variant.extapi.standard.hook.ChromeTargetingHook'
      }
      ...
   ]
```
### 2. Trace Event Flushers
Event flushers handle the terminal ingestion of Variant trace events. They are responsible for writing out Variant trace events to some form of external storage, suitable for your technology stack, so they can be later used for analysis of Variant experiments. See [Variant User Guide](https://www.getvariant.com/resources/docs/0-9/experience-server/user-guide/#section-4.7.2) for more information.

#### [TraceEventFlusherH2](https://github.com/getvariant/variant-extapi-standard/blob/master/src/main/java/com/variant/extapi/standard/flush/jdbc/TraceEventFlusherH2.java)

Writes trace events to an H2 database.  

Configuration:
For server-wide default configuration, which applies to all schemas managed by a Variant server that do not define their own flusher.
```
variant.event.flusher.class.name = com.variant.extapi.standard.flush.jdbc.TraceEventFlusherH2
variant.event.flusher.class.init = {"url":"jdbc:h2:<url>","user":"<user>","password":"<password>"}
 ```
 
For schema-specific configuration (overrides the server-wide default):
```
   'flusher': {
      'class': 'com.variant.extapi.standard.flush.jdbc.TraceEventFlusherH2',
      'init': {'url':'jdbc:h2:<url>','user':'<user>','password':'<password>'}
   }
```

### Adding Standard Extensions to Your Variant Server Instance
```
% git clone https://github.com/getvariant/variant-server-extapi.git
```

The Variant ExtAPI is provided in the `lib/variant-server-extapi-\<release\>.jar` JAR file and the dependent library `lib/variant-core-\<release\>.jar`. You may either directly import these into your project or, if you use a dependency management tool like Maven, install them into your local Maven repository:

```
% mvn install:install-file -Dfile=/path/to/variant-server-extapi-<release>.jar -DgroupId=com.variant \
                -DartifactId=variant-server-extapi -Dversion=<release> -Dpackaging=jar

% mvn install:install-file -Dfile=/path/to/variant-core-<release>.jar -DgroupId=com.variant \
                -DartifactId=variant-core -Dversion=<release> -Dpackaging=jar
```

Note that the repository contains several sample objects in `/src/main/java/com/variant/server/ext/demo/`. These are provided for illustration only and can be removed if you don't need them.

To make your extension classes available to Variant server at run time, you must package them into a JAR file and copy the jar file into Variant server's `ext/` directory, along with all the dependencies.

To package objects in this repository:

```
% mvn package
```

This will build the distribution JAR file in the `target/` directory, which you need to copy into Variant server's `ext/` directory, along with all the dependencies.

