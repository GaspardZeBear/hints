# Kanboard + nginx + php-cgi

## Inspired

https://gist.github.com/odan/b5f7de8dfbdbf76bef089776c868fea1

SSL : https://medium.com/@chandramuthuraj/installing-nginx-on-windows-a-step-by-step-guide-6750575c63e2

## Versions

- nginx-1.19.7
- php-8.2.30-nts-Win32-vs16-x64
- kanboard-1.2.50
- sqlite-tools-linux-x64-3510200

## Directories

- 
    To start the webserver run:
        cd c:\nginx
        start nginx
    Allow the windows firewall access
    Run the tasklist command-line utility to see nginx processes: tasklist /fi "imagename eq nginx.exe"

nginx runs as a standard console application (not a service), and it can be managed using the following commands:

    nginx -s stop fast shutdown
    nginx -s quit graceful shutdown
    nginx -s reload changing configuration, starting new worker processes with a new configuration, graceful shutdown of old worker processes
    nginx -s reopen re-opening log files
    taskkill /IM nginx.exe /F Close all nginx processes

- c:/php/php-8.2.30
- c:/nginx/nginx-1.19.7/html/kanboard-1.2.50

## Database

### sqlite

By default, kanboard uses sqlite DB : file :  C:/nginx/nginx-1.19.7/html/kanboard-1.2.50/data/db.sqlite

## Usage

### pilot nginx

~~~
    To start the webserver run:
        cd c:/nginx/nginx-1.19.7
        start nginx
    Allow the windows firewall access
    Run the tasklist command-line utility to see nginx processes: tasklist /fi "imagename eq nginx.exe"

    or doubleclick on nginx !!!

nginx runs as a standard console application (not a service), and it can be managed using the following commands:

    nginx -s stop fast shutdown
    nginx -s quit graceful shutdown
    nginx -s reload changing configuration, starting new worker processes with a new configuration, graceful shutdown of old worker processes
    nginx -s reopen re-opening log files
    taskkill /IM nginx.exe /F Close all nginx processes

    or kill from CTRL-ALT-SUPP (2 process)
~~~

### Pilot php-cgi

Port 9123 from nginx.conf 

~~~
cd c:/php/php-8.2.30
php-cgi.exe -b 127.0.0.1:9123
~~~

### Kanboard

Port 1961 from nginx.conf 

Browser : http://localhost:1961/kanboard-1.2.50


### API with basic auth

With user

~~~
ubu@A6:~/kanby$ echo -n 'admin:admin' | base64 -
YWRtaW46YWRtaW4=

ubu@A6:~/kanby$ curl -H 'Authorization: Basic  YWRtaW46YWRtaW4=' -d '{"jsonrpc": "2.0", "method": "getAllProjects", "id": 1}' http://A6.mshome.net:1961/kanboard-1.2.50/jsonrpc.php
{"jsonrpc":"2.0","result":[{"id":1,"name":"Chambord2026Equipes","is_active":1,"token":"","last_modified":1770972657,"is_public":0,"is_private":0,"is_everybody_allowed":0,"default_swimlane":"Default swimlane","show_default_swimlane":1,"description":"","identifier":"","start_date":"","end_date":"","owner_id":1,"priority_default":0,"priority_start":0,"priority_end":3,"email":"","predefined_email_subjects":null,"per_swimlane_task_limits":0,"task_limit":0,"enable_global_tags":1,"url":{"board":"http://localhost/?controller=BoardViewController&action=show&project_id=1","list":"http://localhost/?controller=TaskListController&action=show&project_id=1"}},{"id":2,"name":"Chambord2026Victimes","is_active":1,"token":"","last_modified":1770884079,"is_public":0,"is_private":0,"is_everybody_allowed":0,"default_swimlane":"Default swimlane","show_default_swimlane":1,"description":null,"identifier":"","start_date":"","end_date":"","owner_id":1,"priority_default":0,"priority_start":0,"priority_end":3,"email":null,"predefined_email_subjects":null,"per_swimlane_task_limits":0,"task_limit":0,"enable_global_tags":1,"url":{"board":"http://localhost/?controller=BoardViewController&action=show&project_id=2","list":"http://localhost/?controller=TaskListController&action=show&project_id=2"}}],"id":2}
~~~

With jsonrpc 

Beware of carriage return and wrap

~~~
Token from kanboard>settings>API

ubu@A6:~/kanby$ token=7c39b3978c36ef91f9848ed42a9ec657dbb8c9523971da979917cacec3cc
ubu@A6:~/kanby$ auth=$(echo -n "jsonrpc:$token" | base64 -w 0 -)

