# Content
* List of YANG modules
* How to write an ubus specific YANG module

# List of YANG modules
The supplied YANG modules are as follows:
* generic-ubus-router-system.yang
* generic-ubus-router-wireless.yang
* generic-ubus-sip.yang
* generic-ubus-system.yang

# How to write an ubus specific YANG module
This section is related to how to write an ubus specific YANG module that
is able to be read with the [generic ubus plugin](). <!-- TODO add link -->

There are a set of rules that need to be met when writing an ubus specific
YANG module for state data representation. The rules are as follows:
* root container needs to be the same name as the module; but module name is
  has no predefined naming conventions,
* sibling child container of the root container represent the ubus object
  methods and need to have the same name as the ubus object methods,
* child elements of a ubus object method container are directly mapped from the
  ubus object method invocation response, in JSON format, to YANG format;
  meaning that the JSON keys and values are converted to the appropriate YANG module statements and types.

The way that the generic ubus plugin converts the JSON keys and values to the appropriate YANG module statements and types is as follows:

| JSON                                |       YANG        |
|-------------------------------------|:-----------------:|
| string                              |   leaf            |
| number                              |   leaf            |
| boolean                             |   leaf            |
| array of {string, number, boolean}  |   leaf-list       |
| array of {array, object}            |   list            |
| object                              |   container       |

## Example: ubus specific YANG module

To demonstrate how to use the above provided rules a simple example is
provided below. The example contains a simp JSON object simbolizing a
ubus object method response and the YANG module that is created for the ubus
object following the rules.

### Ubus object information

| ubus object | ubus method | ubus method          |
|-------------|:------------|:---------------------|
|   weather   |   report    | { "citiy" : string } |

### Ubus object method response JSON format

```
{
    "temperature": 23.4,
    "humidity": 50,
    "rain": 40
}
```

### Ubus object method response YANG format

```
module ubus-weather {
    container ubus-weather {
        config false;

        container report {
            leaf temperature {
                type string;
            }
            leaf humidity {
                type string;
            }
            leaf rain {
                type string;
            }
        }
    }
}
```

### Registering the ubus object in the generic ubus plugin YANG module

```
<generic-ubus-config xmlns="https://terastream/ns/yang/terastream-generic-ubus">
  <ubus-object>
    <name>weather</name>
    <yang-module>ubus-weather</yang-module>
    <method>
        <name>report</name>
        <message>{"city": "City_name"}</message>
    </method>
  </ubus-object>
</generic-ubus-config>
```