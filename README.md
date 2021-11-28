[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Discord Chat](https://img.shields.io/badge/chat-discord-7289da)](https://discord.gg/gzKFYnpmCY)

OpenLiberty Faces
==========================
[![LibertyFaces Logo](https://github.com/melloware/liberty-faces/blob/main/src/site/LibertyFaces.svg)](https://github.com/melloware/liberty-faces)


### Goals
***
The main goal was to take an out of the box JSF application ([PrimeFaces Showcase](https://github.com/primefaces/primefaces-showcase)) 
and run it in both a Java EE Server and in [OpenLiberty](https://openliberty.io/). 
Some addition goals:
- See how much we can improve performance by incorporating various optimization tricks for JSF applications
- See if OpenLiberty is a viable option for JSF and migrating to Docker containers

### Environment
***
- OpenJDK 11.0.10
- JBoss Wildfly 18.0.1
- OpenLiberty 21.0
- JSF Production Mode
- Intel(R) Core(TM) i7-8750H CPU @2.21 GHz 16GB RAM

### Optimizations
***
- Apache MyFaces (Liberty) instead of Jakarta Mojarra (Wildfly)
- PrimeFaces [MOVE_SCRIPTS_TO_BOTTOM](https://primefaces.github.io/primefaces/10_0_0/#/gettingstarted/configuration?id=configuration)
- OmniFaces [GzipResponseFilter](https://showcase.omnifaces.org/filters/GzipResponseFilter)
- OmniFaces [CombinedResourceHandler](https://showcase.omnifaces.org/resourcehandlers/CombinedResourceHandler)
- PrimeFaces Extensions [CombinedResourceHandler Helper](https://github.com/primefaces-extensions/primefaces-extensions/issues/293) 
- PrimeFaces Extensions [Optimizer](https://github.com/primefaces-extensions/resources-optimizer-maven-plugin)
- jQuery [Hide Page Until Complete](https://stackoverflow.com/questions/9550760/hide-page-until-everything-is-loaded-advanced/28129691#28129691)

### Metrics
***
The following client and server metrics were captured while hitting the exact same page [/datatable/crud.xhtml](https://www.primefaces.org/showcase/ui/data/datatable/crud.xhtml)
Using `Incognito Mode` and pressing CTRL+F5 so it forced the browser to load all resources from the server with nothing cached.

Metric                |  WildFly EE | Liberty        | Liberty (optimized) | Improvement |
----------------------| ----------  | ---------------| --------------------|-------------|
Package Size          | 48.5 MB WAR | 169.0 MB JAR   | 169.0 MB JAR        | -248.45%    |
Cold Startup          | 10.3 s      | 24.23 s        | 24.23 s             | -135.24%    |
Memory Used           | 140 MB      | 93.8 MB        | 93.8 MB             | 33.00%      |
HTTP Requests         | 114         | 114            | 89                  | 21.93%      |
Resource Size         | 4.4 MB      | 4.4 MB         | 4.4 MB              | -----       |
Transferred Size      | 4.4 MB      | 4.4 MB         | 2.9 MB              | 34.09%      |
DOM Loaded            | 1150 ms     | 1060 ms        | 916 ms              | 20.35%      |
Lighthouse Score      | 59/100      | 46/100         | 94/100              | 42.37%      |
First Paint           | 2.4 s       | 3.1 s          | 0.8 s               | 66.67%      |
Largest Paint         | 2.7 s       | 4.3 s          | 1.4 s               | 48.15%      |
Speed Index           | 2.4 s       | 3.1 s          | 1.2 s               | 50.00%      |
Time To Interactive   | 3.9 s       | 4.4 s          | 1.4 s               | 64.10%      |


### Development

***
To run the example in Dev mode:

```
git clone https://github.com/melloware/liberty-faces
cd liberty-faces
mvn clean compile liberty:dev
```

Then open your web browser to http://localhost:8080/showcase/

### Production

***
To run the example in HotSpot Production mode (GraalVM native-image not supported):

```
git clone https://github.com/melloware/liberty-faces
cd liberty-faces
mvn clean package liberty:package
java -jar target/liberty-faces.jar
```

Then open your web browser to http://localhost:8080/


### Docker

***
```
mvn clean package liberty:package
docker build -f docker/Dockerfile -t melloware/liberty-faces . 
docker run -i --rm -p 8080:8080 melloware/liberty-faces
```

OR run already pushed image:
```
docker run -i --rm -p 8080:8080 melloware/liberty-faces
