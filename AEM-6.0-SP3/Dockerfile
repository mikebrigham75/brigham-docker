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

EXPOSE 4502:4502

# update system
RUN yum update

# install wget
RUN yum -y install wget
 
# install java
RUN wget --no-cookies --no-check-certificate --header "Cookie: gpw_e24=http%3A%2F%2Fwww.oracle.com%2F; oraclelicense=accept-securebackup-cookie" "http://download.oracle.com/otn-pub/java/jdk/8u131-b11/d54c1d3a095b4ff2b6607d096fa80163/jre-8u131-linux-x64.rpm"
RUN yum -y localinstall jre-8u131-linux-x64.rpm
 
# set date-time to UK
RUN rm -f /etc/localtime
RUN ln -s /usr/share/zoneinfo/Europe/London /etc/localtime
 
# Create initial folders & Entry point
RUN mkdir -p /opt/aem/author/crx-quickstart/install
RUN mkdir -p /opt/aem/author/crx-quickstart/bin
ENTRYPOINT /opt/aem/author/crx-quickstart/bin
   
# Download AEM 6.0
ADD http://brigham-build.corp.adobe.com:8081/artifactory/tam-aem-local/6.0.0/cq-quickstart-6.0.0.jar /opt/aem/author

# Download license
ADD http://brigham-build:8081/artifactory/tam-aem-local/config/license/license.properties /opt/aem/author
   
# Download SP3 & install config files to install folder
ADD http://brigham-build:8081/artifactory/tam-aem-local/6.0.3/AEM-6.0-Service-Pack-3-6.0.SP3.zip /opt/aem/author/crx-quickstart/install
ADD http://brigham-build:8081/artifactory/tam-aem-local/config/mk/tarmk/author/repo/org.apache.jackrabbit.oak.plugins.blob.datastore.FileDataStore.config /opt/aem/author/crx-quickstart/install
ADD http://brigham-build:8081/artifactory/tam-aem-local/config/mk/tarmk/author/repo/org.apache.jackrabbit.oak.plugins.segment.SegmentNodeStoreService.config /opt/aem/author/crx-quickstart/install

# Start AEM
WORKDIR /opt/aem/author
RUN java -Xms8g -Xms8g -jar cq-quickstart-6.0.0.jar -r author,crx3 -p 4502
#RUN chmod 777 -R /opt/aem/author/crx-quickstart/bin
#RUN ./start


# Copy modified Start file
# ADD http://brigham-build:8081/artifactory/tam-aem-local/config/mk/tarmk/author/bin/start /opt/aem/author/crx-quickstart/bin

# Download & install configurations (Set to Classic mode & add oak-query,log)
#ADD http://brigham-build:8081/artifactory/tam-aem-local/config/mk/tarmk/author/config/brigham-config-author-1.0.zip /opt
#curl -u admin:admin -F file=@"/opt/brigham-config-author-1.0.zip" -F name="brighamConfigAuthor" -F force=true -F install=true http://localhost:4502/crx/packmgr/service.jsp