ubu@A6:~/kanby$ echo $auth
anNvbnJwYzo3YzM5YjM5NzhjMzZlZjkxZjk4NDhlZDQyYTllYzY1N2RiYjhjOTUyMzk3MWRhOTc5OTE3Y2FjZWMzY2M=

ubu@A6:~/kanby$ curl -H "Authorization: Basic $auth" -d '{"jsonrpc": "2.0", "method": "getAllProjects","id":2}' http://A6.mshome.net:1961/kanboard-1.2.50/jsonrpc.php
{"jsonrpc":"2.0","result":[{"id":1,"name":"Chambord2026Equipes","is_active":1,"token":"","last_modified":1770972657,"is_public":0,"is_private":0,"is_everybody_allowed":0,"default_swimlane":"Default swimlane","show_default_swimlane":1,"description":"","identifier":"","start_date":"","end_date":"","owner_id":1,"priority_default":0,"priority_start":0,"priority_end":3,"email":"","predefined_email_subjects":null,"per_swimlane_task_limits":0,"task_limit":0,"enable_global_tags":1,"url":{"board":"http://localhost/?controller=BoardViewController&action=show&project_id=1","list":"http://localhost/?controller=TaskListController&action=show&project_id=1"}},{"id":2,"name":"Chambord2026Victimes","is_active":1,"token":"","last_modified":1770884079,"is_public":0,"is_private":0,"is_everybody_allowed":0,"default_swimlane":"Default swimlane","show_default_swimlane":1,"description":null,"identifier":"","start_date":"","end_date":"","owner_id":1,"priority_default":0,"priority_start":0,"priority_end":3,"email":null,"predefined_email_subjects":null,"per_swimlane_task_limits":0,"task_limit":0,"enable_global_tags":1,"url":{"board":"http://localhost/?controller=BoardViewController&action=show&project_id=2","list":"http://localhost/?controller=TaskListController&action=show&project_id=2"}}],"id":2}
~~~

## Files 

### c:/nginx/nginx-1.19.7/conf/nginx.conf

~~~

#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    server {
      listen 1961;
      server_name localhost;
      index index.php;
      error_log c:/nginx/logs/localhost.error.log;
      access_log c:/nginx/logs/localhost.access.log;
      root c:/nginx/nginx-1.19.7/html;

      location / {
        #try_files $uri /index.php$is_args$args;
        try_files $uri $uri/ /kanboard-1.2.50/index.php;
       }

      location ~ \.php {
        try_files $uri =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param SCRIPT_NAME $fastcgi_script_name;
        fastcgi_index index.php;
        fastcgi_pass 127.0.0.1:9123;
      }
    }
}
~~~

### c:/nginx/nginx-1.19.7/html/index.php (for test )

~~~
<?php
echo "The time is " . date("H:i:s") . "<br>";
echo "The time is " . date("h:i:s a");
phpinfo();
~~~

### c:/php/php-8.2.30/php.ini

Uncomment gd extension !

~~~
[PHP]
engine = On
short_open_tag = Off
implicit_flush = Off
zend.enable_gc = On
expose_php = Off
max_execution_time = 30
max_input_time = 60
memory_limit = 512M
error_reporting = E_ALL
display_errors = On
display_startup_errors = On
log_errors = On
variables_order = "GPCS"
request_order = "GP"
register_argc_argv = Off
auto_globals_jit = On
post_max_size = 8M
default_mimetype = "text/html"
default_charset = "UTF-8"
include_path = "."
extension_dir = "c:\php\php-8.2.30\ext"
enable_dl = Off
file_uploads = On
upload_max_filesize = 2M
max_file_uploads = 20
allow_url_fopen = On
allow_url_include = Off
default_socket_timeout = 60

extension=curl
extension=fileinfo
extension=gd
extension=gettext
extension=intl
extension=mbstring
extension=mysqli
extension=openssl
;extension=pdo
extension=pdo_mysql
extension=pdo_sqlite
extension=sqlite3

[Session]
session.save_handler = files
session.save_path = "c:\nginx\temp"
session.use_strict_mode = 0
session.use_cookies = 1
session.use_only_cookies = 1
session.name = PHPSESSID
session.auto_start = 0
session.cookie_lifetime = 0
session.cookie_path = /
session.cookie_domain =
session.cookie_httponly =
session.cookie_samesite =
session.serialize_handler = php
session.gc_probability = 1
session.gc_divisor = 1000
session.gc_maxlifetime = 1440
session.cache_limiter = nocache
session.cache_expire = 180
session.use_trans_sid = 0
session.sid_bits_per_character = 5
~~~
