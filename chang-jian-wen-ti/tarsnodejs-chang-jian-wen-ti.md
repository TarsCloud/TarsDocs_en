# TarsNodejs FAQ

### When node.js applications developed egg framework in tars, they may fail in release or restart. Why?

> * When egg doesn't use in single mode, it will open an independent worker process to listen to ports. When applications release or restart in tars, listening process won't be killed. So the port will be used and cannot be listened. The service cannot be started. 
> * Process management of Tars Nodejs, named node-agent, including Cluster sub-process, exception monitoring, process pull-up, monitoring and reporting, etc. Ordinary Nodejs programs naturally have the above features as long as they are released on the Tars platform, and there is no need to do their own process management.

