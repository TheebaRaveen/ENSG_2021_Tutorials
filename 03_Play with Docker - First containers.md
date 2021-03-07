## Some useful links : 

* Docker cheat sheet : https://www.docker.com/sites/default/files/d8/2019-09/docker-cheat-sheet.pdf
* Docker stats & metrics : https://docs.docker.com/config/containers/runmetrics/ or https://docs.docker.com/engine/reference/commandline/stats/

## Play with your first container : Apache Tomcat 

Apache Tomcat on docker Hub : https://hub.docker.com/_/tomcat

Note: as of docker-library/tomcat#181, the upstream-provided (example) webapps are not enabled by default, per upstream's security recommendations, but are still available under the webapps.dist folder within the image to make them easier to re-enable.

Run the default Tomcat server (CMD ["catalina.sh", "run"]):

```
$ docker run -it --rm tomcat:9.0
```
You can test it by visiting http://container-ip:8080 in a browser or, if you need access outside the host, on port 8888:

```
$ docker run -it --rm -p 8888:8080 tomcat:9.0
```
You can then go to http://localhost:8888 or http://host-ip:8888 in a browser (noting that it will return a 404 since there are no webapps loaded by default).

The default Tomcat environment in the image is:

```
CATALINA_BASE:   /usr/local/tomcat
CATALINA_HOME:   /usr/local/tomcat
CATALINA_TMPDIR: /usr/local/tomcat/temp
JRE_HOME:        /usr
CLASSPATH:       /usr/local/tomcat/bin/bootstrap.jar:/usr/local/tomcat/bin/tomcat-juli.jar
```
The configuration files are available in /usr/local/tomcat/conf/. By default, no user is included in the "manager-gui" role required to operate the "/manager/html" web application. If you wish to use this app, you must define such a user in tomcat-users.xml.

## Activate the /manager/html web application

First, we need to define a Tomcat user that has access to the manager application. This user is defined in a file called tomcat-users.xml and will be assigned both the manager-gui and manager-script roles, which grant access to the manager HTML interface as well as the API:
```
<tomcat-users>
  <role rolename="manager-gui"/>
  <role rolename="manager-script"/>
  <user username="tomcat" password="s3cret" roles="manager-gui,manager-script"/>
</tomcat-users>
```

### Expose the manager
By default, the manager application will only accept traffic from localhost. Keep in mind that from the context of a Docker image, localhost means the container's loopback interface, not that of the host. With port forwarding enabled, traffic to an exposed port enters the Docker container via the container's external interface and will be blocked by default. Here we have a copy of the manager applications context.xml file with the network filtering disabled:

```
<Context antiResourceLocking="false" privileged="true" >
  <!--
    <Valve className="org.apache.catalina.valves.RemoteAddrValve"
         allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />
  -->
  <Manager sessionAttributeValueClassNameFilter="java\.lang\.(?:Boolean|Integer|Long|Number|String)|org\.apache\.catalina\.filters\.CsrfPreventionFilter\$LruCache(?:\$1)?|java\.util\.(?:Linked)?HashMap"/>     
</Context>
```

This blog post (https://pythonspeed.com/articles/docker-connection-refused/) provides some more detail about Docker networking with forwarded ports.

### Run the container
The final hurdle to jump is the fact that the Tomcat Docker image does not load any applications by default. The default applications, such as the manager application, are saved in a directory called /usr/local/tomcat/webapps.dist. We need to move this directory to /usr/local/tomcat/webapps. This is achieved by overriding the command used when launching the container.

The command below maps the two custom XML files we created above (saved to /tmp in this example), moves /usr/local/tomcat/webapps.dist to /usr/local/tomcat/webapps, and finally launches Tomcat:

sudo docker run \
  --name tomcat \
  -it \
  -p 8080:8080 \
  -v /tmp/tomcat-users.xml:/usr/local/tomcat/conf/tomcat-users.xml \
  -v /tmp/context.xml:/tmp/context.xml \
  tomcat:9.0 \
  /bin/bash -c "mv /usr/local/tomcat/webapps /usr/local/tomcat/webapps2; mv /usr/local/tomcat/webapps.dist /usr/local/tomcat/webapps; cp /tmp/context.xml /usr/local/tomcat/webapps/manager/META-INF/context.xml; catalina.sh run"

### Access the manager application
To open the manager application, open the URL http://localhost:8080/manager/html. Enter `tomcat` for the username, and `s3cret` for the password:

### Bonus candy : access the application from your host machine!

### Bonus candy 2 : find & run the Mapstore2 Web application, then access it from your host!