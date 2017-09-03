##################################
#
# AEM 6.0 SP3 on Centos with Java 8.131
# brigham@adobe.com
#
# All AEM files from from an internal artifactory so are not publically accessible
#
# v1.0 - 2017.09.01 - this is my first Dockerfile so needs improving - everything is hardcoded at the moment - this will change in later versions
#
##################################

FROM centos
MAINTAINER Mike Brigham <brigham@adobe.com>

EXPOSE 4502
EXPOSE 80

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
RUN mkdir -p /opt/aem/author/crx-quickstart/install
RUN mkdir -p /opt/aem/author/crx-quickstart/bin
   
# Download AEM 6.0
ADD http://brigham-build.corp.adobe.com:8081/artifactory/tam-aem-local/6.0.0/cq-quickstart-6.0.0.jar /opt/aem/author

# Download license
ADD http://brigham-build:8081/artifactory/tam-aem-local/config/license/license.properties /opt/aem/author
   
# Unpack AEM
# This step is essential as if you launch AEM without unpacking it first, the docker image build will never complete
WORKDIR /opt/aem/author

RUN java -jar cq-quickstart-6.0.0.jar -unpack

# Download SP3 & install config files to install folder
ADD http://brigham-build:8081/artifactory/tam-aem-local/6.0.3/AEM-6.0-Service-Pack-3-6.0.SP3.zip /opt/aem/author/crx-quickstart/install
ADD http://brigham-build:8081/artifactory/tam-aem-local/config/mk/tarmk/author/repo/org.apache.jackrabbit.oak.plugins.blob.datastore.FileDataStore.config /opt/aem/author/crx-quickstart/install
ADD http://brigham-build:8081/artifactory/tam-aem-local/config/mk/tarmk/author/repo/org.apache.jackrabbit.oak.plugins.segment.SegmentNodeStoreService.config /opt/aem/author/crx-quickstart/install

# Copy modified Start file
ADD http://brigham-build:8081/artifactory/tam-aem-local/config/mk/tarmk/author/bin/start /opt/aem/author/crx-quickstart/bin

# Start AEM & add post first run config
RUN chmod 777 -R /opt/aem
WORKDIR /opt/aem/author
RUN crx-quickstart/bin/start && \
    
# Do not proceed until AEM is running
until $(curl -u admin:admin --output /dev/null --silent --head --fail http://localhost:4502); do printf '.'; sleep 5; done
    
# Add post first run config
#WORKDIR /opt
#ADD http://brigham-build:8081/artifactory/tam-aem-local/config/mk/tarmk/author/config/brigham-config-author-1.0.zip /opt
#RUN curl -u admin:admin -F file=@"brigham-config-author-1.0.zip" -F name="brighamConfigAuthor" -F force=true -F install=true http://localhost:4502/crx/packmgr/service.jsp
#RUN rm -f brigham-config-author-1.0.zip
    
# Once all config has been added, stop AEM
# RUN crx-quickstart/bin/stop


# End of Dockerfile
