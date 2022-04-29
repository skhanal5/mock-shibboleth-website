<h1 align="center">
  Mock Shibboleth Webpage
</h1>

<p align="center">
  A mock webpage on Apache 2.4 webservers that utilizes Shibboleth Identity Provider and Service Provider to understand the flow of user authentication & authorization.
</p>


## Identity Provider (IDP)

This setup makes use of Internet2's dockerized instance of the Shibboleth IDP. You can reference the repository with the container [here](https://github.internet2.edu/docker/shib-idp). You can also use the Shibboleth distribution of the IDP which you can download from [here](https://shibboleth.net/downloads/identity-provider/).
I have not included the corresponding adjustments made to the IDP in this repository's filesystem as very few changes were made outside of it's basic configuration which you can find on the Shibboleth documentation.

## Service Provider (SP)

On the otherhand, the corresponding SP used is from the Shibboleth distribution which you can find [here](https://shibboleth.net/downloads/service-provider/). Likewise, those files have not been included (yet) as the configuration can be found following the official documentation for the most part.

## Apache

My configuration of the webpage makes use of Apache's webserver via [xampp](https://www.apachefriends.org/download.html). I have configured a basic virtual host for the webpage & service provider. In this setup, I have configured Apache to make use of SSL (via modssl) and set it to listen to port **8443**. *Note the IDP setup will make use of port **443** and this setup **will not work with the IDP and SP running on the same ports***.

General virtual host setup in httpd-ssl.conf:
```
Listen 8443
<VirtualHost _default_:8443>
  DocumentRoot "C:/xampp/htdocs/your-project-directory"
  ServerName localhost 
  ServerAdmin admin@example.com
  ErrorLog "C:/xampp/apache/logs/error.log"
  TransferLog "C:/xampp/apache/logs/access.log"
  SSLEngine on
</VirtualHost>
```

Now that we have our virtualhost configured, we need to make it work for Shibboleth's SP. Load the appropriate mod_shib.so file corresponding to your Apache version and include the following parameters for the directories in your web directory.

Example Shibboleth setup:

```
<Location /mock-website/secure>
  AuthType shibboleth
  ShibRequestSetting requireSession 1
  Require shib-session
</Location>
```

To understand the parameters used head over to the [Shibboleth Service Provider documentation](https://shibboleth.atlassian.net/wiki/spaces/SHIB2/pages/2577072327/NativeSPApacheConfig]) for more information.
