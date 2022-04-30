---
title: "How to Update"
description: "Regularly update the binary to keep your Daptin website stable, usable, and secure."
lead: "Regularly update the binary to keep your Daptin website stable, usable, and secure."
date: 2020-11-12T13:26:54+01:00
lastmod: 2020-11-12T13:26:54+01:00
draft: false
images: []
menu:
  docs:
    parent: "help"
weight: 610
toc: true
---

{{< alert icon="💡" text="Learn more about <a href=\"https://go.dev/doc/modules/version-numbers\">semantic versioning</a> and <a href=\"https://go.dev/doc/modules/managing-dependencies#getting_version\">version pinning</a>." />}}

## Download latest binary

Download from  [`releases page`](https://github.com/daptin/daptin/releases).


Latest version is:

```bash
npm outdated [[<@scope>/]<pkg> ...]
```

## Update packages

The [`npm update`](https://docs.npmjs.com/cli/v7/commands/npm-update) command will update all the packages listed to the latest version (specified by the tag config), respecting semver:

```bash
npm update [<pkg>...]
```
