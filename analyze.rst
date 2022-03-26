Analyze
=======

.. toctree::
    :maxdepth: 3
    :titlesonly:
    :hidden:

    analyze/filter
    analyze/expand_reduce
    analyze/intersect

For automated analysis of your log files, there is the :code:`lal::Analyzer` class. This object will read a log file and
construct a traversable tree on which you can perform all sorts of operations. Before expanding on that, creating an
analyzer follows a familiar pattern. Just as with formatting, you must register all dynamic parameter types that you
logged (except for integer and floating point types, those are again registered by default). After that a log file can
be read:

.. code-block:: cpp

    // A custom type used during logging.
    struct float3
    {
        float x = 0;
        float y = 0;
        float z = 0;
    };
    ...

    lal::Analyzer analyzer;

    // Register all parameters before reading a log file.
    analyzer.registerParameter<float3>();
    ...

    // Then read the log file.
    analyzer.read("log.bin");

The Log Tree
------------

A full log is essentially just a basic tree. This tree consists of 4 distinct types of nodes:

1. **Log**: This is the root of the tree, and mainly just ties things together. Obviously, there's only one of these.
2. **Stream**: These are the direct children of the log node. There is one for each stream that you created during
   logging.
3. **Region**: A region can be a direct child of a stream node, or it can be found below another region node. It does
   not contain any data, other than the name of the region (if it is not anonymous). Their children are all region and
   message nodes that were logged in between construction and destruction of the region object.
4. **Message**: The most important nodes. These contain the actual data you logged.

Traversal of the tree is not done directly through the analyzer, but with a :code:`lal::Tree` object. For each node in
the analyzer, it has a simple flag indicating whether the node is currently enabled. These flags can be modified using
various operations. You can create any number of trees from the same analyzer, and combine their results for more
advanced operations:

.. code-block:: cpp

    lal::Tree treeA(analyzer);
    lal::Tree treeB(analyzer);

    // Do operations on trees.
    ...

An analyzer plus tree could be visualized as below:

.. figure:: /_static/images/analyze/tree.svg
