## 3Scale API Proxy using Heroku

This is a sample app showing how to run a [3Scale](3Scale.com) API Proxy on Nginx using the
[OpenResty buildpack](https://github.com/leafo/heroku-openresty).

[Learn about 3scale's API proxy on Nginx](https://support.3scale.net/howtos/api-configuration/nginx-proxy)

Usage
---------

First:
Get a free [3Scale](3Scale.com) account and a free [Heroku.com](Heroku.com) account if you don't already have them.

Follow [3Scale's instructions](https://support.3scale.net/howtos/api-configuration/nginx-proxy) on setting up an Nginx proxy. Once you've configured your sandbox proxy, you can download two Nginx config files as a zip file.
You'll skip 3scale's instructions for deploying on AWS. We'll be deploying on Heroku


Example usage
----------------------

    #Clone this repo, or your own fork of it (use your repo's URL obviously)
    $ git clone https://github.com/Taytay/api-proxy-3scale-heroku.git

    #go into newly cloned folder
    $ cd api-proxy-3scale-heroku

Modify the nginx.conf file that 3Scale gave you to look like the nginx.conf file here.
(Search for "HEROKU CHANGE" in my nginx.conf file to see the changes I made

Paste the contents of the generated .lua file into nginx_3scale_access.lua file

Create your heroku app, using the heroku-openresty buildpack:

    $ heroku apps:create --buildpack http://github.com/leafo/heroku-buildpack-lua.git heroku-app-name

Commit your changes to git

    $ git add .
    $ git commit -m "Configuring the proxy with generated files from 3Scale"

Create the 3SCALE_PROVIDER_KEY config variable using your secret 3Scale
provider key. It's in the .conf file that 3Scale generates for you

    $ heroku config:set 3SCALE_PROVIDER_KEY=1239832745abcde

Deploy the app to heroku

    $ git push heroku master
    ...
    -----> Fetching custom git buildpack... done
    -----> Lua app detected
    ...
    -----> Discovering process types
           Procfile declares types -> web

    -----> Compiled slug size: 5.0MB
    -----> Launching... done, v14
           http://heroku-app-name.herokuapp.com deployed to Heroku


Test your API proxy using an app_id and app_key you get from your 3scale control panel. More info about these credentials [here](https://support.3scale.net/howtos/api-configuration/nginx-proxy)

    $ curl http://heroku-app-name.herokuapp.com:80/v1/word/awesome.json\?app_id\=12345678\&app_key\=12355abcdef12355abcdef12355abcdef



Motivations
-----------
I wanted a free way to host 3Scale's Nginx API proxy. They have a hosted proxy,
but it's only for sandbox/testing use. Nginx is very efficient, so you won't need
to deploy another Heroku dyno unless you're pushing insane API traffic.
That keeps this within the free usage tier on Heroku.

Credits
-------
[3Scale](3Scale.com) did the cool part by making their API proxy an Nginx config rather than a proprietary binary. Nice!

The [OpenResty buildpack](https://github.com/leafo/heroku-openresty) did the hard Heroku work! Thanks!

I'm Taylor Brown, aka [Taytay](http://taytay.com/)
