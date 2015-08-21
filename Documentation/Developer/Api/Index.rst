.. ==================================================
.. FOR YOUR INFORMATION
.. --------------------------------------------------
.. -*- coding: utf-8 -*- with BOM.

.. include:: ../../Includes.txt


.. _developers-api:

The full API
------------

On top of the method demonstrated above, class :code:`\Cobweb\Overlays\OverlayEngine` provides a full toolbox related to
getting overlaid records in the TYPO3 CMS frontend. This API is described below.

:code:`\Cobweb\Overlays\OverlayEngine` is a fully static class. As such it cannot be XCLASSed.


.. _developers-api-getallrecordsfortable:

getAllRecordsForTable
~~~~~~~~~~~~~~~~~~~~~

This method returns an array with all records properly
overlaid with their translations and filtered according
to the table's enable fields.

.. note::

   This will not work properly using implicit joins
   (i.e. :code:`SELECT * FROM foo, bar ...`) as neither enable
   fields, nor translations, nor versions will be cleanly
   applied to both tables.

This method takes the following parameters:

$selectFields (string)
  Comma-separated list of fields to select from the table.

$fromTable (string)
  Table from which to select.

$whereClause (string)
  Additional WHERE clause for the query. Enable fields are handled automatically.
  Optional, defaults to empty string.

$groupBy (string)
  GROUP BY field(s).
  Optional, defaults to empty string.

$orderBy (string)
  ORDER BY field(s).
  Optional, defaults to empty string.

$limit (string)
  LIMIT value ([begin,]max).
  Optional, defaults to empty string.

$indexField (string)
  Optional, name of a field to use as an index for the result array.
  **Must be included in the list of select fields.**


.. _developers-api-getsinglerecordfortable:

getSingleRecordForTable
~~~~~~~~~~~~~~~~~~~~~~~

Similar to getAllRecordsForTable(), but returns a
single record by automatically applying a limit of 1 to
the underlying SQL query.

This method takes the following parameters:

$selectFields (string)
  Comma-separated list of fields to select from the table.

$fromTable (string)
  Table from which to select.

$whereClause (string)
  Additional WHERE clause for the query. Enable fields are handled automatically.
  Optional, defaults to empty string.

$groupBy (string)
  GROUP BY field(s).
  Optional, defaults to empty string.

$orderBy (string)
  ORDER BY field(s).
  Optional, defaults to empty string.


.. _developers-api-getenabledfieldscondition:

getEnabledFieldsCondition
~~~~~~~~~~~~~~~~~~~~~~~~~

This method returns the complete SQL condition needed
to respect all enable fields as defined in the TCA of
the table. Essentially this method is a wrapper around
:code:`\TYPO3\CMS\Frontend\Page\PageRepository::enableFields()`.
The only difference is that it removes the " AND " at the beginning of the
condition, so that it's not necessary to prepare for
such condition by adding things like "1=1" to your
SQL statements.

For more details look at what happens inside
:code:`\TYPO3\CMS\Frontend\Page\PageRepository::enableFields()`.

This method takes the following parameters:

$table (string)
  Name of the table to build the condition for.

$showHidden (boolean)
  Set to TRUE to force the display of hidden records.

$ignoreArray (array)
  Defines which enable fields to ignore. Use keys like "disabled", "starttime",
  "endtime", "fe_group" (i.e. keys from "enablecolumns" in TCA) and set values
  to TRUE to exclude corresponding conditions from WHERE clause.


.. _developers-api-getlanguagecondition:

getLanguageCondition
~~~~~~~~~~~~~~~~~~~~

This method returns the SQL condition necessary to
select the correct records with records to their
language, based on the table's TCA and the currently
selected language in the FE.

This method takes the following parameters:

$table (string)
  Name of the table to build the condition for.

$alias (string)
  Alias of the table to be used in the SQL condition.


.. _developers-api-getversioningcondition:

getVersioningCondition
~~~~~~~~~~~~~~~~~~~~~~

This method assembles the SQL condition necessary to
select the correct records for the live web site or for
the current workspace.

This method takes the following parameters:

$table (string)
  Name of the table to build the condition for.

$alias (string)
  Alias of the table to be used in the SQL condition.

$getOverlaysDirectly (boolean)
  Set to TRUE to directly get the overlays, instead of the placeholders
  to overlay (check the method's comments in the code for
  a deeper discussion). Leave the default value of FALSE if unsure.


.. _developers-api-getallfieldsfortable:

getAllFieldsForTable
~~~~~~~~~~~~~~~~~~~~

This method returns the list of all fields for a given
table (based on querying the database, not analyzing
the TCA).

This method takes the following parameters:

$table (string)
  Name of the table to get the fields for.


.. _developers-api-selectbasefields:

selectBaseFields
~~~~~~~~~~~~~~~~

This method makes sure that the query will include a
number of base fields necessary for the overlay
process. This means the uid and pid fields.

This method takes the following parameters:

$table (string)
  Table from which to select.

$selectFields (string)
  List of fields to select from the table.


.. _developers-api-selectoverlaysfields:

