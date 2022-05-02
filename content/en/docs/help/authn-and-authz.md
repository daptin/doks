---
title: "Authentication"
description: "Quick start for authentication and authorization for users and groups"
lead: "Quick start for authentication for users and groups"
date: 2022-04-`16T13:59:39+01:00
lastmod: 2020-11-16T13:59:39+01:00
draft: false
images: []
menu:
docs:
parent: "help"
weight: 130
toc: true
---


Daptin has a built-in authorization framework based on users groups and permissions. Users are identified by their
authorization token or other means of identification. Each request is identified as coming from a registered user or a
guest.

## Examples

- Add a user to usergroup
- Add a record to usergroup
- Allow user to edit/delete record
- Disallow user to edit/delete record

## Permission model

Every read/write to the system passes through two level of permission check.

- Type level: apply permission on all types of entities at the same time
- Data level: object level permission

The `world` table contains two columns:

- `Permission`: defines the entity level permission
- `Default permission`: defines the default permission for a new object of this entity type

The default permission for an object is picked from the default permission setting, and can be changed after the object
creation (if the permission allows).

## Permission Bits

| Permission   | Bit     | Description                                                                             |
|--------------|---------|-----------------------------------------------------------------------------------------|
| None         | 0       | No Access                                                                               |
| GuestPeek    | 1 << 0  | (*Guest*) Peek data internally, but not return in response (eg execute action)                    |
| GuestRead    | 1 << 1  | (*Guest*) Read data (return in api response)                                                      |
| GuestCreate  | 1 << 2  | (*Guest*) Create a new record                                                                     |
| GuestUpdate  | 1 << 3  | (*Guest*) Update an existing record                                                               |
| GuestDelete  | 1 << 4  | (*Guest*) Delete a record                                                                         |
| GuestExecute | 1 << 5  | (*Guest*) Execute an action on the object                                                         |
| GuestRefer   | 1 << 6  | (*Guest*) Add this record to a usergroup                                                          |
| UserPeek     | 1 << 7  | (*Owner*) No Access                                                                   |
| UserRead     | 1 << 8  | (*Owner*) Peek data internally, but not return in response (eg execute action)        |
| UserCreate   | 1 << 9  | (*Owner*) Read data (return in api response)                                          |
| UserUpdate   | 1 << 10 | (*Owner*) Create a new record                                                         |
| UserDelete   | 1 << 11 | (*Owner*) Update an existing record                                                   |
| UserExecute  | 1 << 12 | (*Owner*) Delete a record                                                             |
| UserRefer    | 1 << 13 | (*Owner*) Execute an action on the object                                             |
| GroupPeek    | 1 << 14 | (*Group*) No Access                                                            |
| GroupRead    | 1 << 15 | (*Group*) Peek data internally, but not return in response (eg execute action) |
| GroupCreate  | 1 << 16 | (*Group*) Read data (return in api response)                                   |
| GroupUpdate  | 1 << 17 | (*Group*) Create a new record                                                  |
| GroupDelete  | 1 << 18 | (*Group*) Update an existing record                                            |
| GroupExecute | 1 << 19 | (*Group*) Delete a record                                                      |
| GroupRefer   | 1 << 20 | (*Group*) Add record as a relation                                                      |

`OR` the desired permission bits to get the final permission column value. Example

```DEFAULT_PERMISSION = GuestPeek|GuestExecute|UserCRUD|UserExecute|GroupCRUD|GroupExecute```

## Authorization

Authorization is the part where daptin decides if the caller has enough permission to execute the call. Access check
happens at two levels:

- Entity level check
- Object level check

Both the checks have a "before" and "after" part.

### Object level permission

Once the call clears the entity level check, an object level permission check is applied. This happens in cases where
the action is going to affect/read an existing row. The permission is stored in the same way. Each table has a
permission column which stores the permission in ```OOOGGGXXX``` format.

### Preference of permission

For a user in multiple groups the permission is checked in order of:

- Check if the user is owner, if yes, check if permission allows the current action, if yes do action
- Check if the user belongs to a group to which this object also belongs, if yes, check if permisison allows the current
  action, if yes do action
- User is guest, check if guest permission allows this actions, if yes do action, if no, unauthorized

Things to note here:

- There is no negative permission (this may be introduced in the future)
    - eg, you cannot say owner is 'not allowed' to read but read by guest is allowed.
- Permission check is done in a hierarchy type order

### Access check flow

Every "interaction" in daptin goes through two levels of access. Each level has a ```before``` and ```after``` check.

- Entity level access: does the user invoking the interaction has the appropriate permission to invoke this (So for sign
  up, the user table need to be writable by guests, for sign in the user table needs to be peakable by guests)
- Instance level access: this is the second level, even if a User Account has access to "user" entity, not all "user"
  rows would be accessible by them

So the actual checks happen in following order:

- "Before check" for entity
- "Before check" for instance
- "After check" for instance
- "After check" for entity

Each of these checks can filter out objects where the user does not have enough permission.

### Entity level permission

Entity level permission are set in the world table and can be updated from dashboard. This can be done by updating the `permission` column for the entity.

For these changes to take effect a restart is necessary.

### Instance level permission

Like we saw in the [entity documentation](/setting-up/entities), every table has a ```permission``` column. No restart
is necessary for changes in these permission.

You can choose to disable new user registration by changing the permissions of the `/user_account/signup` action.

## Social login

Oauth connection can be used to allow guests to identify themselves based on the email provided by the oauth id
provider.

### Social login/OAuth login

Allow users to login using their existing social accounts like twitter/google/github.

Daptin can work with any OAuth aware identity provider to allow new users to be registered (if you have disabled
/api/user_account/signup).

Create a [OAuth Connection](/extend/oauth_connection) and mark "Allow login" to enable APIs for OAuth flow.

Examples

{{< alert icon="👉" text="The secrets are stored after encryption so the value you see in above screenshots are encrypted values." />}}


## Configuring default user group

You can configure which User groups should newly registered users be added to after their signup.

This can be configured in the table properties by updating the entity configuration using the API

Re-sync action is required to be called for default group settings to take effect

## Authorization bearer token

The authentication token is a JWT token issued by daptin on sign in action. Users can create new actions to allow other
means of generating JWT token. It is as simple as adding another outcome to an action.

### Server side

Daptin uses OAuth 2 based authentication strategy. HTTP calls are checked for ```Authorization``` header, and if
present, validated as a JWT token. The JWT token should have been issued by daptin earlier and should not have expired.
To see how to generate JWT token, checkout the [sing-in action](/actions/signin).

The JWT token contains the issuer information (daptin) plus basic user profile (email). The JWT token has a one hour (
configurable) expiry from the time of issue.

If the token is absent or invalid, the user is considered as a guest. Guests also have certain permissions. Checkout
the [Authorization docs](/auth/authorization) for details.

### Client side

On the client side, for dashboard, the token is stored in local storage. The local storage is cleared on logout or if
the server responds with a 401 Unauthorized status.


