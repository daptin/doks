---
title: "Quick Start"
description: "One page summary of how to start a new Daptin project."
lead: "One page summary of how to start a new Daptin project."
date: 2020-11-16T13:59:39+01:00
lastmod: 2020-11-16T13:59:39+01:00
draft: false
images: []
menu:
  docs:
    parent: "prologue"
weight: 110
toc: true
---

## Requirements

Daptin is a static binary which you can download for your specific platform:

- Download from [Releases page](https://github.com/daptin/daptin/releases) (it includes no web dashboard)
- Pull docker image from [Docker hub](https://hub.docker.com/u/daptin) `docker run -p 6336:6336 daptin/daptin`

## Start a new Daptin project

Start server

### Get started

Here is a quick list to jump into the documentation:

- Sign up to create new account, become administrator and create groups
- Create a new table with few columns, create new record, search records, delete using REST API /  using GraphQL
- Listening to create/update/delete events using websockets
- Create documents, create collaborative editor with YJS
- Upload files, upload images, fetch as static assets
- Connect with S3 and use cloud for storage instead of local disk
- Create a new site, expose static content, create a hugo site
- Issue a new certificate using ACME from letsencrypt, generate a self-signed certificate
- Create an email server
- Send emails using SMTP, receive emails using SMTP (ssl)
- Connect with SMTP clients using IMAP(S) (ssl)
- Create a FTP(s) server, authenticate and upload files using a GUI client


```bash
git clone https://github.com/h-enk/daptin-child-theme.git my-daptin-site
```

#### Daptin starter theme

```bash
git clone https://github.com/daptin/daptin.git daptin
```

### Change directories

```bash
cd daptin
```

### Build

```bash
go get ./... && go build -o daptin
```

### Start development server

```bash
./daptin
```

Daptin will start the server accessible by default at `http://localhost:6336`.
