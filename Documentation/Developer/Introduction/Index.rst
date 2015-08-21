.. ==================================================
.. FOR YOUR INFORMATION
.. --------------------------------------------------
.. -*- coding: utf-8 -*- with BOM.

.. include:: ../../Includes.txt


.. _developer-introduction:

Introduction
------------

Traditionally getting a set of records from the database in a TYPO3 application (typically a FE
plugin) is done by calling a method like :code:`\TYPO3\CMS\Core\Database\DatabaseConnection::exec_SELECTquery()`.
Then – if your site uses multiple languages and your records are localized using standard TYPO3 mechanisms – you have to
overlay each record with its translation by calling t3lib_page::getRecordOverlay(). It is also up to
you to check whether the translation succeeded or not. Here is how you code may look like, taking
"tt_news" as an example and doing a simple rendering with a bullet list:

.. code-block:: php

    // Restrict records to default language
    $languageCondition = $GLOBALS['TCA']['tt_news']['ctrl']['languageField'] . ' IN (0, -1)';
    // Also take records that exist only in chosen language
    if ($GLOBALS['TSFE']->sys_language_content > 0) {
        $languageCondition .= ' OR (tt_news.' . $GLOBALS['TCA']['tt_news']['ctrl']['languageField'] . " = '" . $GLOBALS['TSFE']->sys_language_content . "' AND tt_news." . $GLOBALS['TCA']['tt_news']['ctrl']['transOrigPointerField'] . " = '0')";
    }
    // Add enable fields check
    $where = '(' . $languageCondition . ') ' . $GLOBALS['TSFE']->sys_page->enableFields('tt_news');
    $news = $GLOBALS['TYPO3_DB']->exec_SELECTgetRows('uid, pid, title', 'tt_news', $where);

    $content = '<ul>';
    foreach ($news as $item) {
        // Perform overlay on each record
        $overlay = $GLOBALS['TSFE']->sys_page->getRecordOverlay('tt_news', $item, $GLOBALS['TSFE']->sys_language_content, $GLOBALS['TSFE']->sys_language_contentOL);
        // Check if record was not unset (in translation strict mode)
        if (isset($overlay)) {
            $content .= '<li>' . $overlay['title'] . '</li>';
        }
    }
    $content .= '</ul>';
