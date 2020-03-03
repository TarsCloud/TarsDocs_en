# Tars Web API

## Interface call description

Suppose it is deployed in localhost, port 80, and the interface is **tree** and the URL address accessed is:

```text
http://localhost/pages/tree
```

## Application Tree

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

## Server

### server/api/deploy\_server

Deploy Server

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

### server/api/update\_server

修改Server

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

### server/api/server

Get Server

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

### server/api/server\_list

Get Server List

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

### server/api/inactive\_server\_list

Get Server List where Set State equal inactive

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

### server/api/server\_notify\_list

Get Servernotify Log list

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

### server/api/get\_realtime\_state

Get server realtime state

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

### server/api/load\_server

Load Server

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

### server/api/expand\_server\_preview

Pre Expend

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

### server/api/expand\_server

Expand

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

### server/api/add\_adapter\_conf

Add Adapter

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

### server/api/delete\_adapter\_conf

Delete Adapter

#### Parameter

```text
id // Adapter ID
```

#### Return Value

```text
[0] // Deleted Adapter ID
```

### server/api/update\_adapter\_conf

Modify Adapter

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

### server/api/adapter\_conf

Get Adapter

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

### server/api/adapter\_conf\_list

Get Adapter List

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

## Server Config

### server/api/add\_config\_file

Add Config

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

### server/api/delete\_config\_file

Delete Config

#### Parameter

```text
id // ConfigID
```

#### Return Value

```text
[0] // Delete ConfigID
```

### server/api/update\_config\_file

Modify Config

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

### server/api/config\_file

Get Config

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

### server/api/config\_file\_list

Get Config List

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

### server/api/node\_config\_file\_list

Get Node Config List

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

### server/api/config\_file\_history

gGt Config Modify List

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

### server/api/config\_file\_history\_list

Get Config Modify content

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

### server/api/add\_config\_ref

add ref

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

### server/api/delete\_config\_ref

Delete Ref

#### Parameter

```text
id // Ref ID
```

#### Return Value

```text
[0] // Deleted Ref ID
```

### server/api/config\_ref\_list

ref list

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

### server/api/merged\_node\_config

merge node config

#### Parameter

```text
id // Config ID
```

#### Return Value

```text
"" // Config content
```

### server/api/push\_config\_file

push node config 

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

## Task Manage

> Include Start, Stop, Publish, Offline

### server/api/add\_task

Add Job

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

### server/api/task

Get Task and Sub Task Info 

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

### server/api/task\_list

Get Task List

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

## Publish Package

### server/api/upload\_patch\_package

Upload Package

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

### server/api/server\_patch\_list

Get Publish Version list

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

## Template

### server/api/add\_profile\_template

Add Template

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

### server/api/delete\_profile\_template

Modify Template

#### Parameter

```text
id  // Template ID
```

#### Return Value

```text
[0] // Delete Template
```

### server/api/update\_profile\_template

Modify Template

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

### server/api/profile\_template

Get Template

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

### server/api/query\_profile\_template

Query Template

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

## Monitor

### server/api/tarsstat\_monitor\_data

Get tarsstat Monitor Data

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

### server/api/tarsproperty\_monitor\_data

Get tarsproperty monitor data

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

## Dict

### server/api/server\_type\_list

Get Server Type List

#### Parameter

None

#### Return Value

```text
["tars_cpp"]
```

### server/api/template\_name\_list

Get Template List

#### Parameter

None

#### Return Value

```text
["tars.default"]
```

### server/api/cascade\_select\_server

Select Server

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

## Other

### server/api/send\_command

Send Custom Command to Server

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

### server/api/auto\_port

Get Unused Port

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

