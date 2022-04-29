---
title: "Users and groups"
description: "Quick start for users and groups"
lead: "Quick start for users and groups"
date: 2022-04-`16T13:59:39+01:00
lastmod: 2020-11-16T13:59:39+01:00
draft: false
images: []
menu: 1
docs:
parent: "help"
weight: 100
toc: true
---

## User account

### What is a user account

`User account` is a pre-defined table (`user_account`). Authentication is done using a JWT token. Checkout AuthN/Z for more details on how access is granted to resources for users based on permissions and groups.

### Signup

- Signup action API
- POST to user_account API

#### Sign Up Action API

Sign up is an [action](/actions/actions) on user_account table. Sign up takes four inputs:

- Name
- Email
- Password
- PasswordConfirm

When the user invokes the sign up action api, the following things happen internally

- Check if guests can initiate sign in action
- Check if guests can create a new user (create permission)
- Create a new user row
- Check if guests can create a new usergroup (create permission)
- Create a new usergroup row
- Associate the user to the usergroup (refer permission)

This means that every user has his own dedicated user group by default.

{{< alert icon="👉" text="The signup action is just another action. Checkout actions for making your own" />}}

##### cURL Example

```bash
curl 'https://localhost:6336/action/user_account/signup' -H 'Content-Type: application/json;charset=utf-8'
--data-raw '{"attributes":{"email":"email@host.com","password":"password","name":"name","passwordConfirm":"password"}}'
```

#### POST /user_account API

Following the JSONAPI.org structure, we can directly insert a new row in the user_account table

Endpoint: `POST <host>/api/user_account`

`user_account` table has 4 columns

| name      | type     | constraint    |
|-----------|----------|---------------|
| name      | string   | max 80 length |
| email     | string   | email         |
| password  | password | not empty     | 
| confirmed | boolean  |               | 

This means that every user has his own dedicated user group by default.

##### cURL Example

```bash
curl 'https://<host>/api/user_account' -X POST --data-raw '{"data":{"type":"user_account","attributes":{"email":"test@gmail.com","name":"Test name","password":"test123"}},"meta":{}}'
```



### Sign In

Sign In is an [action](actions/actions) on user entity. Sign in takes two inputs:

- Email
- Password

When the user initiates Sign in action, the following things happen:

- Check if guests can peek users table (Peek permission)
- Check if guests can peek the particular user (Peek Permission)
- Match if the provided password bcrypted matches the stored bcrypted password
- If true, issue a JWT token, which is used for future calls

The main outcome of the Sign In action is the jwt token, which is to be used in the ```Authorization``` header of following calls.


##### cRUL example

```bash
curl 'http://localhost:6336/action/user_account/signin' \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'Accept: application/json, text/plain, */*' \
--data-binary '{"attributes":{"email":"<Email>","password":"<Password>"}}'
```

```json
[
  {
    "ResponseType": "client.store.set",
    "Attributes": {
      "key": "token",
      "value": "<AccessToken>"
    }
  },
  {
    "ResponseType": "client.notify",
    "Attributes": {
      "message": "Logged in",
      "title": "Success",
      "type": "success"
    }
  },
  {
    "ResponseType": "client.redirect",
    "Attributes": {
      "delay": 2000,
      "location": "/",
      "window": "self"
    }
  }
]
```

## Guest access
 
You can allow guests to invoke any API by setting the guest permissions on that table. API requests without any Authorization header or invalid JWT token are treated as guests requests.

## User group

### What is a user group

A user group is a collection of users and objects. User group also defines the access level for the users of the group on the objects that belong to the group.

### Create a new user group

