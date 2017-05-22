---
layout: post
title: Syndicate Hadoop FileSystem Driver
---

{% include toc.html %}

`H-Syndicate` allows users to access datasets on Syndicate Volumes from `Hadoop`. 
Plugged into `Hadoop FileSystem Abstraction`, Hadoop users can access Syndicate 
Volumes in the same way as `Hadoop Native Filesystem (HDFS)`.

## Prerequisites

You must first [Install H-Syndicate](#) and [Mount Syndicate Gateways](#).
Once Syndicate Gateways are mounted, they will issue -- as you requested -- `session_name` 
and `session_key` for each mount. 


## Path (URI)

To call `H-Syndicate` for access, give `URI scheme` for `H-Syndicate`. We recommend 
`hsyn://` but this can differ from environments by policy. `Hadoop` will redirect 
accesses to `H-Syndicate` when requested file path (URI) has the corresponding 
scheme.

`H-Syndicate`'s path is defined as following:
```
<scheme>://<Syndicate_REST_host>/<session_name>/path/to/file
```

Most cases, users can omit `host` part. In this case, local `Syndicate REST service` 
will be accessed.

Following example is a typical form:
```
hsyn:///<session_name>/path/to/file
```

## Session Key

`session_key` is not passed directly via path for security. `session_key` is 
passed to `H-Syndicate` securely via `Hadoop Credential Provider`.

It creates a secure database storing your all `session_name` and `session_key` 
pairs on `Hadoop File System (HDFS)`. Then, `H-Syndicate` finds corresponding 
`session_key` to a given `session_name` from the database.

To store `session_key` to the database:
```
hadoop credential create \
            fs.hsyndicate.session.<session_name>.key \
            -v <session_key> \
            -provider jceks://hdfs/user/<hadoop_user>/.syndicate/hsyndicate.jceks
```
You can use different paths for the database file. But we recommend place them 
under your home directory for security.

To use the database, the database path should be given. One way is defining 
`hadoop.security.credential.provider.path` configuration parameter.
```
-D hadoop.security.credential.provider.path=jceks://hdfs/user/<hadoop_user>/.syndicate/hsyndicate.jceks
```

Thus, listing a root directory for a session, `test_session`, is:
```
hadoop dfs -D hadoop.security.credential.provider.path=jceks://hdfs/user/<hadoop_user>/.syndicate/hsyndicate.jceks -ls hsyn:///test_session/
```

For more information about `Hadoop Credential Provider`, checkout following links:  
* [CredentialProvider API Guide](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/CredentialProviderAPI.htmlhttps://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/CredentialProviderAPI.html)
