Expand and Reduce
=================

Consider a situation where you want to analyze all error messages in order to catch a nasty bug. You disable all
messages with a non-error category. Then you notice you are still missing some pretty vital information, since just the
error messages don't tell you enough. Viewing some surrounding messages could provide some hints as to what is going
wrong. For that, there is the :code:`expand` operation. It will enable previously disabled nodes if they have a sibling
that is enabled:

.. code-block:: cpp

    lal::Tree tree(...);
    ...

    // Enable any node that has an enabled node at most 3 spots to the left, or 1 to the right.
    tree.expand(3, 1);

Note that this expansion does not cross region or stream boundaries. Below an example where there are 8 nodes in a
group, and the :code:`left/right` value that would be required to enable the disabled nodes. The second row of nodes has
the expansion shown above applied:

.. figure:: /_static/images/analyze/expand.svg

You can also do the reverse using the :code:`reduce` method, where nodes are disabled if they are not surrounded by
enough other nodes that are also enabled:

.. code-block:: cpp

    // Disable any node that has a disabled node at most 2 spots to the left, or 1 to the right.
    tree.reduce(2, 1);

Mind that the reduce operation is not the exact inverse of the expand operation. Below an example:

.. figure:: /_static/images/analyze/reduce.svg
