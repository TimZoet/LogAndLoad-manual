Source Information
==================

To make tracking down the origin of messages in a massive log more easy, it is possible to log an exact source location.
Note that this is done in a separate message, not as part of a normal message. Doing this requires a tiny bit more
effort, because you have to write a grand total of two lines of code to log a single message:

.. code-block:: cpp

    // Must be static constexpr, otherwise we cannot use its hash as a template parameter.
    static constexpr auto loc = std::source_location::current();
    // Actual location in the code will be the line above, not the line below.
    stream.sourceInfo<lal::hash(loc)>(loc);
