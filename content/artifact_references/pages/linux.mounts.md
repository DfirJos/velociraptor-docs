---
title: Linux.Mounts
hidden: true
tags: [Client Artifact]
---

List mounted filesystems by reading /proc/mounts

```yaml
name: Linux.Mounts
description: List mounted filesystems by reading /proc/mounts

parameters:
  - name: ProcMounts
    default: /proc/mounts

precondition: |
   SELECT OS From info() where OS = 'linux'

sources:
  - query: |
      SELECT Device, Mount, FSType, split(string=Opts, sep=",") As Options
      FROM parse_records_with_regex(
         file=ProcMounts,
         regex='(?m)^(?P<Device>[^ ]+) (?P<Mount>[^ ]+) (?P<FSType>[^ ]+) '+
             '(?P<Opts>[^ ]+)')


reports:
  - type: CLIENT
    template: |
      # Mounted filesystems

      {{ Query "SELECT * FROM source()" | Table }}

```
