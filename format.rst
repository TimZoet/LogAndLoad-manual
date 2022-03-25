Format
======

.. toctree::
    :maxdepth: 3
    :titlesonly:
    :hidden:

    format/categories
    format/params
    format/order
    format/regions
    format/source_info

To convert the binary data in a log file to human readable text, you need an instance of the :code:`lal::Formatter`
class. This object has a number of settings that can be configured to change how the data is formatted exactly. With a
formatter ready to go, you can make it format a log file using the :code:`format` method, which takes nothing more than
a path to the file:

.. code-block:: cpp

    // Create and configure a formatter.
    auto formatter = lal::Formatter();
    ...

    // Read and format a binary log file.
    formatter.format("log.bin");

Each stream in the log is written to a separate file. By default all output files are given the name of the log file
plus the index of the stream. In the above example, you'd get files named :code:`log_0.txt`, :code:`log_1.txt`, etc. To
change this, you can assign your own formatting function to the :code:`filenameFormatter` member:

.. code-block:: cpp

    formatter.filenameFormatter = [](const std::filesystem::path& path,
                                     const size_t                 index) -> std::filesystem::path {
        return std::to_string(index) + "_stream.log";
    };

Further information on how to customize the formatting of categories, dynamic parameter types, regions, etc. can be
found on the various other pages in this section.
