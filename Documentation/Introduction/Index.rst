.. ==================================================
.. FOR YOUR INFORMATION
.. --------------------------------------------------
.. -*- coding: utf-8 -*- with BOM.

.. include:: ../Includes.txt


.. _introduction:

Introduction
============


This extension makes it easier to get a set of records from the TYPO3 database, properly overlaid
for sites with multiple languages. Furthermore it provides in a central place a set of tools related
to database calls that are sometimes spread over several classes in the TYPO3 architecture. Versions
and workspaces are also supported, as well as language overlays in foreign tables.

It also aims to reduce the number of database calls, at least for language overlays. In a scenario
where many records are fetched and overlaid the number of requests to the database will be much
smaller, albeit at the cost of increased PHP processing (there's no free lunch). For version
overlays, extension "overlays" still relies on the TYPO3 Core method
:code:`\TYPO3\CMS\Frontend\Page\PageRepository::versionOL()`.

There are limitations to what can be achieved with a simple library. Please read the
:ref:`Limitations <limitations>` chapter carefully.


.. _introduction-questions:

Questions and support
---------------------

If you have any questions about this extension, please ask them in the TYPO3 English mailing list,
so that others can benefit from the answers. Please use the bug tracker on forge.typo3.org to report
problem or suggest features (http://forge.typo3.org/projects/extension-overlays/issues).


.. _introduction-credits:

Credits
-------

Most of the overlay code itself comes from the TYPO3 CMS core and was not developed by myself. It is
encapsulated in various methods for easier use.


.. _introduction-happy-developer:

Keeping the developer happy
---------------------------

Every encouragement keeps the developer ticking, so don't hesitate to send thanks or share your
enthusiasm about the extension.

If you appreciate this work and want to show some support, please check
http://www.monpetitcoin.com/en/francois/support-me/.
