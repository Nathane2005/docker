#
# Unless explicitly acquired and licensed from Licensor under another license, the contents of
# this file are subject to the Reciprocal Public License ("RPL") Version 1.5, or subsequent
# versions as allowed by the RPL, and You may not copy or use this file in either source code
# or executable form, except in compliance with the terms and conditions of the RPL
#
# All software distributed under the RPL is provided strictly on an "AS IS" basis, WITHOUT
# WARRANTY OF ANY KIND, EITHER EXPRESS OR IMPLIED, AND LICENSOR HEREBY DISCLAIMS ALL SUCH
# WARRANTIES, INCLUDING WITHOUT LIMITATION, ANY WARRANTIES OF MERCHANTABILITY, FITNESS FOR A
# PARTICULAR PURPOSE, QUIET ENJOYMENT, OR NON-INFRINGEMENT. See the RPL for specific language
# governing rights and limitations under the RPL.
#
# http://opensource.org/licenses/RPL-1.5
#
# Copyright 2012-2016 Open Justice Broker Consortium
#

# Dockerfile to build a Saiku container running on Java 8

FROM ojbc/java8-server-base

MAINTAINER Open Justice Broker Consortium "http://www.ojbc.org"

WORKDIR /tmp

# Build saiku-latest.zip from source:
#  1. check out the ojbc/saiku repo from GH
#  2. mvn install -DskipTests
#  3. zip saiku-server/target/dist into a zipfile (i.e., the root of the zipfile should contain a single directory, the saiku-server subdirectory of saiku-server/target/dist)
#  4. name that zipfile saiku-latest.zip and copy it into the files/ subdir here.

COPY files/saiku-latest.zip /tmp/
COPY files/*.jar /tmp/

RUN	mkdir -p /opt && \
    unzip -d /opt/ saiku-latest.zip && \
	rm -f saiku-latest.zip && \
	sed -i "s/sh startup.sh/\/opt\/saiku-server\/tomcat\/bin\/catalina.sh run/g" /opt/saiku-server/start-saiku.sh && \
    sed -i s/8080/80/g /opt/saiku-server/tomcat/conf/server.xml

# todo: at some point, might be nice to get all the various versions of xerces out of the saiku codebase. for now, we just copy in
# a current version.

RUN rm /opt/saiku-server/tomcat/webapps/saiku/WEB-INF/lib/xercesImpl-2.5.0.jar && \
    cp /tmp/xercesImpl-2.11.0.jar /opt/saiku-server/tomcat/webapps/saiku/WEB-INF/lib/

# turn up logging
RUN sed -rie "s/(.+)Threshold(.+)WARN(.+)/\1Threshold\2INFO\3/g" /opt/saiku-server/tomcat/webapps/saiku/WEB-INF/classes/log4j.xml

RUN cd /opt/saiku-server/tomcat

CMD ["/opt/saiku-server/start-saiku.sh"]
