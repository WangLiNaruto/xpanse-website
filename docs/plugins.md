---
sidebar_position: 7
---

# Plugins

Plugins in Xpanse represent integration with cloud providers.
We have one plugin each for every cloud provider where the managed services can be deployed.
The orchestrator decides which plugin to be invoked based on the cloud provider the end customer chooses to host his
service.

## Plugin Implementation

A plugin is a class that implements
the `OrchestratorPlugin` (https://github.com/eclipse-xpanse/xpanse/blob/main/modules/orchestrator/src/main/java/org/eclipse/xpanse/modules/orchestrator/OrchestratorPlugin.java)
interface.
The plugin implementation class must also be a Spring bean.

With the plugin implementation, the plugin will provide the cloud-specific implementation of handling credentials,
reading monitoring data, handling infrastructure resources data provisioned via deployers, etc.

## Plugin Activation

A plugin for a cloud provider is activated by default. But a plugin might need one or more mandatory configurations
available for it to work. These mandatory configuration properties must be declared when the plugins are implemented. If
any of the required configuration properties are not available, then such plugin is simply not considered for processing
any requests.

### Openstack

Mandatory configuration properties are the following

-   `OS_AUTH_URL` - Keystone URL of the Openstack installation.

Other optional config properties

-   `OS_SERVICE_PROJECT` - Openstack project to be used to get monitoring information. All metrics data is stored in a
    different central project. If this is not provided, then the project where the resource is hosted is used to get the
    metrics data.
-   `OS_PROXY_HOST` and `OS_PROXY_PORT` - Proxy server information to reach the Openstack installation.
-   `OS_SSL_DISABLED` - If set to `true`, the certificate validation on the REST API calls to Openstack installation will
    be disabled.

### HuaweiCloud

No mandatory configuration properties required.

### FlexibleEngine

No mandatory configuration properties required.

### SCS - Sovereign Cloud Stack

No mandatory configuration properties required.
But `OS_AUTH_URL` which is the keystone base URL of the SCS installation must be passed one of the fixed deployment variables as below in all the service registration requests.

```yaml
name: OS_AUTH_URL
description: SCS cloud instance to be used.
kind: fix_env # this is important here.
dataType: string
mandatory: true
sensitiveScope: none
value: 'https://example.com'
```
