Message Ordering
================

The :code:`lal::Formatter` class has an :code:`indexFormatter` member variable that holds a function to write the index
of a message. By default it will combine the :code:`indexPaddingWidth` and :code:`indexPaddingCharacter` with the index
to write a padded integer. If you have two streams that write messages in a certain order, the output may look as
follows:

.. code-block:: cpp

    // log_0.txt
    00000000 | <category> | <message>
    00000001 | <category> | <message>
    00000005 | <category> | <message>
    00000006 | <category> | <message>

    // log_1.txt
    00000002 | <category> | <message>
    00000003 | <category> | <message>
    00000004 | <category> | <message>
    00000007 | <category> | <message>

The width and character can be changed and the function will automatically use the new values. You can also replace the
function in its entirety.
