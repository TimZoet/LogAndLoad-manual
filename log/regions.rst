Regions
=======

To add more structure to logs you can add anonymous and named regions. Regions can be used to group messages. When a
region is constructed it writes a 'start' message. On destruction it writes an 'end' message:

.. code-block:: cpp

    // Messages before region.
    ...

    {
        // Anonymous region is constructed, 'start' written to log.
        auto region = stream.region();

        // All messages inbetween are part of the region.
        ...

        // Region goes out of scope, 'end' written to log.
        ~region...
    }

    // Messages after region.
    ...

You can also nest regions. Additionally, named regions can be created by passing in a format type. Note that any braces
in the format string are just printed as braces. You cannot provide any parameters:

.. code-block:: cpp

    struct MyRegion
    {
        static constexpr char    message[] = "MyRegion {}"; // Braces are just printed as-is.
        static constexpr uint32_t category = 0;
    };

    {
        auto namedRegion = stream.region<MyRegion>();
        ...
    }

By default, regions are not movable. If you do want to move a region out of a scope for more advanced control flow, you
can create a movable region with the :code:`movableRegion` method. 
