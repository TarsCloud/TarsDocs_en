# Tars Cookie

## Concept description

Tars cookie is information that is propagated on the service invocation chain. The upper business layer can set cookies upstream of the invocation chain and obtain cookies downstream of the invocation chain.

## scenes to be used

### Propagate command priority

The access Svr sets the request command id and request command priority in the cookie and propagate it to the back end. The back end can customize its own overload protection policy based on this information (such as preferentially rejecting low priority requests)

### Propagate msgno and uid

The access Svr sets the message sequence number (msgno) and user id (uid) in the cookie. The backend service can retrieve the cookie and print the msgno and uid in the log header (this step can be automatically processed by encapsulating a log macro)
When locating a problem, you can quickly find the log by uid or msgno (for example, when A fails to call B, and you have seen A's error log, then you can copy the msgno and go to the B server to quickly find the log of the same context)
The upper business layer can also report logs to retrieval platforms such as elk, making it easy to find all logs through msgno

## Relationship with dyeing
1. Cookies are similar to dyeing and can be propagated on the invocation chain
2. Dyeing conceptually, is only used for a small number of requests; and cookie is used for a full number of requests
3. Dyeing will print a centralized dyeing log, while cookies will not
4. Cookies and dyeing can be used at the same time. you can set cookies for full requests, and dye for a small number of requests that require special handling (and if msgno and uid in the cookie are printed in the log, find a complete log of the request in the centralized dyeing log will be very convenient)
5. Dyeing full request can also achieve cookie-like functions, but this is equivalent to losing the dyeing function, and the disk I/O and usage capacity will double at this time
6. The function of setting / reading cookies uses the map parameter, which is convenient for businesses to customize various information and is more friendly to use.

## Usage example
### Setting cookies
```text
#include "servant/Cookie.h"
CookieOp cookieOp;
map<string, string> cookie;
cookie["msgno"] = "12345";
cookie["uid"] = "67890";
cookieOp.setCookie(cookie);
```
### Get cookies
#### Get cookies from thread-specific data
```text
#include "servant/Cookie.h"
map<string, string> & cookie = CookieOp::getCookie();
TLOGDEBUG("cookie:" << TC_Common::tostr(cookie.begin(), cookie.end()) << endl);
TLOGDEBUG("msgno:" << cookie["msgno"] << endl);
```
#### Get cookies from the current object
```text
map<string, string> & cookie = current->getCookie();
TLOGDEBUG("current cookie:" << TC_Common::tostr(cookie.begin(), cookie.end()) << endl);
TLOGDEBUG("msgno:" << cookie["msgno"] << endl);
```


## application
### Access service setting cookie
The access service sets msgno and uid in the cookie before the RPC
```text
#include "servant/Cookie.h"

CookieOp cookieOp;
map<string, string> cookie;
cookie["msgno"] = genMsgNo();
cookie["uid"] = uid;
cookieOp.setCookie(cookie);
UserInfoServantPrx->async_getUserInfo(cb, ...); 
```


### Backend service print log
The backend service uses the following customized macros to print the log. The log header will automatically contain msgno and uid
```text
LOG_ERROR("call DBProxy.getUserInfo failed, ret:" << ret);
```

The LOG_ERROR macro is defined as follows
```text
#define LOG_ERROR(msg) \
    TLOGERROR(__FILENAME__ << ":" << __LINE__ << ":" << __FUNCTION__ << "|" << CookieOp::getCookie()["msgno"] << "|" << CookieOp::getCookie()["uid"] << "|" << msg << endl);
```


### View logs
Looking at the logs of each back-end service, you can see that each log contains msgno and uid, and the logs of the same request contains the same msgno and uid.
#### log of UserInfoSvr
```text
2020-03-26 11:01:22|24209|DEBUG|UserInfoServantImp.cpp:1019:getUserInfo|af4686091585191682152580023|10001|call DBProxy.getUserInfo failed, ret:-1
```

#### log of DBProxySvr 
```text
2020-03-26 11:01:22|12359|DEBUG|DBProxyServantImp.cpp:720:getUserInfo|af4686091585191682152580023|10001|sql is: select * from t_user wehre uid = '10001'
2020-03-26 11:01:22|12359|ERROR|DBProxyServantImp.cpp:723:getUserInfo|af4686091585191682152580023|10001|access db timeout
```