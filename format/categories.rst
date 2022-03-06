Categories
==========

The :code:`lal::Formatter` class has a :code:`categoryFormatter` member variable that holds a function to write the
category of a message. By default it will just write the category as an integer plus :code:`|` separator:

.. code-block:: cpp

    0 | <message>
    2 | <message>
    0 | <message>
    1 | <message>

In the likely case that categories aren't just simple numbers but have an actual meaning, you can override the function:

.. code-block:: cpp

    static constexpr uint32_t    info = 0;
    static constexpr uint32_t warning = 1;
    static constexpr uint32_t   error = 2;
    static constexpr uint32_t   fatal = 3;

    auto formatter = lal::Formatter();

    formatter.categoryFormatter = [](std::ostream& out, const uint32_t category) {
        switch (category)
        {
        case    info: out << "INFO    | "; break;
        case warning: out << "WARNING | "; break;
        case   error: out << "ERROR   | "; break;
        case   fatal: out << "FATAL   | "; break;
        default:      out << "?       | "; break;
        }
    };

Formatted messages will then be slightly more informative:

.. code-block:: cpp

    INFO    | <message>
    ERROR   | <message>
    INFO    | <message>
    WARNING | <message>
