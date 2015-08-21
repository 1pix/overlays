.. ==================================================
.. FOR YOUR INFORMATION
.. --------------------------------------------------
.. -*- coding: utf-8 -*- with BOM.

.. include:: ../../Includes.txt


.. _developer-overlay-records:

Getting overlaid records in a single call
-----------------------------------------

Using the "overlays" library the code in the previous chapter is reduced to the following:

.. code-block:: php

    $news = \Cobweb\Overlays\OverlayEngine::getAllRecordsForTable('title', 'tt_news');
    $content .= '<ul>';
    foreach ($news as $item) {
        $content .= '<li>' . $item['title'] . '</li>';
    }
    $content .= '</ul>';


The :code:`\Cobweb\Overlays\OverlayEngine::getAllRecordsForTable()`
method takes the same arguments as
:code:`\TYPO3\CMS\Core\Database\DatabaseConnection::exec_SELECTquery()`,
but takes care of all the enable fields and all the translation
process.

On top of this the "overlays" library is so structured that all translation overlays are gotten
in a single database request. The normal TYPO3 CMS process generates one query per record to overlay.
Thus "overlays" plays much nicer to your database. This comes at the price of some additional
PHP processing.
