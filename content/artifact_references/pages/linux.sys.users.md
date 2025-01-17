---
title: Linux.Sys.Users
hidden: true
tags: [Client Artifact]
---

Get User specific information like homedir, group etc from /etc/passwd.

```yaml
name: Linux.Sys.Users
description: Get User specific information like homedir, group etc from /etc/passwd.
parameters:
  - name: PasswordFile
    default: /etc/passwd
    description: The location of the password file.
sources:
  - precondition: |
      SELECT OS From info() where OS = 'linux'
    query: |
      SELECT User, Description, Uid, Gid, Homedir, Shell
      FROM parse_records_with_regex(
            file=PasswordFile,
            regex='(?m)^(?P<User>[^:]+):([^:]+):' +
                  '(?P<Uid>[^:]+):(?P<Gid>[^:]+):(?P<Description>[^:]*):' +
                  '(?P<Homedir>[^:]+):(?P<Shell>[^:\\s]+)')

```
