# Introduction

## Clients servers and URLs

Addresses on the Web are expressed with URLs (Uniform Resource Locators) which specify a protocol (e.g. http), a server
name (e.g. www.apache.org), a URL-path (e.h. /docs/current/getting-started.html) and possibly a query string
(e.g. ?arg=value) used to pass additional arguments to the server.

## Hostnames and DNS

In order to connect to a server, the client will first have to resolve the `servername` to an IP address. Thus, in order
to have web server reachable, it is necessary that the serve-name be in DNS. More than one site can be run using virtual
hosts.

## Configuration files and directives

The Apache HTTP server is configured via text files. Files can be on variety of places, depending on how exactly server
was installed. Locations can be found
in [httpd wiki](https://cwiki.apache.org/confluence/display/HTTPD/DistrosDefaultLayout). The default configuration file
is usually `httpd.conf`.

The server is configured by placing `configuration directives` in configuration files.

The question of ***Where should I put that directive?*** depends on where it should be effective. Global ones should be
outside any `<Directory>`, `<Location>`, `<VirtualHost>` or other section.

In addition to main files some directives are added in `.htaccess` files.

## Web site content

Website content can be static or dynamic.

Static content is things like HTML, images, css that reside in filesystem. The `DocumentRoot` directive specifies where
in your filesystem you should place these files. Can be set globally or per virtual host. For example if document root
is `/var/www/html` and request is made for `http//ww.example.com/work/`, the file
`/var/www/html/work/index.html` will be server to the client.

Dynamic content uses server modules such a `mod_php` to render content.

## Log files and Troubleshooting

The location of the error log is defined by [ErrorLog](https://httpd.apache.org/docs/2.4/mod/core.html#errorlog)
directive, which may be set globally or per virtual host. It is possible to associate log ID which can be correlated to
access log entry.

# Configuration

## Apache2 configuration folder structure

Configuration is located at `/etc/apache2` and has following structure:

```bash
	/etc/apache2/
	|-- apache2.conf
	|	`--  ports.conf
	|-- mods-enabled
	|	|-- *.load
	|	`-- *.conf
	|-- conf-enabled
	|	`-- *.conf
	`-- sites-enabled
	 	`-- *.conf
```

File named `apache2.confg` is main configuration file. It puts pieces together by including all remaining configuration
files when starting up the web server.
***It can be copied over to local files `docker cp {container_id}:/etc/apache2/apache2.conf /projects/{project}/docker/apache2.conf`.***

The main configuration file `apache2.conf` does follow things:

1. Loads `ports.conf` - determines listening ports for incoming connections
2. Sets default error log to `ErrorLog ${APACHE_LOG_DIR}/error.log`
3. Sets default Log format
4. Loads configuration files in `mods-enabled/*.load`, `mods-enabled.conf` , `conf-enabled/*` and `sites-enabled/*`

and also includes
following configuration:

```conf
<FilesMatch \.php$>
	SetHandler application/x-httpd-php
</FilesMatch>

DirectoryIndex disabled
DirectoryIndex index.php index.html

<Directory /var/www/>
    # Removes indexes optons. This option lists diretories if handler was not found.
	Options -Indexes
	AllowOverride All
</Directory>
```

Core apache2 directives that are always available are available [here](https://httpd.apache.org/docs/2.4/mod/core.html).

### Use apache helper scripts

- To manage modules `a2enmod` `a2dismod`
- To manage sites `a2ensite` `a2dissite`
- To manage configurations `a2enconf` `a2disconf`

Note that official php:apache image starts apache using `apache2 -D FOREGROUND` (in foreground).

