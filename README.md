## 3Scale API Proxy using Heroku

This is a sample app showing how to run a [3Scale](http://3Scale.com) API Proxy on Nginx using the
[OpenResty buildpack](https://github.com/leafo/heroku-openresty).

[Learn about 3scale's API proxy on Nginx](https://support.3scale.net/howtos/api-configuration/nginx-proxy)

Usage
---------

#### Step 1: Get 3Scale and Heroku Accounts ####
Get a free [3Scale](http://3Scale.com) account. (When prompted, I suggest letting 3Scale generate a sample application and account for you.)
Get a free [Heroku.com](http://Heroku.com) account. [Download the Heroku toolbelt](https://toolbelt.heroku.com/)

#### Step 2: Configure 3Scale Api Proxy and download Nginx config files ####
Follow [3Scale's instructions](https://support.3scale.net/howtos/api-configuration/nginx-proxy) on setting up an Nginx proxy. Once you've configured your sandbox proxy, download the Nginx config files. (Skip 3scale's instructions for deploying on AWS since we'll be deploying on Heroku)

#### Step 3: Clone this repo ####

    #Clone this repo, or your own fork of it (use your repo's URL obviously)
    $ git clone https://github.com/Taytay/api-proxy-3scale-heroku.git

    #go into newly cloned folder
    $ cd api-proxy-3scale-heroku


#### Step 4: Rename the generated .conf files ####

When you unzip the config files that 3Scale gave you, they will look something like:

    $ ls ~/proxy_configs
    nginx_1234567890123.conf nginx_1234567890123.lua

Copy them into your newly cloned folder and rename them to `nginx.conf` and `nginx_3scale_access.lua`

    #Copy and rename the generated .conf and .lua files
    $ cp ~/proxy_configs/nginx_1234567890123.conf ./nginx.conf
    $ cp ~/proxy_configs/nginx_1234567890123.lua ./nginx_3scale_access.lua


#### Step 5: Modify nginx.conf ####
Make the following mandatory modifications to the nginx.conf file:

    #1. Add this line to the top of the file
    daemon off;
    #2. replace 'listen 80;' with:
    listen ${{PORT}};
    #3. replace 'access_by_lua_file lua_tmp.lua;' with:
    access_by_lua_file nginx_3scale_access.lua;

See the sample **nginx.sample.conf** file for details, and for notes on other optional changes you can make.

#### Step 5: Create Heroku app ####
Create your heroku app, using the heroku-buildpack-lua buildpack:

    $ heroku apps:create --buildpack http://github.com/leafo/heroku-buildpack-lua.git <heroku-app-name>

Commit your changes to git

    $ git add .
    $ git commit -m "Configuring the proxy with generated files from 3Scale"

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
           http://<heroku-app-name>.herokuapp.com deployed to Heroku


Test your API proxy using an app_id and app_key you get from your 3scale control panel. More info about these credentials [here](https://support.3scale.net/howtos/api-configuration/nginx-proxy)

    $ curl http://<heroku-app-name>.herokuapp.com/v1/word/awesome.json\?app_id\=YOUR_USER_APP_ID\&app_key\=YOUR_USER_APP_KEY

    {"word":"awesome","sentiment":4}%

Troubleshooting
----------------------
If something goes wrong when nginx starts up, just run `heroku logs`
You should see something like:

    2013-05-04T09:30:04.199607+00:00 heroku[web.1]: Starting process with command `start_nginx.sh`
    2013-05-04T09:30:10.112438+00:00 heroku[web.1]: State changed from starting to up


Motivations
-----------
I wanted a free way to host 3Scale's Nginx API proxy. They have a hosted proxy, but it's only for sandbox/testing use. Nginx is very efficient, so you won't need to deploy another Heroku dyno unless you're pushing insane API traffic. That keeps this within the free usage tier on Heroku.

3Scale's AWS instructions are great, but it's even more appealing to me to run Nginx on Heroku because

+  It's free
+  More stuff is managed for me
+  It's on AWS under the hood anyway
+  My Nginx config files are guaranteed to be revisioned in Git


Credits
-------
[3Scale](3Scale.com) did the cool part by [making their API proxy simply an open Nginx config](http://www.3scale.net/api-management/api-proxy-for-api-traffic-management-by-3scale/). Nice!

The [OpenResty buildpack](https://github.com/leafo/heroku-openresty) did the hard Heroku work! Thanks!

I'm Taylor Brown, aka [Taytay](http://taytay.com/)
