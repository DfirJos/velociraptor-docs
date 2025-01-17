---
title: Windows.ETW.ETWSessions
hidden: true
tags: [Client Event Artifact]
---

Windows Event Tracing exposes a lot of low level system information
and events. It is normally employed by security tools to gather
telemetry, however may also be used maliciously.

This artifact monitors for all new ETW sessions and reports the
tracing process as well as the provider that is being traced.


```yaml
name: Windows.ETW.ETWSessions
description: |
  Windows Event Tracing exposes a lot of low level system information
  and events. It is normally employed by security tools to gather
  telemetry, however may also be used maliciously.

  This artifact monitors for all new ETW sessions and reports the
  tracing process as well as the provider that is being traced.

type: CLIENT_EVENT

precondition: SELECT OS From info() where OS = 'windows'

sources:
  - query: |
      LET PublisherGlob = pathspec(
        Path='''HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\WINEVT\Publishers''',
        path_type="registry")

      LET GUIDLookup(GUID) = SELECT Data.value AS Provider
         FROM stat(accessor="registry", filename=PublisherGlob + ("/" + GUID + "/@"))

      SELECT System.TimeStamp AS Timestamp,
        if(condition=System.ID = 14, then="Installed", else="Removed") AS Action, {
           SELECT Name, CommandLine from pslist(pid=System.ProcessID)
        } AS ProcessInfo ,
        GUIDLookup(GUID=EventData.ProviderName)[0].Provider AS Provider,
        EventData.SessionName AS SessionName,
        System AS _System, EventData AS _EventData
      FROM watch_etw(guid="{B675EC37-BDB6-4648-BC92-F3FDC74D3CA2}", all=0x400)
      WHERE System.ID IN (14, 15)

```
