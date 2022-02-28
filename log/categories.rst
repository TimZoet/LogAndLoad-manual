Categories
==========

Categorizing messages serves two purposes. Firstly, it allows you to distinguish between e.g. purely informational
messages and errors. Secondly, it is useful to be able to disable some less important messages in a release build, so as
not to impact performance too much. Disabled messages have no runtime performance impact, because they are optimized
out.

Each format type must be given a static member named :code:`category` of type :code:`uint32_t`. The meaning of that
value is up to you. A typical use case might be some kind of severity:

.. code-block:: cpp

    static constexpr uint32_t    info = 0;
    static constexpr uint32_t warning = 1;
    static constexpr uint32_t   error = 2;
    static constexpr uint32_t   fatal = 3;

    struct InfoMessage
    {
        static constexpr char    message[] = "Just saying hello.";
        static constexpr uint32_t category = info;
    };

    struct WarningMessage
    {
        static constexpr char    message[] = "Hmmm. Something might be wrong.";
        static constexpr uint32_t category = warning;
    };

    struct ErrorMessage
    {
        static constexpr char    message[] = "Oh noes! That won't work.";
        static constexpr uint32_t category = error;
    };

    struct FatalMessage
    {
        static constexpr char    message[] = "AAAAAAGH!";
        static constexpr uint32_t category = fatal;
    };

To the :code:`lal::Log` class you must pass a type that satisfies the :code:`lal::is_category_filter` constraint. This
type's static methods will be used to determine which format types are ignored. :code:`LogAndLoad` comes with several
predefined filters:

* :code:`lal::CategoryFilterAll`: Disables all messages.
* :code:`lal::CategoryFilterNone`: Enables all messages.
* :code:`lal::CategoryFilterSeverity<uint32_t V>`: Enables all messages with :code:`category >= V`.

Continuing the above example, if we want to disable all messages that are not errors (fatal or otherwise), we can use
the severity filter like this:

.. code-block:: cpp

    using log_t = lal::Log<lal::CategoryFilterSeverity<error>, ...>;

Implementing your own filter is trivial. Just take a look at the existing types and the constraint.
