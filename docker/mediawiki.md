<!-- TITLE: Mediawiki -->
<!-- SUBTITLE: Reference How-to Run MediaWiki on Docker -->

# Install

1) Install Mediawiki with `sudo docker container run -d --name mediawiki -p 8080:80 mediawiki`

2) Install MySQL with `sudo docker container run -d --name mediawiki-mysql -v mediawiki-mysql:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=<root_pwd> mysql`

You can additionally install SMTP server with 
`sudo docker run -d --name mediawiki-smtp namshi/smtp`

3) Create a new network with `sudo docker network create mediawiki`

4) Put all containers into a new network.

```
$ sudo docker network connect mediawiki mediawiki
$ sudo docker network connect mediawiki mediawiki-mysql
$ sudo docker network connect mediawiki mediawiki-smtp
```

# Configure

1) Access your wiki at `http://<host>:8080`

2) Click to set up wiki



3) Select language and click *Continue*

[image:74 size:large]

4) Leave default and click *Continue*

[image:75 size:large]

5) Input your MySQL container name and its root password

[image:76 size:large]

6) Leave default and click *Continue*

[image:77 size:large]

7) Assign your wiki name and create your wiki account

[image:78 size:large]

8) Click *Continue* to start configure

[image:79 size:large]

9) Once done, click *Continue* again to download **LocalSettings.php** file.

[image:80 size:large]

10) Transfer this file into the container

```
sudo docker cp LocalSettings.php mediawiki:/var/www/html
```

11) You wiki is ready to use at `http://<host>:8080/index.php/Main_Page`

[image:82 size:large]

## Email Function 

(**WIP, Not Workable Solution yet**)

```
Mailer returned: Failed to add recipient: pacroy@gmail.com [SMTP: Invalid response code received from server (code: 550, response: relay not permitted)] 
```

To get email function work within Wiki, please proceed the following:

1) Edit LocalSettings.php and add the following lines

```
$wgSMTP = array(
	'host'     => "mediawiki-smtp", // could also be an IP address. Where the SMTP server is located
	'IDHost'   => "chairat.me",     // Generally this will be the domain name of your website (aka mywiki.org)
	'port'     => 25,               // Port to use when connecting to the SMTP server
	'auth'     => false             // Should we use SMTP authentication (true or false)
   );
```

Reference: https://www.mediawiki.org/wiki/Manual:$wgSMTP

2) Copy into container `sudo docker cp LocalSettings.php mediawiki:/var/www/html`

# Useful Links

| Descrioption| Link |
|---|---|
| https://www.mediawiki.org/wiki/Markup_spec | Wiki Markup Spec |