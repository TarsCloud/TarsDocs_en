# Tars Web User System

Tars management platform provides the ability to interface with user system (including single sign on system and authority system). If the user does not have corresponding system, tars can provide a simple user system module for the user to choose. The user system module provides the function of single sign on and registration, as well as the authority control ability to the service level. Users can also choose to use only one of these functions

## tars-web >= v2.4.8

Tar web can directly complete the login and authentication of users connecting the LDAP service. You only need to edit the LDAP parameters, set the LDAP address and open LDAP in the UserCenter => Settings

In this way, if your company use LDAP to complete the authentication, you can make the tars web connect to your LDAP to complete the unified authentication

## tars-web < v2.4.7

For the version of TARS web < v2.4.7, you can implement your own independent user authentication module, but the operation is relatively complicated. It is recommended to use the version >= v2.4.8, and directly connect to LDAP

### User system module installation

If users want to use the default user system module, they need to install the user system management platform module in the web first. Corresponding: Database Name: db\_user\_system 
The installation steps are the same as those of the tars management platform, which will not be described in detail.

1) Default login module: provide basic registration and login page, external interface getUidByTicket to obtain user information and interface validate to verify whether to log in, getUidByTicket interface receives a ticket parameter, returns uid validate interface to receive ticket parameter and uid parameter, and returns result (true or false);

2）Default permission module: there are three fields in the permission module database table, which are flag, role and uid, respectively corresponding to flags (in the tars platform, "application + service" means a flag), roles and users.


The permission module provides six interfaces, [detail](../installation/web.md)

```text
    /auth/addAuth： Add permission interface in batch, the input parameter is [{flag: “”，role: “”，uid: “”}],
    /auth/deleteAuth：Delete the permission interface. The input parameter is flag. Delete all permission information under the flag
    /auth/updateAuth：Update permission，the input parameter is:flag，role，uid，Where uid is the user list, which means updating all user information under a certain flag and role.
    /auth/getAuthListByUid：Get a list of all permissions a user has，the input parameter is: uid
    /auth/getAuth：Judge whether the user has permission the input parameter is: flag，role，uid。
    /auth/getAuthListByFlag：Get the user information with a certain flag permission, the input parameter is:flag
```

Note: the default permission module, in order to ensure the system security, the above six interfaces of the column must be accessed in the way of white list, and cannot be called by others at will. The management page auth.html needs to be used by the system administrator. Related configurations of whitelist and administrator can be configured in /config/authconf.js.

### Tars login module capabilities

Tars is associated with the third-party login system or the default user system login module through the configuration file /config/loginconf.js to provide the ability to allow users to log in. The login profile details are as follows:

```text
 {
    enableLogin: true,                       //Enable login authentication or not
    defaultLoginUid: 'admin',                //If login authentication is not enabled, the default user is admin
    redirectUrlParamName: 'redirect_url',    //The name of the original URL parameter when you jump to the login URL，for example:***/login?service=***，default is: redirect_url
    baseUserCenterUrl: 'http://localhost:3001',   //Login jump URL (to replace localhost in the code)
    baseLoginUrl: 'http://localhost:3001/login.html',                 //Login jump UR(userCenterUrl + loginUrl)
    userCenterUrl: '',                      //User center login jump URL
    loginUrl: '',                           //login url(baseLoginUrl:localhost)
    logoutUrl: '',
    logoutredirectUrlParamName: 'url',
    ticketCookieName: 'ticket',             //The name of the cookie in which the ticket information is stored
    uidCookieName: 'uid',                   //The name of the cookie in which user information is stored
    cookieDomain: '',                       //Domain corresponding to cookie value
    ticketParamName: 'ticket',              //When the third party logs in to the service callback, the URL indicates the domain corresponding to the cookie value of the ticket's parameter name
    getUidByTicket: getUidByTicket,         //Check and get the URL of basic user information from CAS server through ticket, or get the method of basic user information
    getUidByTicketParamName: 'ticket',      //Parameter name of ticket when calling get user information interface
    uidKey: 'data.uid',                     //Results the location of the user name is taken out in JSON. Only when the user name is taken can it be considered successful. It can be multi-level
    validate: validate,                     //URL or method to verify whether key and user name match through token and user name to CAS server
    validateTicketParamName: 'ticket',      //Verify the name of the ticket parameter passed in by the interface
    validateUidParamName: 'uid',            //Verify the user parameter name passed in by the interface
    validateMatch: [
        ['data.result', true]
    ],                                      //
    ignore: ['/static'],                    //
    ignoreIps: [],                          //access white ip list
    apiPrefix: ['/pages/server/api'],       //
    apiNotLoginMes: '#common.noLogin#',     //
}
```