selectOverlaysFields
~~~~~~~~~~~~~~~~~~~~

This method makes sure that the query will include all
necessary fields for the **language** overlay process.
It returns the original list of selected fields plus
any other necessary fields that were missing.

This method takes the following parameters:

$table (string)
  Table from which to select.

$selectFields (string)
  List of fields to select from the table.


.. _developers-api-selectoverlaysfieldsarray:

selectOverlayFieldsArray
~~~~~~~~~~~~~~~~~~~~~~~~

This method returns an array containing the list of all
fields that should be added to the current list of
selected fields, in order to have all the necessary
information for the **language** overlay process.

This method takes the following parameters:

$table (string)
  Table from which to select.

$selectFields (string)
  List of fields to select from the table.


.. _developers-api-selectversioningfields:

selectVersioningFields
~~~~~~~~~~~~~~~~~~~~~~

This method makes sure that the query will include all
necessary fields for the **version** overlay process.
It returns the original list of selected fields plus
any other necessary fields that were missing.

This method takes the following parameters:

$table (string)
  Table from which to select.

$selectFields (string)
  List of fields to select from the table.


.. _developers-api-selectversioningfieldsarray:

selectVersioningFieldsArray
~~~~~~~~~~~~~~~~~~~~~~~~~~~

This method returns an array containing the list of al
fields that should be added to the current list of
selected fields, in order to have all the necessary
information for the **version** overlay process.

This method takes the following parameters:

$table (string)
  Table from which to select.

$selectFields (string)
  List of fields to select from the table.


.. _developers-api-overlayrecordset:

overlayRecordSet
~~~~~~~~~~~~~~~~

This method performs both the translation and
versioning overlays on the given recordset, according
to the relevant TCA settings.

.. note::
   If overlay mode is "hideNonTranslated", the
   overlaid recordset may contain less records than the
   original one, if some records were missing a
   translation.

This method takes the following parameters:

$table (string)
  Table name

$recordset (array)
  Full recordset to overlay. Must contain uid, pid and
  $TCA[$table]['ctrl']['languageField'].

$currentLanguage (integer)
  Uid of the currently selected language in the FE.

$overlayMode (string)
  Overlay mode. If "hideNonTranslated" then records without translation
  will not be returned un-translated but removed instead.

$doVersioning (boolean)
  Set to TRUE if version overlays must be performed.


.. _developers-api-getoverlayrecords:

getOverlayRecords
~~~~~~~~~~~~~~~~~

This method is a wrapper around
:code:`\Cobweb\Overlays\OverlayEngine::getLocalOverlayRecords()` and
:code:`\Cobweb\Overlays\OverlayEngine::getForeignOverlayRecords()`.
It will call the appropriate method depending on the translation
structure (i.e. translations in same table or in
foreign table). It returns the same result as these two
methods.

This method takes the following parameters:

$table (string)
  Name of the table for which to fetch the records.

$uids (array)
  Array of all uid's of the original records for which to fetch the translation.

$currentLanguage (integer)
  Uid of the system language to translate to.

$doVersioning (boolean)
  Set to TRUE if version overlays must be performed.


.. _developers-api-getlocaloverlayrecords:

getLocalOverlayRecords
~~~~~~~~~~~~~~~~~~~~~~

This method gets all translation overlay records for
the chosen language and for all records indicated by
the list of uid's. It will work for tables that contain
their own translations.

If workspace preview is active, the translation
overlays will have been properly version-overlaid.

It returns all overlay records organized by uid and pid
so that the overlay process can take place properly
afterwards.

This method takes the following parameters:

$table (string)
  Name of the table for which to fetch the records.

$uids (array)
  Array of all uid's of the original records for which to fetch the translation.

$currentLanguage (integer)
  Uid of the system language to translate to.

$doVersioning (boolean)
  Set to TRUE if version overlays must be performed.


.. _developers-api-getforeignoverlayrecords:

getForeignOverlayRecords
~~~~~~~~~~~~~~~~~~~~~~~~

This method gets all translation overlay records for
the chosen language and for all records indicated by
the list of uid's. It will work for tables that use a
foreign table to store their translations.

If workspace preview is active, the translation
overlays will have been properly version-overlaid.

It returns all overlay records organized by uid and pid
so that the overlay process can take place properly
afterward.

This method takes the following parameters:

$table (string)
  Name of the table for which to fetch the records.

$uids (array)
  Array of all uid's of the original records for which to fetch the translation.

$currentLanguage (integer)
  Uid of the system language to translate to.

$doVersioning (boolean)
  Set to TRUE if version overlays must be performed.


.. _developers-api-overlaysinglerecord:

overlaySingleRecord
~~~~~~~~~~~~~~~~~~~

This method actually performs the translation overlay
of a single record taking into account all fine-grained
translation settings at field level (i.e.
"l10n_mode"). It returns the properly overlaid
record.

This method takes the following parameters:

$table (string)
  Name of the table for which the operation is taking place.

$record (array)
  Record to overlay.

$overlay (array)
  Overlay of the record.
