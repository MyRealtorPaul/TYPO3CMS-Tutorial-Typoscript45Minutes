.. include:: ../../Includes.txt


.. _typoscript-array:

TypoScript is just an array
^^^^^^^^^^^^^^^^^^^^^^^^^^^

Internally, TypoScript is parsed and stored as a PHP array.
For example:

.. code-block:: typoscript

   page = PAGE
   page.10 = TEXT
   page.10.value = Hello World
   page.10.stdWrap.wrap = <h2>|</h2>

will be converted to the following PHP array:

.. code-block:: php

   $data['page'] = 'PAGE';
   $data['page.']['10'] = 'TEXT';
   $data['page.']['10.']['value'] = 'Hello World';
   $data['page.']['10.']['stdWrap.']['wrap'] = '<h2>|</h2>';

Upon evaluation, a ":ref:`PAGE <t3tsref:page>`" object will be created
first, and the parameter `$data['page.']` will be assigned to it.
The ":ref:`PAGE <t3tsref:page>`" object will then search for all properties, which
it knows about. In this case, it will find a numeric entry ("10"), which
has to be evaluated. A new object of type ":ref:`TEXT <t3tsref:cobj-text>`"
with the parameter `$data['page.']['10.']` will be created.
The ":ref:`TEXT <t3tsref:cobj-text>`" object knows the parameters `value` and
`stdWrap`. It will set the content of `value` accordingly. The
parameters from `stdWrap` will be passed to the ":ref:`stdWrap <t3tsref:stdwrap>`" function.
There the property 'wrap' is known, and the text "Hello world" will be inserted
at the pipe (:code:`|`) position and returned.

It is important to be aware of this relationship in order to
understand the behaviour of TypoScript.
For example, if the above TypoScript is extended
with the following line:

.. code-block:: typoscript

   page.10.myFunction = Magic!

the following entry will be added to the PHP array:

.. code-block:: php

   $data['page.']['10.']['myFunction'] = 'Magic!';

However, the ":ref:`TEXT <t3tsref:cobj-text>`" object does not know
any property called "myFunction". Consequently, the entry will have no effect.

.. important::

   No semantic error checking is done. If you define objects or
   properties which do not exist, you will not see any error message.
   Instead, those specific lines of TypoScript simply do nothing. This
   should be considered, especially while troubleshooting.
