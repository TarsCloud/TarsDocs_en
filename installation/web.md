
# Directory
> * [Intro](#chapter-1)
> * [Module description](#chapter-2)
> * [Permission specification](#chapter-3)
> * [Module call](#chapter-4)
> * [Inspection problem](#chapter-5)
> * [Token Manage](#chapter-6)
> * [Password Forget](#chapter-7)
> * [Web development](#chapter-8)

# 1 <a id="chapter-1"></a>Intro

The main functions of tars-web are as follows:
- Deployment, release, expansion and configuration of services
- Service monitoring, statistics viewing
- Online expansion of nodes
- View service log (click service name)
- Privilege management

Tars-web installation usually follows the one click installation script or is automatically installed in the docker container. For the source code installation, please refer to [tars source code installation](source.md)

## 2 <a id="chapter-2"></a>Module description


###  tars web < v2.4.7

Tar web consists of two modules (services implemented by nodejs, implemented by koa framework):
- tars-node-web: Core function interface, bind by default : 0.0.0.0:3000 
- tars-user-system: User permission system, bound by default机: 0.0.0.0:3001

After the system is installed, it is managed by PM2. You can view:

```
pm2 list
```

Common commands:
- start module:
```
pm2 start tars-node-web
pm2 start tars-user-system
```

- stop module
```
pm2 stop tars-node-web
pm2 stop tars-user-system
```

- restart module
```
pm2 start tars-node-web
pm2 start tars-user-system
```


###  tars web >= v2.4.7

Tar web services implemented by nodejs, implemented by koa framework, bind by default : 0.0.0.0:3000 

After the system is installed, it is managed by PM2. You can view:

```
pm2 list
```

Common commands:
- start module:
```
pm2 start tars-node-web
```

- stop module
```
pm2 stop tars-node-web
```

- restart module
```
pm2 start tars-node-web
```

### Other

Note that if you find that there is no PM2 command, the node environment traversal does not take effect. You can execute:
```
source /etc/profile
```

The environment variable of node is automatically installed when tars is installed. Note that the user is root.

## 3 <a id="chapter-3"></a>Permission specification

There are three types of user permissions:
- admin: admin user
- operator: operator user
- developer: Developer, unable to publish, modify configuration, restart and other write operations

The granularity of permissions can be applied to application or service level

For example, if you want to give the operation and maintenance user test, the app is test, and the service is helloserver permission:
- In the user management interface, create user test with operator
- In the permission management interface, add a new permission. Note that it is marked as Test.HelloServer

If you want to users(test) own all  service permissions of app(Test) , mark it as: Test

In the web management platform, you can click specific services -> Click permission management - > enter the user name in the input box of operation and maintenance, development, and multiple users are separated by ;


## 4 <a id="chapter-4"></a>Module call

###  tars web < v2.4.7

Tars-node-web & Tars-user-system is deployed on the same machine by default, and they are all bound with 0.0.0.0 (that is, 127.0.0.1)

Tars-node-web accesses tars-user-system through localhost (127.0.0.1). If 127.0.0.1 is not bound, it has no permission. At this time, you need to modify the configuration of tars-user-system module (demo/config/loginConf.js), modify ignoreips, and open the white list.

Normally, you can access it through the machine's Internet IP, for example: http://xxx.xxx.xxx.xxx:3000 web management platform.

If nginx proxy is hung in front of Web & demo, and the reverse proxy is accessed to different ports (a 3000, a 3001), different domain names need to be set to access. The login state of Web & demo is passed through cookies, so it needs to be deployed under the same root domain name.

For example, the domain name of the web's nginx entry is http://user.tars.com, and the domain name of demo's nginx is http://auth.tars.com (note that the root domain name is tars.com), so you need to set the environment variable on the web server:

```
export USER_CENTER_HOST=http://auth.tars.com
export COOKIE_DOMAIN=.tars.com
```

**Pay attention: COOKIE_DOMAIN has .**

###  tars web >= v2.4.7

Tars-node-web are all bound with 0.0.0.0 (that is, 127.0.0.1)

Normally, you can access it through the machine's Internet IP, for example: http://xxx.xxx.xxx.xxx:3000 web management platform.

## 5 <a id="chapter-5"></a>Inspection problem

###  tars web < v2.4.7

If the tars web runs abnormally, for example, the page cannot be opened, you can check it in the following ways:

- stop web
```
pm2 stop tars-node-web
```

- Enter the directory and start the service manually
```
cd /usr/local/app/web
npm run dev
```

NPM run dev will output the error on the screen. If it is modified according to the error judgment, Ctrl + C will exit NPM run dev.

After the bug is solved, restart the service with PM2:
```
pm2 start tars-node-web
pm2 start tars-user-system
```

Two important web logs:
- The logs in the running process of ARS node web module will be output in /usr/local/app/web/log/ directory for easy location
- The logs in the running process of the tar user system module will be output in the directory /usr/local/app/web/demo/log, which is convenient to locate the problem.

###  tars web >= v2.4.7


If the tars web runs abnormally, for example, the page cannot be opened, you can check it in the following ways:

- stop web
```
pm2 stop tars-node-web
```

- Enter the directory and start the service manually
```
cd /usr/local/app/web
npm run dev
```

NPM run dev will output the error on the screen. If it is modified according to the error judgment, Ctrl + C will exit NPM run dev.

After the bug is solved, restart the service with PM2:
```
pm2 start tars-node-web
```

The important web logs:
- The logs in the running process of ARS node web module will be output in /usr/local/app/web/log/ directory for easy location


## 6 <a id="chapter-6"></a>Token Manage

The user module of Web provides the function of token management, that is, you can create token, and call and obtain relevant data through web API, [See](../dev/tars-web-api.md)

## 7 <a id="chapter-7"></a>Password Forget

If the password of admin is forgotten, Now, it can only be solved by resetting the fields of related tables in the database at present:

- login mysql
- view db: db_user_system
- modify table: t_user_info
- Reset admin user record, reset password to empty

When you log in to the web, you will be prompted to set the admin password.

## 8 <a id="chapter-8"></a>Web development

If you are a nodejs developer, you can also participate in the development of the web in the following ways:

tars web developed by nodejs + koa.

The code architecture of the two modules is the same. We will explain it with tar node web:
- app: backend source
- bin: entry
- client: The front-end page code, which is organized by webpack, before publish must be run: npm run build
- config: config files
- locale: Character set currently supported (Chinese / English)
- app.js: koa entry
