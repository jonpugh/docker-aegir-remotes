# 
# This Dockerfile sets up an Aegir "remote server" using Apache 
# See http://community.aegirproject.org/node/30 for more info.
#
# Stole some stuff from https://github.com/CenturyLinkLabs/docker-apache-php
#

#   docker run --env AUTHORIZED_KEYS='aegir server master ssh key' 

FROM ubuntu:trusty
MAINTAINER Jon Pugh <jon@thinkdrop.net>

# Set authorized keys on docker container creation:
ENV AUTHORIZED_KEYS  ''

# Update and Upgrade Apt
RUN apt-get update
RUN DEBIAN_FRONTEND=noninteractive apt-get -y upgrade

# Install required packages
RUN DEBIAN_FRONTEND=noninteractive apt-get -y install rsync apache2 php5 php5-cli php5-mysql php5-curl php5-gd php-pear php-apc postfix mysql-client sudo supervisor

# Setup aegir user
RUN adduser --system --group --home /var/aegir aegir
RUN adduser aegir www-data  
RUN echo 'aegir ALL=NOPASSWD: /usr/sbin/apache2ctl' >> /etc/sudoers.d/aegir
RUN chsh -s /bin/bash aegir
RUN mkdir /var/aegir/.ssh
RUN mkdir /var/aegir/config
RUN chmod 600 /var/aegir/.ssh
RUN echo $AUTHORIZED_KEYS >> /var/aegir/.ssh/authorized_keys

# Configure apache
RUN a2enmod rewrite
RUN service apache2 restart
RUN ln -s /var/aegir/config/apache.conf /etc/apache2/conf.d/aegir.conf

# Save start and run scripts
ADD start.sh /start.sh
ADD run.sh /run.sh
RUN chmod 755 /start.sh
RUN chmod 755 /run.sh

ADD supervisord-apache2.conf /etc/supervisor/conf.d/supervisord-apache2.conf

EXPOSE 80
CMD ["/run.sh"]
