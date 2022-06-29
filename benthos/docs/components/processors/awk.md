---
title: awk
type: processor
status: stable
categories: ["Mapping"]
---

<!--
     THIS FILE IS AUTOGENERATED!

     To make changes please edit the contents of:
     lib/processor/awk.go
-->

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';


Executes an AWK program on messages. This processor is very powerful as it
offers a range of [custom functions](#awk-functions) for querying and mutating
message contents and metadata.

```yml
# Config fields, showing default values
label: ""
awk:
  codec: ""
  program: ""
```

Works by feeding message contents as the program input based on a chosen
[codec](#codecs) and replaces the contents of each message with the result. If
the result is empty (nothing is printed by the program) then the original
message contents remain unchanged.

Comes with a wide range of [custom functions](#awk-functions) for accessing
message metadata, json fields, printing logs, etc. These functions can be
overridden by functions within the program.

Check out the [examples section](#examples) in order to see how this processor
can be used.

This processor uses [GoAWK][goawk], in order to understand the differences
in how the program works you can [read more about it here][goawk.differences].

## Fields

### `codec`

A [codec](#codecs) defines how messages should be inserted into the AWK program as variables. The codec does not change which [custom Benthos functions](#awk-functions) are available. The `text` codec is the closest to a typical AWK use case.


Type: `string`  
Default: `""`  
Options: `none`, `text`, `json`.

### `program`

An AWK program to execute


Type: `string`  
Default: `""`  

## Examples

<Tabs defaultValue="JSON Mapping and Arithmetic" values={[
{ label: 'JSON Mapping and Arithmetic', value: 'JSON Mapping and Arithmetic', },
{ label: 'Stuff With Arrays', value: 'Stuff With Arrays', },
]}>

<TabItem value="JSON Mapping and Arithmetic">


Because AWK is a full programming language it's much easier to map documents and
perform arithmetic with it than with other Benthos processors. For example, if
we were expecting documents of the form:

```json
{"doc":{"val1":5,"val2":10},"id":"1","type":"add"}
{"doc":{"val1":5,"val2":10},"id":"2","type":"multiply"}
```

And we wished to perform the arithmetic specified in the `type` field,
on the values `val1` and `val2` and, finally, map the result into the
document, giving us the following resulting documents:

```json
{"doc":{"result":15,"val1":5,"val2":10},"id":"1","type":"add"}
{"doc":{"result":50,"val1":5,"val2":10},"id":"2","type":"multiply"}
```

We can do that with the following:

```yaml
pipeline:
  processors:
  - awk:
      program: |
        function map_add_vals() {
          json_set_int("doc.result", json_get("doc.val1") + json_get("doc.val2"));
        }
        function map_multiply_vals() {
          json_set_int("doc.result", json_get("doc.val1") * json_get("doc.val2"));
        }
        function map_unknown(type) {
          json_set("error","unknown document type");
          print_log("Document type not recognised: " type, "ERROR");
        }
        {
          type = json_get("type");
          if (type == "add")
            map_add_vals();
          else if (type == "multiply")
            map_multiply_vals();
          else
            map_unknown(type);
        }
```

</TabItem>
<TabItem value="Stuff With Arrays">


It's possible to iterate JSON arrays by appending an index value to the path,
this can be used to do things like removing duplicates from arrays. For example,
given the following input document:

```json
{"path":{"to":{"foos":["one","two","three","two","four"]}}}
```

We could create a new array `foos_unique` from `foos` giving us the result:

```json
{"path":{"to":{"foos":["one","two","three","two","four"],"foos_unique":["one","two","three","four"]}}}
```

With the following config:

```yaml
pipeline:
  processors:
  - awk:
      program: |
        {
          array_path = "path.to.foos"
          array_len = json_length(array_path)

          for (i = 0; i < array_len; i++) {
            ele = json_get(array_path "." i)
            if ( ! ( ele in seen ) ) {
              json_append(array_path "_unique", ele)
              seen[ele] = 1
            }
          }
        }
```

</TabItem>
</Tabs>

## Codecs

The chosen codec determines how the contents of the message are fed into the
program. Codecs only impact the input string and variables initialised for your
program, they do not change the range of custom functions available.

### `none`

An empty string is fed into the program. Functions can still be used in order to
extract and mutate metadata and message contents.

This is useful for when your program only uses functions and doesn't need the
full text of the message to be parsed by the program, as it is significantly
faster.

### `text`

The full contents of the message are fed into the program as a string, allowing
you to reference tokenised segments of the message with variables ($0, $1, etc).
Custom functions can still be used with this codec.

This is the default codec as it behaves most similar to typical usage of the awk
command line tool.

### `json`

An empty string is fed into the program, and variables are automatically
initialised before execution of your program by walking the flattened JSON
structure. Each value is converted into a variable by taking its full path,
e.g. the object:

``` json
{
	"foo": {
		"bar": {
			"value": 10
		},
		"created_at": "2018-12-18T11:57:32"
	}
}
```

Would result in the following variable declarations:

```
foo_bar_value = 10
foo_created_at = "2018-12-18T11:57:32"
```

Custom functions can also still be used with this codec.

## AWK Functions

### `json_get`

Signature: `json_get(path)`

Attempts to find a JSON value in the input message payload by a
[dot separated path](/docs/configuration/field_paths) and returns it as a string.

### `json_set`

Signature: `json_set(path, value)`

Attempts to set a JSON value in the input message payload identified by a
[dot separated path](/docs/configuration/field_paths), the value argument will be interpreted
as a string.

In order to set non-string values use one of the following typed varieties:

- `json_set_int(path, value)`
- `json_set_float(path, value)`
- `json_set_bool(path, value)`

### `json_append`

Signature: `json_append(path, value)`

Attempts to append a value to an array identified by a
[dot separated path](/docs/configuration/field_paths). If the target does not
exist it will be created. If the target exists but is not already an array then
it will be converted into one, with its original contents set to the first
element of the array.

The value argument will be interpreted as a string. In order to append
non-string values use one of the following typed varieties:

- `json_append_int(path, value)`
- `json_append_float(path, value)`
- `json_append_bool(path, value)`

### `json_delete`

Signature: `json_delete(path)`

Attempts to delete a JSON field from the input message payload identified by a
[dot separated path](/docs/configuration/field_paths).

### `json_length`

Signature: `json_length(path)`

Returns the size of the string or array value of JSON field from the input
message payload identified by a [dot separated path](/docs/configuration/field_paths).

If the target field does not exist, or is not a string or array type, then zero
is returned. In order to explicitly check the type of a field use `json_type`.

### `json_type`

Signature: `json_type(path)`

Returns the type of a JSON field from the input message payload identified by a
[dot separated path](/docs/configuration/field_paths).

Possible values are: "string", "int", "float", "bool", "undefined", "null",
"array", "object".

### `create_json_object`

Signature: `create_json_object(key1, val1, key2, val2, ...)`

Generates a valid JSON object of key value pair arguments. The arguments are
variadic, meaning any number of pairs can be listed. The value will always
resolve to a string regardless of the value type. E.g. the following call:

`create_json_object("a", "1", "b", 2, "c", "3")`

Would result in this string:

`{"a":"1","b":"2","c":"3"}`

### `create_json_array`

Signature: `create_json_array(val1, val2, ...)`

Generates a valid JSON array of value arguments. The arguments are variadic,
meaning any number of values can be listed. The value will always resolve to a
string regardless of the value type. E.g. the following call:

`create_json_array("1", 2, "3")`

Would result in this string:

`["1","2","3"]`

### `metadata_set`

Signature: `metadata_set(key, value)`

Set a metadata key for the message to a value. The value will always resolve to
a string regardless of the value type.

### `metadata_get`

Signature: `metadata_get(key) string`

Get the value of a metadata key from the message.

### `timestamp_unix`

Signature: `timestamp_unix() int`

Returns the current unix timestamp (the number of seconds since 01-01-1970).

### `timestamp_unix`

Signature: `timestamp_unix(date) int`

Attempts to parse a date string by detecting its format and returns the
equivalent unix timestamp (the number of seconds since 01-01-1970).

### `timestamp_unix`

Signature: `timestamp_unix(date, format) int`

Attempts to parse a date string according to a format and returns the equivalent
unix timestamp (the number of seconds since 01-01-1970).

The format is defined by showing how the reference time, defined to be
`Mon Jan 2 15:04:05 -0700 MST 2006` would be displayed if it were the value.

### `timestamp_unix_nano`

Signature: `timestamp_unix_nano() int`

Returns the current unix timestamp in nanoseconds (the number of nanoseconds
since 01-01-1970).

### `timestamp_unix_nano`

Signature: `timestamp_unix_nano(date) int`

Attempts to parse a date string by detecting its format and returns the
equivalent unix timestamp in nanoseconds (the number of nanoseconds since
01-01-1970).

### `timestamp_unix_nano`

Signature: `timestamp_unix_nano(date, format) int`

Attempts to parse a date string according to a format and returns the equivalent
unix timestamp in nanoseconds (the number of nanoseconds since 01-01-1970).

The format is defined by showing how the reference time, defined to be
`Mon Jan 2 15:04:05 -0700 MST 2006` would be displayed if it were the value.

### `timestamp_format`

Signature: `timestamp_format(unix, format) string`

Formats a unix timestamp. The format is defined by showing how the reference
time, defined to be `Mon Jan 2 15:04:05 -0700 MST 2006` would be displayed if it
were the value.

The format is optional, and if omitted RFC3339 (`2006-01-02T15:04:05Z07:00`)
will be used.

### `timestamp_format_nano`

Signature: `timestamp_format_nano(unixNano, format) string`

Formats a unix timestamp in nanoseconds. The format is defined by showing how
the reference time, defined to be `Mon Jan 2 15:04:05 -0700 MST 2006` would be
displayed if it were the value.

The format is optional, and if omitted RFC3339 (`2006-01-02T15:04:05Z07:00`)
will be used.

### `print_log`

Signature: `print_log(message, level)`

Prints a Benthos log message at a particular log level. The log level is
optional, and if omitted the level `INFO` will be used.

[goawk]: https://github.com/benhoyt/goawk
[goawk.differences]: https://github.com/benhoyt/goawk#differences-from-awk
