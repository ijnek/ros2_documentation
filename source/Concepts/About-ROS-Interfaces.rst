.. _InterfaceConcept:

.. redirect-from::

    About-ROS-Interfaces

About ROS 2 interfaces
======================

.. contents:: Table of Contents
   :local:

1. Background
-------------

ROS applications typically communicate through interfaces of one of three types: messages, services and actions.
ROS 2 uses a simplified description language, the `Interface Definition Language (IDL) <https://www.omg.org/spec/IDL/>`__, to describe these interfaces.
This description makes it easy for ROS tools to automatically generate source code for the interface type in several target languages.

In this document we will describe the supported types.

* msg: ``.msg`` files are simple text files that describe the fields of a ROS message. They are used to generate source code for messages in different languages.
* srv: ``.srv`` files describe a service. They are composed of two parts: a request and a response. The request and response are message declarations.
* action: ``.action`` files describe actions. They are composed of three parts: a goal, a result, and feedback.
  Each part is a message declaration itself.


2. Message description specification
------------------------------------

Messages are described and defined in ``.msg`` files in the ``msg/`` directory of a ROS package.
``.msg`` files are composed of two parts: fields and constants.

2.1 Fields
^^^^^^^^^^

Each field consists of a type and a name, separated by a space, i.e:

.. code-block:: bash

   fieldtype1 fieldname1
   fieldtype2 fieldname2
   fieldtype3 fieldname3

For example:

.. code-block:: bash

   int32 my_int
   string my_string

2.1.1 Field types
~~~~~~~~~~~~~~~~~

Field types can be:


* a built-in-type
* names of Message descriptions defined on their own, such as "geometry_msgs/PoseStamped"

*Built-in-types currently supported:*

.. list-table::
   :header-rows: 1

   * - ROS IDL
     - IDL
     - C
     - C++
     - Python
   * - bool
     - boolean
     - bool
     - bool
     - bool
   * - byte
     - octet
     - uint8_t
     - unsigned char
     - bytes
   * - char
     - uint8 (char)
     - uint8_t
     - uint8_t
     - int
   * - float32
     - float
     - float
     - float
     - float
   * - float64
     - double
     - double
     - double
     - float
   * - int8
     - int8
     - int8_t
     - int8_t
     - int
   * - uint8
     - uint8
     - uint8_t
     - uint8_t
     - int
   * - int16
     - int16 (short)
     - int16_t
     - int16_t
     - int
   * - uint16
     - uint16 (unsigned short)
     - uint16_t
     - uint16_t
     - int
   * - int32
     - int32 (long)
     - int32_t
     - int32_t
     - int
   * - uint32
     - uint32 (unsigned long)
     - uint32_t
     - uint32_t
     - int
   * - int64
     - int64 (long long)
     - int64_t
     - int64_t
     - int
   * - uint64
     - uint64 (unsigned long long)
     - uint64_t
     - uint64_t
     - int
   * - string
     - string
     - char *
     - std::string
     - str
   * - wstring
     - wstring
     - char16_t *
     - std::u16string
     - str

*Every built-in-type can be used to define arrays:*

.. list-table::
   :header-rows: 1

   * - Type name
     - `C++ <https://design.ros2.org/articles/generated_interfaces_cpp.html>`__
     - `Python <https://design.ros2.org/articles/generated_interfaces_python.html>`__
     - `DDS type <https://design.ros2.org/articles/mapping_dds_types.html>`__
   * - static array
     - std::array
     - list
     - T[N]
   * - unbounded dynamic array
     - std::vector
     - list
     - sequence<T>
   * - bounded dynamic array
     - rosidl_runtime_cpp::BoundedVector
     - list
     - sequence<T, N>
   * - bounded string
     - std::string
     - str
     - string

Arrays are defined as below, where T can be any of the built-in-types.

.. list-table::
   :header-rows: 1

   * - Description
     - ROS IDL
     - Primitive Type (T)
     - IDL
     - C
     - C++
     - Python
   * - Dynamic Array
     - T[]
     - bool, char, string, wstring
     - sequence<T>
     - C
     - std::vector
     - list
   * -
     -
     - float32, float64, int8, uint8, int16, uint16, int32, uint32, int64, uint64
     - sequence<T>
     - C
     - std::vector
     - array.array
   * -
     -
     - byte
     - sequence<T>
     - C
     - std::vector
     - bytes
   * - Fixed-Size Array
     - T[N]
     - bool, char, string, wstring
     - T__N
     - C
     - std::array
     - list
   * -
     -
     - float32, float64, int8, uint8, int16, uint16, int32, uint32, int64, uint64
     - T__N
     - C
     - std::array
     - numpy.ndarray
   * -
     -
     - byte
     - byte__N
     - C
     - std::array
     - bytes
   * - Bounded Array
     - T[<=N]
     - bool, char, string, wstring
     - sequence<T, N>
     - C
     - rosidl_runtime_cpp::BoundedVector
     - list
   * -
     -
     - float32, float64, int8, uint8, int16, uint16, int32, uint32, int64, uint64
     - sequence<T, N>
     - C
     - rosidl_runtime_cpp::BoundedVector
     - array.array
   * -
     -
     - byte
     - sequence<byte, N>
     - C
     - rosidl_runtime_cpp::BoundedVector
     - bytes

