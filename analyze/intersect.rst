Intersect
=========

If you have multiple :code:`lal::Tree` objects they can be intersected to enable only those nodes which are enabled in
either or both of them. This is done using :code:`operator|` and :code:`operator&`, which just follow the typical rules
of logical operators.

.. code-block:: cpp

    lal::Analyzer analyzer;
    ...
    lal::Tree treeA(analyzer);
    lal::Tree treeB(analyzer);
    ...

    treeA |= treeB;
    treeA &= treeB;

:code:`operator|` visualized:

.. figure:: /_static/images/analyze/intersect_or.svg

:code:`operator&` visualized:

.. figure:: /_static/images/analyze/intersect_and.svg
