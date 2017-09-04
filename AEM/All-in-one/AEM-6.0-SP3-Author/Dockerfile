##################################
#
# AEM 6.0 SP3 on Centos with Java 8.131
# brigham@adobe.com
#
# All AEM files from from an internal artifactory so are not publically accessible
#
# This is an "all in one" Dockerfile and will be split into multiple Dockerfiles once complete
# 
# v1.0 - 2017.09.01 - this is my first Dockerfile so needs improving
# v1.1 - 2017.09.04 - tidied up with some variables
##################################

FROM centos
MAINTAINER Mike Brigham <brigham@adobe.com>

ENV AEM_RUNMODE author
ENV AEM_PORT 4502
ENV DEBUG_PORT 30303

# update system
RUN yum update

# install wget
RUN yum -y install wget
 
# install java
WORKDIR /opt
RUN wget --no-cookies --no-check-certificate --header "Cookie: gpw_e24=http%3A%2F%2Fwww.oracle.com%2F; oraclelicense=accept-securebackup-cookie" "http://download.oracle.com/otn-pub/java/jdk/8u131-b11/d54c1d3a095b4ff2b6607d096fa80163/jre-8u131-linux-x64.rpm"
RUN yum -y localinstall jre-8u131-linux-x64.rpm
RUN rm -f jre-8u131-linux-x64.rpm
 
# set date-time to UK
RUN rm -f /etc/localtime
RUN ln -s /usr/share/zoneinfo/Europe/London /etc/localtime
 
# Create initial folders
RUN mkdir -p /opt/aem/$AEM_RUNMODE/crx-quickstart/install
RUN mkdir -p /opt/aem/$AEM_RUNMODE/crx-quickstart/bin
   
# Download AEM 6.0
ADD http://brigham-build.corp.adobe.com:8081/artifactory/tam-aem-local/6.0.0/cq-quickstart-6.0.0.jar /opt/aem/$AEM_RUNMODE

# Download license
ADD http://brigham-build:8081/artifactory/tam-aem-local/config/license/license.properties /opt/aem/$AEM_RUNMODE
   
# Unpack AEM
# This step is essential as if you launch AEM without unpacking it first, the docker image build will never complete
WORKDIR /opt/aem/$AEM_RUNMODE

RUN java -jar cq-quickstart-6.0.0.jar -unpack

# Download SP3 & install config files to install folder
ADD http://brigham-build:8081/artifactory/tam-aem-local/6.0.3/AEM-6.0-Service-Pack-3-6.0.SP3.zip /opt/aem/$AEM_RUNMODE/crx-quickstart/install
ADD http://brigham-build:8081/artifactory/tam-aem-local/config/mk/tarmk/$AEM_RUNMODE/repo/org.apache.jackrabbit.oak.plugins.blob.datastore.FileDataStore.config /opt/aem/$AEM_RUNMODE/crx-quickstart/install
ADD http://brigham-build:8081/artifactory/tam-aem-local/config/mk/tarmk/$AEM_RUNMODE/repo/org.apache.jackrabbit.oak.plugins.segment.SegmentNodeStoreService.config /opt/aem/$AEM_RUNMODE/crx-quickstart/install

# Copy modified Start file
ADD http://brigham-build:8081/artifactory/tam-aem-local/config/mk/tarmk/$AEM_RUNMODE/bin/start /opt/aem/$AEM_RUNMODE/crx-quickstart/bin

# Start AEM & add post first run config
RUN chmod 777 -R /opt/aem
WORKDIR /opt/aem/$AEM_RUNMODE
RUN crx-quickstart/bin/start && \
    
# Do not proceed until AEM is running
until $(curl -u admin:admin --output /dev/null --silent --head --fail http://localhost:$AEM_PORT); do printf '.'; sleep 5; done
    
    
EXPOSE $AEM_PORT
EXPOSE $DEBUG_PORT    


# Launch AEM when image starts
CMD /opt/aem/$AEM_RUNMODE/crx-quickstart/bin/start
