Dynamic Parameters
==================

The :code:`lal::Formatter` class keeps a map of functions to write dynamic parameters. By default, it will register
functions for all integer and floating point types. In case you logged parameters of any other type, you must register a
function that writes to a :code:`std::ostream` using the :code:`registerParameter` method:

.. code-block:: cpp

    struct float3
    {
        float x = 0;
        float y = 0;
        float z = 0;
    };

    auto formatter = lal::Formatter();
    
    formatter.registerParameter<float3>([](std::ostream& out, const float3& v){
        out << "[" << v.x << ", " << v.y << ", " << v.z << "]";
    });

You can override the already registered functions, should you be unhappy with how e.g. floating point values are
formatted.
