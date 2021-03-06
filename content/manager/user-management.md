---
layout: bt_wiki
title: Managing Users
category: Manager
draft: false
weight: 1400
---


In Cloudify, you can connect to your LDAP or AD system to integrate users and user groups, or you can add users and user groups individually. You must select to either use the LDAP system or to add users and groups individually. You cannot do both. User management is performed using the CLI or user interface.

{{% gsTip title="User Management Credentials" %}}
You must have Cloudify Manager administrator permissions to perform user-management related actions.
{{% /gsTip %}}

## Integrating with an LDAP System
If you integrate LDAP or your AD into Cloudify Manager, you use the users and user groups defined there for access to the Manager. 

To connect Cloudify Manager with LDAP/AD, you must know the the URL of the service and have sufficient credentials to perform searches and so on.

You configure Cloudify with the LDAP configuration during the installation process, in the `ldap` section of the config.yaml file. You can also use the API to configure an LDAP connection after Cloudify Manager is installed, using the `cfy ldap set` command, as long as the Manager is clean, meaning that no tenants, groups, users or resources exist in it.

### How Cloudify Manager Works with the LDAP/AD Service

When a user logs in to Cloudify Manager, their credentials are passed to the LDAP/AD service for authentication. By default, all users in the LDAP/AD service are authenticated to Cloudify Manager, however only users who have specific permissions for a tenant can access it.

When a user logs into Cloudify Manager, the service authenticates the user and returns a list of any groups to which the user belongs.

In Cloudify Manager, if you have added a group to a tenant, using the process that complies with the requirements for defining a group specified in LDAP/AD, all users in that LDAP group can access the tenant. The For more information about specifying LDAP/AD-compliant user-group names, see Adding Users on the Tenant Management page.

LDAP passwords are not saved in Cloudify Manager.

The following graphic indicates how Cloudify Manager interacts with an LDAP/AD service.

![User/LDAP relationship]({{< img "manager/multi-tenancy-ldap-relationship.png" >}})

### Roles management with Ldap

Every time a group or a user joins a tenant they have to get a role. A user can get a role in a few ways:

- directly, by assigning the user a system level role
- directly, by assigning the user a tenant role
- indirectly, by adding the user to a user-group which has already a tenant role assigned for a given tenant

When using LDAP, we don’t have direct users management, therefore we’ll have user roles only by adding users to user-groups.

When a user-group is added to a tenant, a specific tenant role must be assigned to it. By adding a user to a specific user-group, that user will inherit that user-group tenant-association along with its tenant-role.

User can be added to multiple user-groups, which may assigned him to the same tenant with multiple roles. This scenario is valid, since Cloudify allows multiple roles in the same tenant. In the permissions calculation process, all the user roles in the specific tenant will be taken into account.


## Adding Users Manually
If you choose not to integrate Cloudify Manager with LDAP/AD, you must add each user individually and set a password for them. You can also create groups and add users to them. The users and user groups can be assigned to one or more tenants.

For more information, see the [users]({{< relref "cli/users.md" >}}) and [user-groups]({{< relref "cli/usergroups.md" >}}) commands in the CLI documentation.


#### Tenant-Related Commands

You can add and remove users and user groups to/from a specific tenant. To run these user-related tenant-specific commands, use `cfy tenant`. For more information, see the [tenants]({{< relref "cli/tenants.md" >}}) command in the CLI documentation.

- `add-user` enables you to add an individual user to a tenant
- `add-user-group` enables you to add a user groups to a tenant
- `create` enables you to create a tenant
- `delete` enables you to delete a tenant
- `get` enables you to view information about a tenant, including its users
- `list` displays a list of all tenants in this instance of Cloudify Manager. By default, when you generate the list of tenants, only the number of linked user-groups and users is displayed. You can retrieve full details with the use of a `--get data` flag.
- `remove-user` enables you to remove a specific user from a tenant
- `remove-user-group` enables you to remove a user group from a tenant

#### How to assign a role to user

When a user is added to a tenant, a Role must be assigned to it by passing a valid value in the `-r/--role` option.

- `cfy tenant add-user -r <role name> ...` adding a user to a tenant, and give him a role.
- `cfy tenant add-user-group -r <role name> ...` adding a user-group to a tenant, and give it a role.
- `cfy users set-role <role-name>` setting the user system role
