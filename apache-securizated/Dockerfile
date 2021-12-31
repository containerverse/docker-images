FROM ubuntu:20.04

LABEL maintainer="Luis Acosta <luis13cst@gmail.com>" 

# WORKDIR

RUN  \
  apt-get update && apt-get upgrade -y && \
  apt-get install openssl apache2 nano -y

# Definir el usuario y grupo que ejecuta el servicio apache

RUN export APACHE_RUN_USER=www-data
RUN export APACHE_RUN_GROUP=www-data

# Desactivar firma de servidor

RUN sed -i \
        -e 's/ServerTokens OS/ServerTokens ProductOnly/g' \
        -e 's/ServerSignature On/ServerSignature Off/g' \
        /etc/apache2/conf-enabled/security.conf

# Habilitar SSL

RUN a2enmod ssl && service apache2 restart
RUN mkdir /etc/apache2/ssl
WORKDIR /etc/apache2/ssl
RUN openssl req -x509 -nodes -keyout luiscom.key -out luiscom.crt -days 365 \
    -subj "/C=ES/ST=Spain/L=Locality/O=Acosta INC/OU=IT Department/CN=luis.com"

# Virtualhost luis.com

RUN mkdir -p /var/www/luis.com/htdocs
RUN echo "Pagina de prueba" > /var/www/luis.com/htdocs/index.html
COPY ./disk/etc/apache2/sites-available/luis.com.conf /etc/apache2/sites-available
WORKDIR /etc/apache2/sites-available
#RUN a2ensite luis.com.conf && service apache2 restart

# Disable modules

RUN a2dismod status && \
    a2dismod negotiation -f &&\
    a2dismod env -f && \
    a2dismod autoindex -f && \
    a2dismod auth_basic -f && \
    service apache2 restart

# Auth and Require

RUN a2enmod auth_digest authz_user authn_file && service apache2 restart
RUN mkdir /var/www/luis.com/htdocs/privado/
RUN echo "Pagina de prueba para directorio privado" > /var/www/luis.com/htdocs/privado/index.html
#RUN htdigest -c /etc/apache2/password.digest recursopriv admin
#RUN chmod 640 /etc/apache2/password.digest
#RUN chown root:www-data /etc/apache2/password.digest
RUN a2ensite luis.com.conf && service apache2 restart

# Enabling ModSecurity
RUN apt-get install libapache2-mod-security2 -y
RUN mv /etc/modsecurity/modsecurity.conf-recommended /etc/modsecurity/modsecurity.conf
RUN service apache2 restart
RUN sed -i \
        -e 's/SecRuleEngine DetectionOnly/SecRuleEngine On/g' \
        -e 's/SecResponseBodyAccess On/SecResponseBodyAccess Off/g' \
        /etc/modsecurity/modsecurity.conf

#CMD ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]

EXPOSE 80/tcp
EXPOSE 443/tcp
