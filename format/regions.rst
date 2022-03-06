Regions
=======

The :code:`lal::Formatter` class has two member variables that hold functions to write the start and end of a region: 
:code:`anonymousRegionFormatter` and :code:`namedRegionFormatter`. By default they result in the following:

.. code-block:: cpp

    -- REGION START: ANONYMOUS --
      <message>
      <message>
      -- REGION START: <name> --
        <message>
        <message>
      -- REGION END: <name> --
    -- REGION END: ANONYMOUS --

There are also the :code:`indexPaddingWidth` and :code:`indexPaddingCharacter` members. These control the indentation of
nested messages. They default to :code:`2` and :code:`' '`, respectively. All these functions and variables can be
overwritten:

.. code-block:: cpp

    auto formatter = lal::Formatter();

    formatter.anonymousRegionFormatter = [](std::ostream& out, const bool start) {
        if (start)
            out << "<region>";
        else
            out << "</region>";
    };

    formatter.namedRegionFormatter = [](std::ostream& out, const bool start, const std::string& name) {
        if (start)
            out << "<" << name << ">";
        else
            out << "</" << name << ">";
    };

    formatter.regionIndent          = 4;
    formatter.regionIndentCharacter = '.';

The above example would transform the output into the following:

.. code-block:: cpp

    <region>
    ....<message>
    ....<message>
    ....<regionname>
    ........<message>
    ........<message>
    ....</regionname>
    </region>
