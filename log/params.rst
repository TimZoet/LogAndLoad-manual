Dynamic Parameters
==================

Apart from whatever is in the static message string, it is of course useful to be able to write dynamic values to the
log. For that, you can make use of dynamic parameters. A dynamic parameter can be specified inside of the message string
using :code:`{}`. This parameter can then be filled in using any variable:

.. code-block:: cpp

    struct SingleParam
    {
        static constexpr char    message[] = "value = {}";
        static constexpr uint32_t category = 0;
    };

    stream.message<SingleParam>(10);

    stream.message<SingleParam>(10.0f);

Note that in the above example, 2 message types are registered because of the different parameter types. One with an
integer, and another with a float.

In principle, parameters can be of any type. However, keep in mind that objects are written to the log by simply copying
them. Passing in a :code:`std::vector` or pointers will not copy the data they contain, but the objects themselves:

.. code-block:: cpp

    struct float3
    {
        float x = 0;
        float y = 0;
        float z = 0;
    };

    // Logging this type makes perfect sense...
    float3 val = {0.0f, 1.0f, 2.0f};
    stream.message<SingleParam>(val);

    // ...but this will probably not do what you expect...
    std::vector<float> vec = {0.0f, 1.0f, 2.0f};
    stream.message<SingleParam>(vec);

    // ...nor will this.
    auto* ptr = new int32_t(10);
    stream.message<SingleParam>(ptr)

You can define any number of parameters in the message. Each occurrence of :code:`{}` specifies one parameter that can
(must) be filled in:

.. code-block:: cpp

    struct ManyParams
    {
        static constexpr char    message[] = "a = {} b = {} c = {}";
        static constexpr uint32_t category = 0;
    };

    float3 val = {0.0f, 1.0f, 2.0f};
    stream.message<ManyParams>(val, 10, 20.0f);

There is one important restriction you must take into account when it comes to the size of parameters that you log. All
parameters in a single call should not be larger than the size of the stream buffer, minus 4 or 12. 4 bytes are needed
for the identifier. An additional 8 bytes are used if you enabled :doc:`order`.