.. list-table::
   :header-rows: 1

   * - Type name
     - `DDS type <https://design.ros2.org/articles/mapping_dds_types.html>`__
     - `Python <https://design.ros2.org/articles/generated_interfaces_python.html>`__
   * - static array
     - T[N]
     - numpy.ndarray(shape=(N, ), dtype=numpy.DT)
   * - unbounded dynamic array
     - sequence<T>
     - array.array(typecode=TC)
   * - bounded dynamic array
     - sequence<T, N>
     - array.array(typecode=TC)
   * - byte static array
     - octet[N]
     - bytes
   * - byte unbounded dynamic array
     - sequence<octet>
     - bytes
   * - byte bounded dynamic array
     - sequence<octet, N>
     - bytes


All types that are more permissive than their ROS definition enforce the ROS constraints in range and length by software

*Example of message definition using arrays and bounded types:*

.. code-block:: bash

   int32[] unbounded_integer_array
   int32[5] five_integers_array
   int32[<=5] up_to_five_integers_array

   string string_of_unbounded_size
   string<=10 up_to_ten_characters_string

   string[<=5] up_to_five_unbounded_strings
   string<=10[] unbounded_array_of_string_up_to_ten_characters_each
   string<=10[<=5] up_to_five_strings_up_to_ten_characters_each

2.1.2 Field names
~~~~~~~~~~~~~~~~~

Field names must be lowercase alphanumeric characters with underscores for separating words. They must start with an alphabetic character, they must not end with an underscore and never have two consecutive underscores.

2.1.3 Field default value
~~~~~~~~~~~~~~~~~~~~~~~~~

Default values can be set to any field in the message type.
Currently default values are not supported for string arrays and complex types (i.e. types not present in the built-in-types table above, that applies to all nested messages)

Defining a default value is done by adding a third element to the field definition line, i.e:

.. code-block:: bash

   fieldtype fieldname fielddefaultvalue

For example:

.. code-block:: bash

   uint8 x 42
   int16 y -2000
   string full_name "John Doe"
   int32[] samples [-200, -100, 0, 100, 200]

Note:


* string values must be defined in single ``'`` or double quotes ``"``
* currently string values are not escaped

2.2 Constants
^^^^^^^^^^^^^

Each constant definition is like a field description with a default value, except that this value can never be changed programatically. This value assignment is indicated by use of an equal '=' sign, e.g.

.. code-block:: bash

   constanttype CONSTANTNAME=constantvalue

For example:

.. code-block:: bash

   int32 X=123
   int32 Y=-123
   string FOO="foo"
   string EXAMPLE='bar'

.. note::

   Constants names have to be UPPERCASE

3. Service description specification
------------------------------------

Services are described and defined in ``.srv`` files in the ``srv/`` directory of a ROS package.

A service description file consists of a request and a response msg type, separated by '---'. Any two ``.msg`` files concatenated with a '---' are a legal service description.

Here is a very simple example of a service that takes in a string and returns a string:

.. code-block:: bash

   string str
   ---
   string str

We can of course get much more complicated (if you want to refer to a message from the same package you must not mention the package name):

.. code-block:: bash

   #request constants
   int8 FOO=1
   int8 BAR=2
   #request fields
   int8 foobar
   another_pkg/AnotherMessage msg
   ---
   #response constants
   uint32 SECRET=123456
   #response fields
   another_pkg/YetAnotherMessage val
   CustomMessageDefinedInThisPackage value
   uint32 an_integer

You cannot embed another service inside of a service.

4. New features in ROS 2 interfaces
-----------------------------------

The ROS 2 IDL is closely related to the `ROS 1 IDL <https://wiki.ros.org/msg>`__.
Most existing ROS 1 ``.msg`` and ``.srv`` files should be usable as-is with ROS 2.
Atop ROS 1's existing feature set, the ROS 2 IDL introduces some new features, namely:


* **bounded arrays**: Whereas the ROS 1 IDL allows unbounded arrays (e.g., ``int32[] foo``) and fixed-size arrays (e.g., ``int32[5] bar``), the ROS 2 IDL further allows bounded arrays (e.g., ``int32[<=5] bat``).
  There are use cases in which it's important to be able to place an upper bound on the size of an array without committing to always using that much space (e.g., in a real-time system in which you need to preallocate all memory that will be used during execution).
* **bounded strings**: Whereas the ROS 1 IDL allows unbounded strings (e.g., ``string foo``), the ROS 2 IDL further allows bounded strings (e.g., ``string<=5 bar``).
* **default values**: Whereas the ROS 1 IDL allows constant fields (e.g., ``int32 X=123``), the ROS 2 IDL further allows default values to be specified (e.g., ``int32 X 123``).
  The default value is used when constructing a message/service object and can be subsequently overridden by assigning to the field.
  You can also specify default values for action parts.
