

~~~
sudo apt-get install zlib1g-dev uuid-dev libuv1-dev liblz4-dev libjudy-dev libssl-dev libmnl-dev gcc make git autoconf autoconf-archive autogen automake pkg-config curl python
~~~

~~~
./netdata-installer.sh

  ^
  |.-.   .-.   .-.   .-.   .  netdata                                        
  |   '-'   '-'   '-'   '-'   real-time performance monitoring, done right!  
  +----+-----+-----+-----+-----+-----+-----+-----+-----+-----+-----+-----+--->


  You are about to build and install netdata to your system.

  It will be installed at these locations:

   - the daemon     at /usr/sbin/netdata
   - config files   in /etc/netdata
   - web files      in /usr/share/netdata
   - plugins        in /usr/libexec/netdata
   - cache files    in /var/cache/netdata
   - db files       in /var/lib/netdata
   - log files      in /var/log/netdata
   - pid file       at /var/run/netdata.pid
   - logrotate file at /etc/logrotate.d/netdata

  This installer allows you to change the installation path.
  Press Control-C and run the same command with --help for help.


  NOTE:
  Anonymous usage stats will be collected and sent to Google Analytics.
  To opt-out, pass --disable-telemetry option to the installer.

Press ENTER to build and install netdata to your system > 

~~~


Requirements
Make sure your apache has installed mod_proxy and mod_proxy_http.

On debian/ubuntu systems, install them with this:

~~~
sudo apt-get install apache2-bin
~~~
Also make sure they are enabled:

~~~
sudo a2enmod proxy
sudo a2enmod proxy_http
~~~
Ensure your rewrite module is enabled:

~~~
sudo a2enmod rewrite
~~~

Create a new apache site:

~~~
nano /etc/apache2/sites-available/netdata.conf
~~~

with this content:

~~~
<VirtualHost *:80>
    RewriteEngine On
    ProxyRequests Off
    ProxyPreserveHost On

    ServerName www.netdata.com

    <Proxy *>
        Require all granted
    </Proxy>

    ProxyPass "/" "http://localhost:19999/" connectiontimeout=5 timeout=30 keepalive=on
    ProxyPassReverse "/" "http://localhost:19999/"

    ErrorLog ${APACHE_LOG_DIR}/netdata-error.log
    CustomLog ${APACHE_LOG_DIR}/netdata-access.log combined
</VirtualHost>
~~~


~~~
a2ensite netdata
~~~

Enable the VirtualHost:

~~~
systemctl restart netdata
systemctl restart apache2
~~~