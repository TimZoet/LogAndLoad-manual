Filtering
=========

There are different ways to enable or disable specific nodes. This can be done based on whether they have parameters
with specific values, are of a specific category, part of a certain region, etc. All these operations are available
through various :code:`filter...` methods. These methods all follow roughly the same pattern. They require a function
object that is applied to the matching nodes. This function will receive the old state, or flags, the node object itself
and sometimes additional parameters.

Messages
--------

The :code:`filterMessage` method can be used to match specific message nodes. The method takes a format type as template
parameter, plus a list of parameter types. The number of types should obviously match the number of parameters in the
message string. The function object parameter is applied to all nodes that contain the specified message. See below for
a single-parameter example:

.. code-block:: cpp

    // Format type with a single parameter.
    struct MyMessage
    {
        static constexpr char     message[] = "{}";
        static constexpr uint32_t category  = 0;
    };
    ...

    // Write messages with a float or int16_t.
    auto& stream = ...;
    stream.message<MyMessage>(-5.0f);      // 0
    stream.message<MyMessage>(int16_t(5)); // 1
    stream.message<MyMessage>(20.0f);      // 2
    ...

    lal::Tree tree(...);
    ...

    // Match all message nodes for MyMessage with a float parameter.
    // Will match 0 and 2. Disables 0.
    tree.filterMessage<MyMessage, float>([](lal::Tree::Flags oldFlags, const lal::Node& node) {
        if (node.get<float>(0) < 0.0f) return lal::Tree::Flags::Disabled;
        return oldFlags;
    });

As you can see, to retrieve the actual value of a parameter you can call the node's :code:`get` method with a parameter
type and index. Obivously, this parameter must match the corresponding element in the list of types you passed to
:code:`filterMessage`. Going to multiple parameters is trivial. Just pass another type to:code:`filterMessage` and a
non-zero index to :code:`get`.

You can also pass :code:`std::nullptr_t` for a parameter if you don't care about it. See below for an example where we
only care when the second parameter is of a certain type:

.. code-block:: cpp

    // Format type with two parameter.
    struct AnotherMessage
    {
        static constexpr char     message[] = "{} {}";
        static constexpr uint32_t category  = 0;
    };

    // Custom type.
    struct float2 { float x, y; };
    ...

    // Write a mixture of floats, integers and custom types.
    auto& stream = ...;
    stream.message<AnotherMessage>(10.0f, int16_t(5));                 // 0
    stream.message<AnotherMessage>(-20.0f, uint64_t(10));              // 1
    stream.message<AnotherMessage>(float2{0.5f, -1.5f}, uint64_t(11)); // 2
    ...

    // Match all message nodes for MyMessage with an uint64_t parameter at index 1.
    // Will match 1 and 2. Disables 1.
    tree.filterMessage<AnotherMessage, std::nullptr_t, uint64_t>([](lal::Tree::Flags oldFlags, const lal::Node& node) {
        if (node.get<uint64_t>(1) % 2 == 0) return lal::Tree::Flags::Disabled;
        return oldFlags;
    });

Mind you, it is still possible to do something with unspecified parameter types. Continuing the above example, we could
check if the first parameter is of a certain type using the node's :code:`has` method. We can then take its value into
account:

.. code-block:: cpp

    // Will match 1 and 2. Disables none.
    tree.filterMessage<AnotherMessage, std::nullptr_t, uint64_t>([](lal::Tree::Flags oldFlags, const lal::Node& node) {
        // We have important reasons to always want to enable messages with a negative float at index 0.
        if (node.has<float>(0) && node.get<float>(0) < 0>) return lal::Tree::Flags::Enabled;
        if (node.get<uint64_t>(1) % 2 == 0) return lal::Tree::Flags::Disabled;
        return oldFlags;
    });

Streams
-------

Stream nodes can be filtered using the :code:`filterStream` method. It takes a function that itself receives the old
node flags, the stream node object and the stream index:

.. code-block:: cpp

    // Enable only stream 1.
    tree.filterStream([](lal::Tree::Flags oldFlags, const lal::Node& node, size_t index) {
        return index == 1 ? lal::Tree::Flags::Enabled : lal::Tree::Flags::Disabled;
    });

Categories
----------

All message nodes have a category, and they can be filtered based on this value. This is done by passing a function to
the :code:`filterCategory` method that will be applied to all message nodes. For example, if the category represents
some kind of severity, you might want to disable all messages that don't contain errors:

.. code-block:: cpp

    static constexpr uint32_t info    = 0;
    static constexpr uint32_t warning = 1;
    static constexpr uint32_t error   = 2;
    static constexpr uint32_t fatal   = 3;
    ...

    // Disable all messages with low severity.
    tree.filterCategory([](lal::Tree::Flags oldFlags, uint32_t category) {
        return category < error ? lal::Tree::Flags::Disabled : lal::Tree::Flags::Enabled;
    });

.. note::

    TODO: Named regions can also have a category. How are/should these be handled?

Regions
-------

As described in other sections, regions come in two flavours: anonymous and named. Anonymous region nodes do not have a
valid :code:`formatType`. You can use this to, for example, disable all of them:

.. code-block:: cpp

    // Disable all anonymous regions and leave named nodes as they are.
    tree.filterRegion([](lal::Tree::Flags oldFlags, const lal::Node& node) {
        return node.formatType ? oldFlags : lal::Tree::Flags::Disabled;
    });

Named regions do have a valid :code:`formatType`, which can be used to determine what to do with them using the
:code:`matches` method:

.. code-block:: cpp

    struct MyRegion
    {
        static constexpr char     message[] = "My Region";
        static constexpr uint32_t category  = 0;
    };

    struct SomeOtherRegion
    {
        static constexpr char     message[] = "Some Other Region";
        static constexpr uint32_t category  = 0;
    };
    ...

    tree.filterRegion([](lal::Tree::Flags oldFlags, const lal::Node& node) {
        // Leave anonymous nodes as they are.
        if (!node.formatType) return oldFlags;

        // Disable all regions that don't match MyRegion.
        return node.formatType->matches<MyRegion>() ? lal::Tree::Flags::Enabled : lal::Tree::Flags::Disabled;
    });