## Tars authority module capability

Tars is associated with the third-party permission system or the default user system permission module through the configuration file /config/authconf.js to provide the ability of permission control. The details of the permission profile are as follows (the interface in and out participation in the user system permission module of point 1 above are consistent):

```text
{

    /**
     * Enable custom permission module
     */
    enableAuth: false,

    
    /**
     * addAuthUrl             add auth
     * input parameter 
     * @param   {Array}    auth         auth list: {"flag": "app-server", "role": "operator", "uid": "username"}
     */
    /**
     * return parameter
     * @param   {Number}    ret_code            200: succ
     * @param   {String}    err_msg             error message
     */
    addAuthUrl: 'http://localhost/api/auth/addAuth',

    /**
     * deleteAuthUrl             Delete permission URL, used to delete the permission when the service is offline
     * input parameter
     * @param   {String}    flag                Permission unit, which is "application.servername" in tars
     */
    /**
     * return parameter
     * @param   {Number}    ret_code            200: succ
     * @param   {String}    err_msg             error message
     */
    deleteAuthUrl: 'http://localhost/api/auth/deleteAuth',

    /**
     * updateAuthUrl             update auth url
     * input parameter
     * @param   {String}    flag                Permission unit, which is "application.servername" in tars
     * @param   {String}    role                role: operator or developer
     * @param   {String}    uid                 user id
     */
    /**
     * return parameter
     * @param   {Number}    ret_code            200: succ
     * @param   {String}    err_msg             error message
     */
    updateAuthUrl: 'http://localhost/api/auth/updateAuth',

    /**
     * getAuthListByUidUrl             get auth list by user id
     * input parameter
     * @param   {String}    uid                 user id
     */
    /**
     * return parameter
     * @param   {Array}     data               server list
     *        @param   {String}    flag                Permission unit, which is "application.servername" in tars
     *        @param   {String}    role                role: operator or developer
     *        @param   {String}    uid                 user id
     * @param   {Number}    ret_code            200: succ
     * @param   {String}    err_msg             error message
     */
    getAuthListByUidUrl: 'http://localhost/api/auth/getAuthListByUid',

    /**
     * getAuthListByFlagUrl             get user list by auth flag
     * input parameter
     * @param   {String}    flag                 Permission unit, which is "application.servername" in tars
     */
    /**
     * return parameter
     * @param   {Array}     data                server list
     *        @param   {String}    flag                Permission unit, which is "application.servername" in tars
     *        @param   {String}    role                role: operator or developer
     *        @param   {String}    uid                 user id
     * @param   {Number}    ret_code            200: succ
     * @param   {String}    err_msg             error message
     */
    getAuthListByFlagUrl: 'http://localhost/api/auth/getAuthListByFlag',

    /**
     * getAuthUrl             Judge whether the user has the operation authority of the corresponding role
     * @param   {String}    flag                Permission unit, which is "application.servername" in tars
     * @param   {String}    role                role: operator or developer
     * @param   {String}    uid                 user id
     */
    /**
     * return parameter
     * @param   {Object}     data                auth list
     *        @param   {Boolean}    result       permission
     * @param   {Number}    ret_code             200: succ 
     * @param   {String}    err_msg             
     */
    getAuthUrl: 'http://localhost/api/auth/getAuth'
}
```

