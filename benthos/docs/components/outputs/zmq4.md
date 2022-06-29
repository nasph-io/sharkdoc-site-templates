---
title: zmq4
type: output
status: stable
categories: ["Network"]
---

<!--
     THIS FILE IS AUTOGENERATED!

     To make changes please edit the contents of:
     lib/output/zmq4.go
-->

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

Writes messages to a ZeroMQ socket.


<Tabs defaultValue="common" values={[
  { label: 'Common', value: 'common', },
  { label: 'Advanced', value: 'advanced', },
]}>

<TabItem value="common">

```yml
# Common config fields, showing default values
output:
  label: ""
  zmq4:
    urls: []
    bind: true
    socket_type: ""
```

</TabItem>
<TabItem value="advanced">

```yml
# All config fields, showing default values
output:
  label: ""
  zmq4:
    urls: []
    bind: true
    socket_type: ""
    high_water_mark: 0
    poll_timeout: 5s
```

</TabItem>
</Tabs>

By default Benthos does not build with components that require linking to external libraries. If you wish to build Benthos locally with this component then set the build tag `x_benthos_extra`:

```shell
# With go
go install -tags "x_benthos_extra" github.com/benthosdev/benthos/v4/cmd/benthos@latest

# Using make
make TAGS=x_benthos_extra
```

There is a specific docker tag postfix `-cgo` for C builds containing this component.

## Fields

### `urls`

A list of URLs to connect to. If an item of the list contains commas it will be expanded into multiple URLs.


Type: `array`  

```yml
# Examples

urls:
  - tcp://localhost:5556
```

### `bind`

Whether to bind to the specified URLs (otherwise they are connected to).


Type: `bool`  
Default: `true`  

### `socket_type`

The socket type to connect as.


Type: `string`  
Options: `PUSH`, `PUB`.

### `high_water_mark`

The message high water mark to use.


Type: `int`  
Default: `0`  

### `poll_timeout`

The poll timeout to use.


Type: `string`  
Default: `"5s"`  

