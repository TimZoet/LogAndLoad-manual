Log
===

.. toctree::
    :maxdepth: 3
    :titlesonly:
    :hidden:

    log/categories
    log/params
    log/order
    log/regions
    log/source_info

All logging starts with creating a :code:`lal::Log` object. The constructor takes two parameters. The first is a path to
the file to which the log messages are going to be written. The second parameter is the size of the global buffer in
bytes. Note that both a front and back buffer are allocated, so the actual amount of allocated data is twice as large:

.. code-block:: cpp

    auto log = lal::Log("log.bin", 4096);

Streams
-------

Writing to the log isn't done directly through the :code:`lal::Log` object, but a :code:`lal::Stream`. Streams can be
constructed with the :code:`createStream` method. This method has only a single parameter, the buffer size. This buffer
size cannot be larger than the global buffer size:

.. code-block:: cpp

    auto& stream = log.createStream(4096);

Streams also have two buffers. When writing messages to a stream, the front buffer will of course fill up. Once it is
full, the front and back buffer are swapped. Further messages will go to the new front buffer. Meanwhile the filled back
buffer will be transferred (flushed) to the global front buffer.

If the global front buffer is full, the same thing happens. Front and back are switched. Stream front buffers are
flushed to the new global front buffer. The global back buffer is written to the log file. Naturally, this disk access
is the part with the highest latency. However, as long as writes complete before a stream's front buffer has filled up
while the back buffer is still pending, the latency of writing new messages will stay low.

Writing to a stream is not thread safe. For that you can create multiple streams. There are no restrictions on how many
streams can be created at once, although obviously more streams require more synchronization.

.. code-block:: cpp

    // Buffer size can be different per stream.
    auto& stream0 = log.createStream(4096);
    auto& stream1 = log.createStream(1024);
    auto& stream2 = log.createStream(2048);

Messages
--------

To write a message to a stream, just call the :code:`message` method. This method requires a compile-time string wrapped
in a struct or class. This type should satisfy the :code:`lal::is_format_type` concept:

.. code-block:: cpp

    // Type satisfying is_format_type.
    struct MyMessage
    {
        static constexpr char    message[] = "Hello!";
        static constexpr uint32_t category = 0;
    };

    // Will register MyMessage and write a reference to it to the log.
    stream.message<MyMessage>();

    // MyMessage was already registered, so just a reference is written.
    stream.message<MyMessage>();

(The :code:`category` member is explained in :doc:`log/categories`.) The first time you write a particular message to
the log, it is registered. What is written to the log (or rather, the stream buffer) is just an identifier of type
:code:`uint32_t`. This identifier is a hash of the :code:`message`, :code:`category` and the types of any
:doc:`log/params`. When the log is destructed (probably right around when the application terminates), all registered
messages are stored in a file next to the log file. That way, the data in the log can be reconstructed.

Further information on writing non-static data and special messages, and group or order messages can be found on the
various other pages in this section.
