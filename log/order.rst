Message Ordering
================

While messages within a single stream are perfectly ordered, messages across streams are not synchronized in any way. In
practice, this is quite difficult and will mostly depend on how you do your own synchronization. However, it is possible
to have all messages write a unique message index. This is done using a :code:`std::atomic_uint64_t` held by the log
object that is incremented by the stream every time it writes a message. The message index is written and incremented
after writing the message identifier and before writing any dynamic parameters. Enabling this feature is done using a
template parameter of the :code:`lal::Log`:

.. code-block:: cpp

    using log_t = lal::Log<..., lal::Ordering::Enabled>;

Keep in mind that this message index doesn't really give many guarantees about the actual order in which things
happened. It is mostly a convenient way to give all messages a unique index.
