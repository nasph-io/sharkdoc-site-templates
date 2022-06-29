---
title: read_until
type: input
status: stable
categories: ["Utility"]
---

<!--
     THIS FILE IS AUTOGENERATED!

     To make changes please edit the contents of:
     lib/input/read_until.go
-->

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';


Reads messages from a child input until a consumed message passes a [Bloblang query](/docs/guides/bloblang/about/), at which point the input closes.

```yml
# Config fields, showing default values
input:
  label: ""
  read_until:
    input: {}
    check: ""
    restart_input: false
```

Messages are read continuously while the query check returns false, when the query returns true the message that triggered the check is sent out and the input is closed. Use this to define inputs where the stream should end once a certain message appears.

Sometimes inputs close themselves. For example, when the `file` input type reaches the end of a file it will shut down. By default this type will also shut down. If you wish for the input type to be restarted every time it shuts down until the query check is met then set `restart_input` to `true`.

### Metadata

A metadata key `benthos_read_until` containing the value `final` is added to the first part of the message that triggers the input to stop.

## Fields

### `input`

The child input to consume from.


Type: `input`  
Default: `null`  

### `check`

A [Bloblang query](/docs/guides/bloblang/about/) that should return a boolean value indicating whether the input should now be closed.


Type: `string`  
Default: `""`  

```yml
# Examples

check: this.type == "foo"

check: count("messages") >= 100
```

### `restart_input`

Whether the input should be reopened if it closes itself before the condition has resolved to true.


Type: `bool`  
Default: `false`  

## Examples

<Tabs defaultValue="Consume N Messages" values={[
{ label: 'Consume N Messages', value: 'Consume N Messages', },
]}>

<TabItem value="Consume N Messages">

A common reason to use this input is to consume only N messages from an input and then stop. This can easily be done with the [`count` function](/docs/guides/bloblang/functions/#count):

```yaml
# Only read 100 messages, and then exit.
input:
  read_until:
    check: count("messages") >= 100
    input:
      kafka:
        addresses: [ TODO ]
        topics: [ foo, bar ]
        consumer_group: foogroup
```

</TabItem>
</Tabs>

