FROM httpd:

LABEL maintainer="Luis Acosta <luis13cst@gmail.com>" 

# WORKDIR

RUN  \
  apt-get update && apt-get upgrade -y && \
  apt-get install openssl nano -y

# Definir el usuario y grupo que ejecuta el servicio apache

RUN export APACHE_RUN_USER=www-data
RUN export APACHE_RUN_GROUP=www-data

# Desactivar firma de servidor

RUN sed -i \
        -e 's/ServerTokens OS/ServerTokens ProductOnly/g' \
        -e 's/ServerSignature On/ServerSignature Off/g' \
        /usr/local/apache2/conf/extra/httpd-default.conf 

# Habilitar SSL y otros modulos

RUN mkdir /usr/local/apache2/ssl

RUN sed -i \
        -e 's+#LoadModule ssl_module modules/mod_ssl.so+LoadModule ssl_module modules/mod_ssl.so+g' \
        -e 's+#LoadModule rewrite_module modules/mod_rewrite.so+LoadModule rewrite_module modules/mod_rewrite.so+g' \
        -e 's+#LoadModule ssl_module modules/mod_ssl.so+LoadModule ssl_module modules/mod_ssl.so+g' \
        -e 's+#LoadModule auth_digest_module modules/mod_auth_digest.so+LoadModule auth_digest_module modules/mod_auth_digest.so+g' \
        /usr/local/apache2/conf/httpd.conf

RUN apachectl -k restart


# Virtualhost config

RUN mkdir -p /var/www/html
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
