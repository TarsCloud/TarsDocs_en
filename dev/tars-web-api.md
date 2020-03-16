# Tars Web API
> * [Auth](#auth)
> * [Tree](#tree)
> * [Server](#server)
>- * [Deploy Server](#deploy-server)
>- * [Modify Server](#modify-server)
>- * [Get Server](#get-server)
>- * [Get Server List](#get-server-list)
>- * [Get Inactive Server List](#get-inactive)
>- * [Get Server Notify Log List](#get-notify-server)
>- * [Get server realtime state](#get-server-status)
>- * [Load Server](#load-server)
>- * [Pre Expend](#pre-expand)
>- * [Expand](#expand)
>- * [Add Adapter](#add-adapter)
>- * [Delete Adapter](#delete-adapter)
>- * [Modify Adapter](#modify-adapter)
>- * [Get Adapter](#get-adapter)
>- * [Get Adapter List](#get-adapter-list)
> * [Server Config](#config)
>- * [Add Config](#add-config)
>- * [Delete Config](#del-config)
>- * [Modify Config](#modify-config)
>- * [Get Config](#get-config)
>- * [Get Config List](#get-config-list)
>- * [Get Node Config List](#get-node-config-list)
>- * [Get Config Modify List](#get-config-modify-history)
>- * [Get Config Modify content](#get-config-modify-list)
>- * [Add Ref](#add-ref)
>- * [Delete Ref](#del-ref)
>- * [Ref List](#ref-list)
>- * [Merge Node Config](#merge-node-config)
>- * [Push Config](#push-config)
> * [Task Manage](#task)
>- * [Add Task](#add-task)
>- * [Get Task and Sub Task Info ](#get-task)
>- * [Get Task List](#get-task-list)
> * [Publish Package](#publish)
>- * [Upload Package](#upload-publish)
>- * [Upload And Publish](#upload-and-publish)
>- * [Get Publish Version list](#get-publish-list)
> * [Template](#template)
>- * [Add Template](#add-template)
>- * [Delete Template](#del-template)
>- * [Modify Template](#modify-template)
>- * [Get Template](#get-template)
>- * [Query Template](#query-template)
> * [Monitor](#monitor)
>- * [Get tarsstat Monitor Data](#get-tarsstat)
>- * [Get tarsproperty monitor data](#get-tarspropery)
> * [Dict](#dict)
>- * [Get Server Type List](#get-server-type)
>- * [Get Template List](#get-template-list)
>- * [Select Server](#choose-server)
> * [Resource](#resource)
>- * [Install Tars Node](#install-tarsnode)
>- * [Uninstall Tars Node](#uninstall-tarsnode)
> * [Other](#other)
>- * [Send Custom Command to Server](#send-command)
>- * [Get Unused Port](#get-port)

## <a id="auth"></a>Auth

Please use web>=2.0.0.

Create a new token in the user center, and use the web API after obtaining the token

The usage of Web API is as follows:

```
http://xx.xx.xx.xx/api/[command]?ticket=[token]

```

Ticket: token generated in Web Platform

## <a id="tree"></a>Tree

/api/tree

### tree

Get Application Tree

#### Parameter

None

#### Return Value

```text
{
    "id": "",                  // Node ID
    "name": "",                // Node Name
    "pid": "",                 // Parent Node ID
    "open": true,              // Expand
    "is_parent": true,         // Is Parent Node
    "children": []             // Children
}
```

## <a id="server"></a>Server

### <a id="deploy-server"></a>Deploy Server

/api/deploy\_server

#### Parameter

```text
{                                  
    "application": "",             // Application
    "server_name": "",             // Server Name
    "node_name": "",               // Node Name
    "server_type": "",             // Server Type
    "template_name": "",           // Template Name
    "enable_set": true,            // Open Set
    "set_name": "",                // Set Name
    "set_area": "",                // Set Region
    "set_group": "",               // Set Group
    "adapters": [{ 
        "obj_name": "",            // OBJ Name
        "bind_ip": "",             // Obj Bind IP
        "port": "",                // Port
		"port_type": "tcp",        // Port Type
		"protocol": "tars",        // Protocol
        "thread_num": 0,           // Thread Num
        "max_connections": 0,      // Max Connections
        "queuecap": 0,             // Max length
        "queuetimeout": 0          // Timeout in Queue
    }]                             
}
```

#### Return Value

```text
{
	"server_conf": {          //Server Info
		"id": 0,                   // Server ID
		"application": "",         // Application
		"server_name": "",         // Server Name
		"node_name": "",           // Node Name
		"server_type": "",         // Server Type
		"enable_set": true,        // Open Set
		"set_name": "",            // Set Name
		"set_area": "",            // Set Region
		"set_group": "",           // Set Group
		"setting_state": "",       // Set State
		"present_state": "",       // Current State
		"bak_flag": true,          // Is Backup Machine
		"template_name": "",       // Template Name
		"profile": "",             // Private Template
		"async_thread_num": 0,     // Async Thread Num
		"base_path": "",           // Default Path
		"exe_path": "",            // EXE Path
		"start_script_path": "",   // Start Script
		"stop_script_path": "",    // Stop Script
		"monitor_script_path": "", // Monitor Script
		"patch_time": "",          // Publish Time
		"patch_version", "",       // Publish Version
		"process_id": "",          // ProcessID
		"posttime": ""             // Update Time
	},
	"tars_node_rst":[   //tarsnode install result
		{
			"ip":"1.1.1.3",  //Machine IP
			"rst":false,//install tarsnode
			"msg":""
		}
	]
}
```

#### About

Only Support POST method，in Header set Content-Type:application/json

### <a id="modify-server"></a>Modify Server

/api/update\_server

#### Parameter

```text
{
	"id": 0,                  // Server ID
	"isBak": true,            // Is Backup Machine
	"template_name": "",      // Template Name
	"server_type": "",        // Server Type
	"enable_set": "",         // Open Set
	"set_name": "",           // Set Name
	"ser_area": "",           // Set Region
	"set_group": "",          // Set Group
	"async_thread_num": 0,    // Async Thread Num
	"base_path": "",          // Default Path
	"exe_path": "",           // EXE Path
	"start_script_path": "",  // Start Script
	"stop_script_path": "",   // Stop Script
	"monitor_script_path": "",// Monitor Script
	"profile": ""             // Private Template
}
```

#### Return Value

```text
{
    "id": 0,                   // Server ID
    "application": "",         // Application
    "server_name": "",         // Server Name
    "node_name": "",           // Node Name
    "server_type": "",         // Server Type
    "enable_set": true,        // Open Set
    "set_name": "",            // Set Name
    "set_area": "",            // Set Region
    "set_group": "",           // Set Group
    "setting_state": "",       // Set State
    "present_state": "",       // Current State
    "bak_flag": true,          // Is Backup Machine
    "template_name": "",       // Template Name
    "profile": "",             // Private Template
    "async_thread_num": 0,     // Async Thread Num
    "base_path": "",           // Default Path
    "exe_path": "",            // EXE Path
    "start_script_path": "",   // Start Script
    "stop_script_path": "",    // Stop Script
    "monitor_script_path": "", // Monitor Script
    "patch_time": "",          // Publish Time
    "patch_version", "",       // Publish Version
    "process_id": "",          // ProcessID
    "posttime": ""             // Update Time
}
```

#### About

Only Support POST method，in Header set Content-Type:application/json

### <a id="get-server"></a>Get Server

/api/server

#### Parameter

```text
id // Server ID
```

#### Return Value

```text
{
    "id": 0,                   // Server ID
    "application": "",         // Application
    "server_name": "",         // Server Name
    "node_name": "",           // Node Name
    "server_type": "",         // Server Type
    "enable_set": true,        // Open Set
    "set_name": "",            // Set Name
    "set_area": "",            // Set Region
    "set_group": "",           // Set Group
    "setting_state": "",       // Set State
    "present_state": "",       // Current State
    "bak_flag": true,          // Is Backup Machine
    "template_name": "",       // Template Name
    "profile": "",             // Private Template
    "async_thread_num": 0,     // Async Thread Num
    "base_path": "",           // Default Path
    "exe_path": "",            // EXE Path
    "start_script_path": "",   // Start Script
    "stop_script_path": "",    // Stop Script
    "monitor_script_path": "", // Monitor Script
    "patch_time": "",          // Publish Time
    "patch_version", "",       // Publish Version
    "process_id": "",          // ProcessID
    "posttime": ""             // Update Time
}
```

### <a id="get-server-list"></a>Get Server List

/api/server\_list

#### Parameter

```text
tree_node_id // tree ID
```

#### Return Value

```text
[{
    "id": 0,                   // Server ID
    "application": "",         // Application
    "server_name": "",         // Server Name
    "node_name": "",           // Node Name
    "server_type": "",         // Server Type
    "enable_set": true,        // Open Set
    "set_name": "",            // Set Name
    "set_area": "",            // Set Region
    "set_group": "",           // Set Group
    "setting_state": "",       // Set State
    "present_state": "",       // Current State
    "bak_flag": true,          // Is Backup Machine
    "template_name": "",       // Template Name
    "profile": "",             // Private Template
    "async_thread_num": 0,     // Async Thread Num
    "base_path": "",           // Default Path
    "exe_path": "",            // EXE Path
    "start_script_path": "",   // Start Script
    "stop_script_path": "",    // Stop Script
    "monitor_script_path": "", // Monitor Script
    "patch_time": "",          // Publish Time
    "patch_version": "",       // Publish Version
    "process_id": "",          // ProcessID
    "posttime": ""             // Update Time
}]
```


### <a id="get-inactive"></a>Get Inactive Server List

/api/inactive\_server\_list

#### Parameter

```text
application // Application
server_name // Server Name
node_name   // Node Name
```

#### Return Value

```text
[{
    "id": 0,                   // Server ID
    "application": "",         // Application
    "server_name": "",         // Server Name
    "node_name": "",           // Node Name
    "server_type": "",         // Server Type
    "enable_set": true,        // Open Set
    "set_name": "",            // Set Name
    "set_area": "",            // Set Region
    "set_group": "",           // Set Group
    "setting_state": "",       // Set State
    "present_state": "",       // Current State
    "bak_flag": true,          // Is Backup Machine
    "template_name": "",       // Template Name
    "profile": "",             // Private Template
    "async_thread_num": 0,     // Async Thread Num
    "base_path": "",           // Default Path
    "exe_path": "",            // EXE Path
    "start_script_path": "",   // Start Script
    "stop_script_path": "",    // Stop Script
    "monitor_script_path": "", // Monitor Script
    "patch_time": "",          // Publish Time
    "patch_version": "",       // Publish Version
    "process_id": "",          // ProcessID
    "posttime": ""             // Update Time
}]
```

### <a id="get-notify-server"></a>Get Server Notify Log List

/api/server\_notify\_list

#### Parameter

```text
tree_node_id // Tree ID
```

#### Return Value

```text
{
    "count": 0,
    "rows":[{
        "notifytime": "",          // Time
        "server_id": "",           // Server ID
        "thread_id": "",           // Thread ID
        "command": "",             // Command
        "result": ""               // 结果
    }]
}
```

#### Is Support Page

yes


### <a id="get-server-status"></a>Get server realtime state

/api/get\_realtime\_state

#### Parameter

```text
id // Server ID
```

#### Return Value

```text
{
    "realtime_state": ""       // realtime state
}
```

### <a id="load-server"></a>Load Server

/api/load\_server

#### Parameter

```text
application  // Application
server_name  // Server Name
node_name    // Node Name
```

#### Return Value

```text
"" // Execute result
```

### <a id="pre-expand"></a>Pre Expend

/api/expand\_server\_preview

#### Parameter

```text
{
    "application": "",        // Application
    "server_name": "",        // Server Name
    "set": "",                // Set
    "node_name": "",          // Node Name
    "expand_nodes": [""],     // Expand Node
    "enable_set": true,       // Open Set
    "set_name": "",           // Set Name
    "set_area": "",           // Set Region    
    "set_group": "",          // Set Group
    "copy_node_config": true  // If copy node config when node_name is not empty
}
```

#### Return Value

```text
[{
    "application": "",        // Application
    "server_name": "",        // Server Name
    "set": "",                // Set
    "obj_name": "",           // OBJ Name
    "node_name": "",          // Node Name
    "bind_ip": "",            // Obj Bind IP
    "port": 0,                // Port
    "template_name": "",      // Template Name
    "status": "",             // 状态
}]
```

#### About

Only Support POST method，in Header set Content-Type:application/json

### <a id="expand"></a>Expand

/api/expand\_server

#### Parameter

```text
{
    "application": "",            // Application
    "server_name": "",            // Server Name
    "set": "",                    // Set
    "node_name": "",              // Node Name
    "copy_node_config": true，    // If copy node config, when node_name is not empty
    "expand_preview_servers": [{
        "node_name": "",          // Node Name
        "set": "",                // Set
        "obj_name": "",           // OBJ Name
        "bind_ip": "",            // Obj Bind IP
        "port": 0                 // Port
    }]
}
```

#### Return Value

```text
{
		"server_conf": {          //Server Info
			[{                                  
				"id": 0,                   // Server ID
				"application": "",         // Application
				"server_name": "",         // Server Name
				"node_name": "",           // Node Name
				"server_type": "",         // Server Type
				"enable_set": true,        // Open Set
				"set_name": "",            // Set Name
				"set_area": "",            // Set Region
				"set_group": "",           // Set Group
				"setting_state": "",       // Set State
				"present_state": "",       // Current State
				"bak_flag": true,          // Is Backup Machine
				"template_name": "",       // Template Name
				"profile": "",             // Private Template
				"async_thread_num": 0,     // Async Thread Num
				"base_path": "",           // Default Path
				"exe_path": "",            // EXE Path
				"start_script_path": "",   // Start Script
				"stop_script_path": "",    // Stop Script
				"monitor_script_path": "", // Monitor Script
				"patch_time": "",          // Publish Time
				"patch_version", "",       // Publish Version
				"process_id": "",          // ProcessID
				"posttime": ""             // Update Time
			}]
		},
		"tars_node_rst":[   //tarsnode install result
			{
				"ip":"1.1.1.3",  //Machine IP
				"rst":false,// install tarsnode result
				"msg":"" 
			}
		]
}
```

#### About

Only Support POST method，in Header set Content-Type:application/json


### <a id="add-adapter"></a>Add Adapter

/api/add\_adapter\_conf

#### Parameter

```text
{
	"application": "",         // Application
	"server_name": "",         // Server Name
	"node_name": "",           // Node Name
	"thread_num": 1,           // Thread Num
	"endpoint": "",            // EndPoint
	"max_connections": 0,      // Max Connect'o'n's
	"allow_ip": "",            // Allow IP
	"servant": "",             // Servant
	"queuecap": 0,             // Queue Length
	"queuetimeout": 0,         // Timeout in Queue
	"protocol": "",            // Protocol
	"handlegroup": ""          // Handle Group(not use)
}
```

#### Return Value

```text
{
	"id": 0,                   // Adapter ID
	"application": "",         // Application
	"server_name": "",         // Server Name
	"node_name": "",           // Node Name
	"adapter_name": "",        // Adapter Name
	"thread_num": 1,           // Thread Num
	"endpoint": "",            // EndPoint
	"max_connections": 0,      // Max Connections
	"allow_ip": "",            // Allow IP
	"servant": "",             // Servant
	"queuecap": 0,             // Queue Length
	"queuetimeout": 0,         // Timeout in Queue
	"posttime": "",            // Update Time
	"protocol": "",            // Protocol
	"handlegroup": ""          // Handle Group(not use)
}
```

#### About

Only Support POST method，in Header set Content-Type:application/json


### <a id="delete-adapter"></a>Delete Adapter

/api/delete\_adapter\_conf

#### Parameter

```text
id // Adapter ID
```

#### Return Value

```text
[0] // Deleted Adapter ID
```

### <a id="modify-adapter"></a>Modify Adapter

/api/update\_adapter\_conf

#### Parameter

```text
{
	"id": 0,                   // Adapter ID
	"thread_num": 1,           // Thread Num
	"endpoint": "",            // EndPoint
	"max_connections": 0,      // Max Connections
	"allow_ip": "",            // Allow IP
	"servant": "",             // Servant
	"queuecap": 0,             // Queue Length
	"queuetimeout": 0,         // Timeout in Queue
	"protocol": "",            // Protocol
	"handlegroup": ""          // handle Group (not use)
}
```

#### Return Value

```text
{
    "id": 0,                   // Adapter ID
    "application": "",         // Application
    "server_name": "",         // Server Name
    "node_name": "",           // Node Name
    "adapter_name": "",        // Adapter Name
    "thread_num": 1,           // Thread Num
    "endpoint": "",            // EndPoint
    "max_connections": 0,      // Max Connections
    "allow_ip": "",            // Allow IP
    "servant": "",             // Servant
    "queuecap": 0,             // Queue Length
    "queuetimeout": 0,         // Timeout in Queue
    "posttime": "",            // Update Time
    "protocol": "",            // Protocol
    "handlegroup": ""          // 处理组
}
```

#### About

Only Support POST method，in Header set Content-Type:application/json


### <a id="get-adapter"></a>Get Adapter

/api/adapter\_conf

#### Parameter

```text
id // Adapter ID
```

#### Return Value

```text
{
    "id": 0,                   // Adapter ID
    "application": "",         // Application
    "server_name": "",         // Server Name
    "node_name": "",           // Node Name
    "adapter_name": "",        // Adapter Name
    "thread_num": 1,           // Thread Num
    "endpoint": "",            // EndPoint
    "max_connections": 0,      // Max Connections
    "allow_ip": "",            // Allow IP
    "servant": "",             // Servant
    "queuecap": 0,             // Queue Length
    "queuetimeout": 0,         // Timeout in Queue
    "posttime": "",            // Update Time
    "protocol": "",            // Protocol
    "handlegroup": ""          // handle group(not use)
}
```

### <a id="get-adapter-list"></a>Get Adapter List

/api/adapter\_conf\_list

#### Parameter

```text
id // Server ID
```

#### Return Value

```text
[{
    "id": 0,                   // Adapter ID
    "application": "",         // Application
    "server_name": "",         // Server Name
    "node_name": "",           // Node Name
    "adapter_name": "",        // Adapter Name
    "thread_num": 1,           // Thread Num
    "endpoint": "",            // EndPoint
    "max_connections": 0,      // Max Connections
    "allow_ip": "",            // Allow IP
    "servant": "",             // Servant
    "queuecap": 0,             // Queue Length
    "queuetimeout": 0,         // Timeout in Queue
    "posttime": "",            // Update Time
    "protocol": "",            // Protocol
    "handlegroup": ""          // 处理组
}]
```

## <a id="config"></a>Server Config

### <a id="add-config"></a>Add Config

/api/add\_config\_file

#### Parameter

```text
{
    "level": 1,         // Level
    "application": "",  // Application
    "server_name": "",  // Server Name
    "node_name": "",    // Node Name
    "set_name": "",     // Set Name
    "set_area": "",     // Set取
    "set_group": "",    // Set Group
    "filename": "",     // File Name
    "config": ""        // Content
}
```

#### Return Value

```text
{
    "id": 0,            // ConfigID
    "server_name": "",  // Server Name
    "node_name": "",    // Node Name
    "set_name": "",     // Set Name
    "set_area": "",     // Set取
    "set_group": "",    // Set Group
    "filename": "",     // File Name
    "config": "",       // Content
    "level": 1,         // Level，1：Application or Set，2：Server，3：节点
    "posttime": "",     // Update Time
}
```

### <a id="del-config"></a>Delete Config

/api/delete\_config\_file

#### Parameter

```text
id // ConfigID
```

#### Return Value

```text
[0] // Delete ConfigID
```

### <a id="modify-config"></a>Modify Config

/api/update\_config\_file

#### Parameter

```text
{
    "id": 0,            // ConfigID
    "config": "",       // Content
    "reason": ""        // About
}
```

#### Return Value

```text
{
    "id": 0,            // ConfigID
    "server_name": "",  // Server Name
    "node_name": "",    // Node Name
    "set_name": "",     // Set Name
    "set_area": "",     // Set Region
    "set_group": "",    // Set Group
    "filename": "",     // File Name
    "config": "",       // Content
    "level": 1,         // Level，1：Application or Set，2：Server，3：Node
    "posttime": "",     // Update Time
}
```

#### About

Only Support POST method，in Header set Content-Type:application/json

### <a id="get-config"></a>Get Config

/api/config\_file

#### Parameter

```text
id // ConfigID
```

#### Return Value

```text
{
    "id": 0,            // ConfigID
    "server_name": "",  // Server Name
    "node_name": "",    // Node Name
    "set_name": "",     // Set Name
    "set_area": "",     // Set Region
    "set_group": "",    // Set Group
    "filename": "",     // File Name
    "config": "",       // Content
    "level": 1,         // Level，1：Application or Set，2：Server，3：Node
    "posttime": "",     // Update Time
}
```

### <a id="get-config-list"></a>Get Config List

/api/config\_file\_list

#### Parameter

```text
level       // Level,1:Application，2：Set Name，3：Set Region，4：Set Group，5：Server
application // Application
server_name // Server Name
set_name    // Set Name
set_area    // Set Region
set_group   // Set Group
```

#### Return Value

```text
[{
    "id": 0,            // ConfigID
    "server_name": "",  // Server Name
    "node_name": "",    // Node Name
    "set_name": "",     // Set Name
    "set_area": "",     // Set取
    "set_group": "",    // Set Group
    "filename": "",     // File Name
    "config": "",       // Content
    "level": 1,         // Level，1：Application Or Set，2：Server，3：Node
    "posttime": "",     // Update Time
}]
```

### <a id="get-node-config-list"></a>Get Node Config List

/api/node\_config\_file\_list

#### Parameter

```text
application // Application
server_name // Server Name
set_name    // Set Name
set_area    // Set Region
set_group   // Set Group
config_id   // ConfigID
```

#### Return Value

```text
[{
    "id": 0,            // ConfigID
    "server_name": "",  // Server Name
    "node_name": "",    // Node Name
    "set_name": "",     // Set Name
    "set_area": "",     // Set Region
    "set_group": "",    // Set Group
    "filename": "",     // File Name
    "config": "",       // Content
    "level": 1,         // Level，1：Application or Set，2：Server，3：Node
    "posttime": "",     // Update Time
}]
```

#### About

Only Support POST method，in Header set Content-Type:application/json

### <a id="get-config-modify-history"></a>Get Config Modify List

/api/config\_file\_history

#### Parameter

```text
id  // ID
```

#### Return Value

```text
{
    "id": "",        // ID
    "config_id": "", // ConfigID
    "reason": "",    // About
    "content": "",   // content
    "posttime": "",  // Update Time
}
```

### <a id="get-config-modify-list"></a>Get Config Modify content

/api/config\_file\_history\_list

#### Parameter

```text
config_id // ConfigID
```

#### Return Value

```text
{
    "count":0,
    "rows":[{
        "id": "",        // ID
        "config_id": "", // ConfigID
        "reason": "",    // About
        "content": "",   // Content
        "posttime": "",  // Update Time
    }]
}
```

#### Is Support Page

yes

### <a id="add-ref"></a>Add ref

/api/add\_config\_ref

#### Parameter

```text
config_id    // ConfigID
reference_id // ref ConfigID
```

#### Return Value

```text
{
    "id": "",           // ref ID
    "config_id": "",    // ConfigID
    "reference_id": ""  // ref ConfigID
}
```

### <a id="del-ref"></a>Delete Ref

/api/delete\_config\_ref

#### Parameter

```text
id // Ref ID
```

#### Return Value

```text
[0] // Deleted Ref ID
```

### <a id="ref-list"></a>Ref list

/api/config\_ref\_list

#### Parameter

```text
config_id // ConfigID
```

#### Return Value

```text
[{
    "id": 0,                // refID
    "config_id": 0,         // ConfigID
    "reference": {
        "id": 0,            // ConfigID
        "server_name": "",  // Server Name
        "node_name": "",    // Node Name
        "set_name": "",     // Set Name
        "set_area": "",     // Set取
        "set_group": "",    // Set Group
        "filename": "",     // File Name
        "config": "",       // Content
        "level": 1,         // Level，1：Application or Set，2：Server，3：Node
        "posttime": "",     // Update Time
    }
}]
```

### <a id="merge-node-config"></a>Merge Node Config

/api/merged\_node\_config

#### Parameter

```text
id // Config ID
```

#### Return Value

```text
"" // Config content
```

### <a id="push-config"></a>Push Config 

/api/push\_config\_file

#### Parameter

```text
ids // config ID, sep by ;
```

#### Return Value

```text
[{
    "application": "",    // Application
    "server_name": "",    // Server Name
    "node_name": "",      // Node Name
    "ret_code": 0,        // Execute Result，0: Succ
    "err_msg": ""         // Error Message
}]
```

## <a id="task"></a> Task Manage

> Include Start, Stop, Publish, Offline

### <a id="add-task"></a>Add Task

/api/add\_task

#### Parameter

```text
{
    "serial": true, // Is Serial
    "items": [{
        "server_id": "",    // Server Name
        "command": "",     // Command
        "parameters": {     // Parameter
        }
    }]
}

Command Include: restart，stop，undeploy_tars，patch_tars
when command!=patch_tars时，parameter is empty
when command=patch_tars时，parameter:
{
    patch_id: "0",    // Version
    update_text: "",  // About
    bak_flag: true    // backup flag, true：backup，false：master
}
```

#### Return Value

```text
""  // Task ID
```

#### About

Only support POST，http Header set: Content-Type:application/json

### <a id="get-task"></a>Get Task and Sub Task Info 

/api/task

#### Parameter

```text
task_no // Task ID
```

#### Return Value

```text
{
    "task_no": "",               // Task ID
    "serial": true,              // Is Serial
    "status": 0,                 // Job State
    "items":[{
        "task_no": "",
        "item_no": "",           // Sub Job ID
        "application": "",       // Application
        "server_name": "",       // Server Name
        "node_name": "",         // Node Name
        "command": "",           // Command
        "parameters": {},        // Parameter
        "start_time": "",        // Start Time
        "end_time": "",          // End Time
        "status": "",            // Sub Job State
        "status_info": "",       // State
        "execute_info": ""       // Execute Info
    }]
}
```

#### 

### <a id="get-task-list"></a>Get Task List

/api/task\_list

#### Parameter

```text
application // Application
server_name // Server Name
command     // Command
from        // Start Time
to          // End Time
```

#### Return Value

```text
[{
    "task_no": "",               // Task ID
    "serial": true,              // Is Serial
    "status": 0,                 // Job State
    "items":[{
        "task_no": "",
        "item_no": "",           // Sub Job ID
        "application": "",       // Application
        "server_name": "",       // Server Name
        "node_name": "",         // Node Name
        "command": "",           // Command
        "parameters": {},        // Parameter
        "start_time": "",        // Start Time
        "end_time": "",          // End Time
        "status": "",            // Sub Job State
        "status_info": "",       // State
        "execute_info": ""       // Execute Info
    }]
}]
```

## ## <a id="publish"></a>Publish Package

### <a id="upload-publish"></a>Upload Package

/api/upload\_patch\_package

#### Parameter

```text
application // Application
module_name // Module Name
comment     // About
suse        // Packge OS name(not use)
task_id     // task ID
md5         // package md5（empty not check）
```

#### Return Value

```text
{
    "id": 0,        // package ID
    "server": "",   // Server，Application+Module Name
    "tgz": "",      // Package name
    "comment": "",  // About
    "posttime": ""  // Update Time
}
```


### <a id="upload-and-publish"></a>Upload And Publish

/api/upload\_and\_publish

#### Parameter

```text
application // app
module_name // name
comment     // comment
suse        // file
```

#### Reture Value

```text
msg
```

#### Demo(use curl)

```
curl http://${TARS_WEB_HOST}/api/upload_and_publish?ticket=${TARS_TOKEN} -Fsuse=@${TARGET}.tgz -Fapplication=${APP} -Fmodule_name=${ServerName} -Fcomment=developer-auto-upload
```

### <a id="get-publish-list"></a>Get Publish Version list

/api/server\_patch\_list

#### Parameter

```text
application // Application
module_name // Module Name
```

#### Return Value

```text
{
    "count":0,
    "rows":[{
        "id": 0,        // PackageID
        "server": "",   // Server，Application+Module Name
        "tgz": "",      // Package name
        "comment": "",  // About
        "posttime": ""  // Update Time
    }]
}
```

#### Support Page

Yes

## <a id="template"></a>Template

### <a id="get-template"></a>Add Template

/api/add\_profile\_template

#### Parameter

```text
{
	"template_name": "",      // Not Empty，Template Name
	"parents_name": "",       // Not Empty，Parent Template
	"profile": ""             // Not Empty，Template Content
}
```

#### Return Value

```text
{
	"id": 0,                  // Template ID
	"template_name": "",      // Template Name
	"parents_name": "",       // Parent Template
	"profile": "",            // Template Content
	"posttime": ""            // Update Time
}
```


### <a id="del-template"></a>Delete Template

/api/delete\_profile\_template

#### Parameter

```text
id  // Template ID
```

#### Return Value

```text
[0] // Delete Template
```

### <a id="modify-template"></a>Modify Template

/api/update\_profile\_template

#### Parameter

```text
{
	"id": "",                 //  Not Empty，Template ID
	"template_name": "",      //  Not Empty，Template Name
	"parents_name": "",       //  Not Empty，Parent Template
	"profile": ""             //  Not Empty，Template Content
}
```

#### Return Value

```text
{
	"id": 0,                  // Template ID
	"template_name": "",      // Template Name
	"parents_name": "",       // Parent Template
	"profile": "",            // Template Content
	"posttime": ""            // Update Time
}
```

### <a id="get-template"></a>Get Template

/api/profile\_template

#### Parameter

```text
template_name // Template Name
```

#### Return Value

```text
{
	"id": 0,                  // Template ID
	"template_name": "",      // Template Name
	"parents_name": "",       // Parent Template
	"profile": "",            // Template Content
	"posttime": ""            // Update Time
}
```


### <a id="query-template"></a>Query Template

/api/query\_profile\_template

#### Parameter

```text
template_name  // Template Name
parents_name   // Parent Template Name
```

#### Return Value

```text
[{
	"id": 0,                  // Template ID
	"template_name": "",      // Template Name
	"parents_name": "",       // Parent Template
	"profile": "",            // Template Content
	"posttime": ""            // Update Time
}]
```

## <a id="monitor"></a>Monitor

### <a id="get-tarsstat"></a>Get tarsstat Monitor Data

/api/tarsstat\_monitor\_data

#### Parameter

```text
thedate         // Show Time
predate         // Compare Time
startshowtime   // Start Time
endshowtime     // End Time
master_name     // Server
slave_name      // Client
interface_name  // Interface
master_ip       // ServerIP
slave_ip        // ClientIP
group_by        // Group
```

#### Return Value

```text
[{
    "show_date": "",          // Date
    "show_time": "",          // Time
    "master_name": "",        // Server
    "slave_name": "",         // Client
    "interface_name": "",     // Interface
    "master_ip": "",          // ServerIP
    "slave_ip": "",           // ClientIP
    "the_total_count": "",    // Current Date count
    "pre_total_count": "",    // Compare Date count
    "total_count_wave": "",   // wave rate
    "the_avg_time": "",       // Curent Date cost
    "pre_avg_time": "",       // Compare date cost
    "the_fail_rate": "",      // Current Date fail rate
    "pre_fail_rate": "",      // Compare Date fail rate
    "the_timeout_rate": "",   // Current Date timeout rate
    "pre_timeout_rate": ""    // Compare Date timeout rate
}]
```

### <a id="get-tarsproperty"></a>Get tarsproperty monitor data

/api/tarsproperty\_monitor\_data

#### Parameter

```text
thedate         // Show Time
predate         // Compare Time
startshowtime   // Start Time
endshowtime     // End Time
master_name     // Server Name
master_ip       // IP
property_name   // property
policy          // policy
```

#### Return Value

```text
[{
    "show_date": "",          // Date
    "show_time": "",          // Time
    "master_name": "",        // Server Name
    "master_ip": "",          // IP
    "property_name": "",      // property
    "policy": "",             // policy
    "the_value": "",          // Current Date Value
    "pre_value": ""           // Compare Date Value
}]
```

## <a id="dick"></a>Dict

### <a id="get-server-type"></a>Get Server Type List

/api/server\_type\_list

#### Parameter

None

#### Return Value

```text
["tars_cpp"]
```

### <a id="get-template-list"></a>Get Template List

/api/template\_name\_list

#### Parameter

None

#### Return Value

```text
["tars.default"]
```

### <a id="choose-server"></a>Select Server

/api/cascade\_select\_server

#### Parameter

```text
level        // Level，1：Application，2：Server Name，3：Set，4：Node
application  // Application，level>1 Not Empty
server_name  // Server Name，level>2 Not Empty
set          // Set，level>3，:Set Name.Set Region.Set Group
```

#### Return Value

```text
[""] server list
```


## <a id="resource"></a>Resource

### <a id="install-tarsnode"></a>Install Tars Node

/server/api/install\_tars\_node

#### Parameter

```text
ips	//tarsnode machine ip
```

#### Return Value

```text
[
	{
        "ip": "",  
        "rst": true, 
		"msg":"" 
    }
]
```

### <a id="uninstall-tarsnode"></a>Uninstall Tars Node

/api/uninstall\_tars\_node

#### Parameter

```text
ips	//tarsnode machine ip
```

#### Return Value

```text
[
	{
        "ip": "",  //机器IP
        "rst": true, //卸载结果
		"msg":"" //卸载结果信息
    }
]
```

## <a id="other"></a>Other

### <a id="send-command"></a>Send Custom Command to Server

/api/send\_command

#### Parameter

```text
server_ids // Server ID
command    // Command
```

#### Return Value

```text
[{
	"application": "", // Application
	"server_name": "", // Server Name
	"node_name": "",   // Node Name
	"ret_code": "",    // Return Value
	"err_msg": ""      // err message
}]
```

### <a id="get-port"></a>Get Unused Port

/api/auto\_port

#### Parameter

```text
node_name // Machine IP，sep by ;
```

#### Return Value

```text
[{
	"node_name": "", //Machine IP
	"port": "", // Port
}]
```

