In the last step, we played with Tomcat 

## What are the main issues?

## What is missing?

## Writing a geoserver Dockerfile
Basically, geoserver is a 
Please follow the following steps (https://github.com/geosolutions-it/docker-geoserver) and answer questions : 
- What type of build is this ?
- Is this an official DockerFile?
- Is it actively maintained?
- Which version of geoserver is deployed?
- Is it scaleable?
- What extensions are deployed?
- How much RAM is allowed and how to extend it?
- How to create a cluster?

If at least one question misses a quick or positive answer, you'd better build one by yourself at least for development!

## Building its own geoserver Dockerfile 
Let's try to master a much simpler deployment.
We begin by using the `tomcat` application server.

```
FROM tomcat:9-jdk11
LABEL maintainer="Elastic Labs <contact@elasticlabs.co>"
```

Right! 
Now, prepare some useful variables to keep control of what we're doing.

```
ENV \
    # Geoserver installation basics
    GEOSERVER_VERSION=2.18.0 \
    GEOSERVER_DATA_DIR=/var/local/geoserver \
    GEOSERVER_ADMIN_USER=admin \
    GEOSERVER_ADMIN_PASSWORD=myawesomegeoserver \
    # https://docs.geoserver.org/stable/en/user/production/container.html#optimize-your-jvm
    INITIAL_MEMORY=1G \
    MAXIMUM_MEMORY=2G
```

Deduce the Geoserver WAR location : 
Navigate to the official repository and find a way to use above variables to build a valid URL pattern

```
# Geoserver WAR location 
ENV WAR_URL=???
```

Now, proceed with the download & building of Geoserver

```
# Build (2/ ) Download & deploy GeoServer
RUN mkdir ${GEOSERVER_DATA_DIR} \
    # Get and deploy Geoserver version
	&& wget -c --progress=bar --no-check-certificate ${WAR_URL} -O /tmp/resources/geoserver-${GEOSERVER_VERSION}.zip \
	&& unzip /tmp/resources/geoserver-${GEOSERVER_VERSION}.zip -d /tmp/geoserver \
	&& unzip /tmp/geoserver/geoserver.war -d ${CATALINA_HOME}/webapps/geoserver \
    # Move necessary files outside default deployment directory
    && cp -r ${CATALINA_HOME}/webapps/geoserver/data/user_projections ${GEOSERVER_DATA_DIR} \
    && cp -r ${CATALINA_HOME}/webapps/geoserver/data/security ${CATALINA_HOME} \
    && rm -rf ${CATALINA_HOME}/webapps/geoserver/data \
    && rm -rf /tmp/geoserver \
    # Remove Tomcat manager, docs, and examples
    && rm -rf ${CATALINA_HOME}/webapps/ROOT \
    && rm -rf ${CATALINA_HOME}/webapps/docs \
    && rm -rf ${CATALINA_HOME}/webapps/examples \
    && rm -rf ${CATALINA_HOME}/webapps/host-manager \
    && rm -rf ${CATALINA_HOME}/webapps/manager; 
```

Finally, set the environment and entrypoint 

```
# Tomcat environment
ENV CATALINA_OPTS "-server -Djava.awt.headless=true \
	-Xms${INITIAL_MEMORY} -Xmx${MAXIMUM_MEMORY} -XX:+UseConcMarkSweepGC -XX:NewSize=48m -DGEOSERVER_DATA_DIR=${GEOSERVER_DATA_DIR}"

# Create tomcat user to avoid root access. 
RUN addgroup --gid 1099 tomcat && useradd -m -u 1099 -g tomcat tomcat \
    && chown -R tomcat:tomcat . \
    && chown -R tomcat:tomcat ${GEOSERVER_DATA_DIR} \
    && chown -R tomcat:tomcat ${CATALINA_HOME}/webapps/geoserver/

ADD .conf/gs/entrypoint.sh /usr/local/bin/entrypoint.sh
ENTRYPOINT [ "/bin/sh", "/usr/local/bin/entrypoint.sh"]
```

Now, build & run the container! 
What commands will you use? 
- **Guess & chat!**

