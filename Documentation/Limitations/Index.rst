.. ==================================================
.. FOR YOUR INFORMATION
.. --------------------------------------------------
.. -*- coding: utf-8 -*- with BOM.

.. include:: ../Includes.txt


.. _limitations:

Limitations
===========

TYPO3's overlay architecture makes it very difficult to provide a full-fledged API for some
operations without building a complete SQL parser on top (which is essentially what the "dataquery"
extension of the Tesseract project does).


.. _limitations-table-joins:

Table joins
-----------

In :code:`\TYPO3\CMS\Core\Database\DatabaseConnection::exec_SELECTquery()`
it is perfectly okay to make joins by setting several table names in
the :code:`$from_table` parameter. Example:

.. code-block:: sql

    $result = \TYPO3\CMS\Core\Database\DatabaseConnection::exec_SELECTquery('*', 'pages, tt_content', 'tt_content.pid = pages.uid');


This will not work correctly for overlays. Indeed tables must be overlaid individually. So in order
to have joined tables overlaid, it would be necessary to separate the result of the query into two
tables again, overlay each part and join them again.

The suggestion that can be made here is to select records from both tables separately using
tx_overlays::getAllRecordsForTable() and perform the join in your PHP code.


.. _limitations-text-search:

Text search
-----------

Imagine trying to select records based on a LIKE condition place on a text field. Imagine the
following records:

=====  ===========  ================  ===========================  =====  =====
uid    l18n_parent  sys_language_uid  title                        ...
-----  -----------  ----------------  ---------------------------  -----  -----
1      0            0                 Weather forecast for Geneva  ...
-----  -----------  ----------------  ---------------------------  -----  -----
2      1            3                 Wetterbericht für Genf       ...
=====  ===========  ================  ===========================  =====  =====

Now let's say we want to make a text-based search on the title. We might write something like:

.. code-block:: php

    $records = \Cobweb\Overlays\OverlayEngine::getAllRecordsForTable('uid, title', 'tt_news', "title LIKE '%" . $searchWord . "%'");


($searchWords would have been properly escaped first, of course). Since extension "overlays" aims to
follow the TYPO3-way of doing overlays it will automatically select only records in the default
language and overlay them afterwards. This means that the above query will work fine if you search
for "geneva", but will not work if you search for "genf", because that word does not appear in the
default language. The result of the query is thus wrong: there is a record that would match the
condition, but it won't be found because it's not in the default language.

On the other hand if you tried to select directly in the right language (using
:code:`\TYPO3\CMS\Core\Database\DatabaseConnection::exec_SELECTquery()`), you will miss some information because
some fields in "tt_news" are not editable in the translations.

There's no simple solution to this conundrum. One might be to select records in the requested
language first, use these records' "parent_id" information to get the records in the default
language, and then overlay the latter with the former. This is obviously a lot of overhead. Not to
mention what a nightmare it becomes if you add the dimension of version overlays.

A better solution is probably to avoid such text-based searches at all and use search engines in
that case (Apache Solr, Google, etc.), although that will probably not work for workspace preview,
except if you can manage to index workspace versions. Then again it's probably unnecessary to be
able to correctly search in workspaces in most cases.

All that is described above is not an issue if you test numeric fields, such as simple flags, dates,
etc.
