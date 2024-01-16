FROM httpd
ADD httpd.conf /usr/local/apache2/conf
ADD httpd-vhosts.conf /usr/local/apache2/conf/extra
ADD build /usr/local/apache2/build
WORKDIR /usr/local/apache2
EXPOSE 80
