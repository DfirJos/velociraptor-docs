---
title: Windows.Registry.BackupRestore
hidden: true
tags: [Client Artifact]
---

This artifact will return BackupRestore configuration.

Applications that request or perform backup and restore operations can use
these keys to communicate with each other or with features such as the
Volume Shadow Copy Service (VSS) and Windows Backup.


```yaml
name: Windows.Registry.BackupRestore
author: Matt Green - @mgreen27
description: |
    This artifact will return BackupRestore configuration.

    Applications that request or perform backup and restore operations can use
    these keys to communicate with each other or with features such as the
    Volume Shadow Copy Service (VSS) and Windows Backup.

reference:
  - https://andreafortuna.org/2017/10/02/volume-shadow-copies-in-forensic-analysis/
  - https://docs.microsoft.com/en-us/windows/win32/backup/registry-keys-for-backup-and-restore

parameters:
 - name: KeyGlob
   default: HKEY_LOCAL_MACHINE\SYSTEM\*ControlSet*\Control\BackupRestore\**

sources:
  - precondition:
      SELECT OS From info() where OS = 'windows'

    query: |
      -- output rows and dedup on unique values for each
      SELECT ModTime,FullPath,
        Name as KeyName,
        Data.value as KeyValue,
        Data.type as KeyType
      FROM glob(globs=KeyGlob, accessor="registry")
      WHERE NOT KeyType ='key'
      GROUP BY ModTime, KeyName,KeyValue,KeyType

```
