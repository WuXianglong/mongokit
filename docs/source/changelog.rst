Changelog
=========

Version 1.0.0
-------------
 * Added python 3 support (thanks to @aquavitae)
 * Big code clean up (thanks to @Winnetou)
 * Updated documentation
 * Added read the docs documentation
 * Improved documentation structure


Version 0.9.1
-------------

 * fixed #131 - Use PEP8 recommendation for import
 * fixed tests (thanks @JohnBrodie and @bneron)
 * Added a Makefile for running tests in venv (thanks to @gulbinas)
 * fixed pep8 error (thanks to @gulbinas)
 * added support for MongoReplicaSetClient (thanks to @inabhi9)
 * Added `__getstate__` and `__setstate__` to DotedDict and i18nDotedDict. Problems appeared here when pickling mongokit documents due to apparent lack of these functions. (thanks to @petersng)
 * Fixed english mistake and typos into the documentation (thanks to @biow0lf, @SeyZ, @gianpaj and @1123)
 * Fixed inherited queries when accessing cursor by index (thanks to @asivokon)
 * changed the namespace on schema document errors (thanks to @rtjoseph11)

Version 0.9.0
-------------

 * now MongoKit required PyMongo >= 2.5
 * find_and_modify returns None if the query fails (thanks to @a1gucis)
 * Fix off-by-one error on SchemaDocument (thanks to @John Brodie)
 * Fix inherited queries (issue #106) (thanks to @effem-git)
 * Fix for serialization of nested structures with type validation (thanks to @LK4D4)
 * Remove unnecessary path arguments in to_json._convert_to_python (thanks to @Alexandr Morozov)
 * big refactorization by using multiple inheritance for DRYness (thanks to @liyanchang)
 * Add find_fulltext method for convenience (thanks to @astronouth7303) (not official and not documented yet)
 * Allow text indexes in document definitions (thanks to @astronouth7303)
 * Adding replica set support (thanks to @liyanchang)
 * Fix typos on README (thanks to @girasquid)
 * add pagination helper (not yed documented)(thanks to @jarrodb) https://github.com/namlook/mongokit/blob/master/mongokit/paginator.py

Version 0.8.3
-------------

 * allow keyword arguments (like read_preferences, slave_okay, etc) to be set in Connection (thanks to Peter S Ng)
 * Add find_and_modify again. It was removed by an unexpected rollback.
 * use MongoClient with MasterSlaveConnection

Version 0.8.2
-------------

 * fix #101 - validators condition fix
 * fix #110 - support PyMongo >= 2.4 (import MongoClient) -- thanks to @mattbodman and @zavatskiy
 * Fixed some spelling/grammar (thanks to @gekitsuu)

Version 0.8.1
-------------

 * support pymongo 2.3
 * small updates to validation messages (Merge pull request #94 from unpluggd/master)
 * Fixed typo when throwing MaxDocumentSizeError in validate() (thanks to Andy Pavlo)
 * added fix for unconditional access to __wrap on cursors (thanks to David T. Lehmann)
 * Add .travis.yml for Travis CI (http://travis-ci.org/) (Marc Abramowitz)
 * Fixed rendering issue in the docs. (thanks to Dougal Matthews)
 * tweaked the error messages in validation for missing and unknown fields to aid in debugging projects (thanks to Phillip B Oldham)

Version 0.8.0
-------------

 * Add spec file for rpm-based distributions (Merge pull request #63 from linuxnow/master)
 * change document size limitation for mongodb 1.8 or later. Thanks to Aleksey Sivokon (Merge pull request #74 from key/master)
 * validation of "" for an int (Merge pull request #79 from barnybug/master)
 * Fix exception when loading documents with a custom type field missing (Merge pull request #80 from barnybug/master)
 * Big documentation restructuration made by Sean Lynch (Merge pull request #82 from sean-lynch/master)
 * Using rename no longer causes migrations throw an exception (Merge pull request #86 from matthewh/master)
 * Some test is modified and added tox (Merge pull request #91 from aircastle/modifiytest)
 * Replace pymongo.objectid with bson.objectid (Merge pull request #88 from behackett/master)
 * Added Support for additional keyword-arguments for index-creation (Merge pull request #85 from mfelsche/master)
 * Remove anyjson dependency and use builtin json instead

Thank you all for all your patches !

Version 0.7.2
-------------

 * add inherited queries support (please see http://github.com/namlook/mongokit/wiki/Inherited-queries for more details)

Version 0.7.1
-------------

 * change MongokitMasterSlaveConnection to MasterSlaveConnection for consistency
 * fix #57 -- support pymongo > 1.9 in grid.py
 * fix #45 -- remove automatic index creation
 * fix #43 -- slicing a cursor should return a mongokit document, not dict
 * Don't try to convert None struct to json (patch from @mLewisLogic thanks !)
 * fix schemaless issue (thanks to Mihai Pocorschi for reporting it)

Version 0.7
-----------

 * add `use_schemaless` feature ! please see the documentation for more information
 * Add equality test for mongokit operators (thanks to @allancaffee)
    This allows developers to write unit tests on the structure
    of their document classes when operators are used
 * roll back find_and_modify for master branch (need pymongo 1.10 for that)
 * many documentation fixes
 * fix #55 -- Bug in VersionedDocument remove() method
 * fix #53 -- Fixed a few spelling errors in README
 * fix #52 -- Advanced bulk migration docs example is broken
 * fix #51 -- pymongo.dbref is deprecated, use bson.dbref instead
 * fix #49 -- Can't specify default values for lists of embedded objects
 * fix #48 -- uuid.UUID support
 * fix #41 -- add basestring to authorized types
 * fix #40 -- Made some enhancements
 * fix #39 -- KeyError when saving partially loaded documents
 * fix #34 -- add find_and_modify method to Document
 * fix #32 -- allow the structure to be empty (was: document.to_json())
 * fix #24 -- Don't handle __database__/__collection__ attribute for virtual documents

Version 0.6
-----------

 * fix error when check is True. Thanks to @dasmith for the patch
 * Many english corrections in the documentation thanks to @allancaffee
 * spliting doc and refactoring documentation
 * remove unused MongoDocumentCursor

Version 0.5.13.1
----------------

 * fix #26 -- unable to install (debian lenny, py 2.5)
 * fix #25 -- put the new url into the setup.py

Version 0.5.13
--------------

 * fix #21 -- required_fields weird behavior with autorefs
 * fix #19 -- 'checked' field not listed in 'indexes' section
 * fix #20 -- creating index on fields not in structure + optimize index generation
 * fix #18 -- typo in the doc
 * fix import. Dbref isn't in pymongo package anymore
 * fix deprecation warning from pymongo's from_dict
 * fix #8 -- allow to access Document via the db

Version 0.5.12.1
----------------

 * fix #17 -- got an unexpected keyword argument 'from_son'
 * fix #15 -- typo in the doc

Version 0.5.12
--------------

 * allow register method to be a decorator (thanks to Christopher Grebs for the inspiration)
 * get ride of MongoDocumentCursor and use a subclass of pymongo's Cursor instead
 * structure and descriptors validation is now done at object creation (not instantiation)

   - *advantage* : mongokit is 40% faster
   - *beware* : if you put a Document into a structure for reference, mongokit doesn't check anymore if use_autorefs is set

 * add i18n descriptor validation + better i18n support
 * code cleaning

Version 0.5.11
--------------

 * support latest pymongo version
 * some changes in GridFS support (please read http://namlook.github.com/mongokit/gridfs.html)
 * Deprecate atomic_save feature
 * remove libmagic import from grid.py : to many trouble with this lib, we have to find another way to guess the content-type
 * fix #79 -- Tries to migrate non-saved document
 * fix #70 -- Set changes from set to list when a validation error occurs
 * add contributor + fix email address to prevent spam
 * fix deprecation warning of Python 2.6
 * fix issue with validation and migration
 * fix #75 -- add "version" attribute to module

Version 0.5.10
--------------

 * fix bug in autorefs when reference in double list

Version 0.5.9
-------------

 * minors fixes

Version 0.5.8
-------------

 * add rewind to cursor
 * killed many bug in `from_json()`
 * fix #66 - local variable 'l_objs' referenced before assignment
 * fix #61 - Issue with indexing on multi-columns with directions

Version 0.5.7
-------------

 * fix #63 - Creating index for each document instance operation. Brings speed improvements
 * fix #60 - autorefs doesn't work with complex structures
 * fix #62 - Dereference to model. Thanks to Christian Joudrey for the patch
 * fix #64 - error with atomic_save when using embed document
 * fix #65 - Lazy migrations with dict in list and documentation fix

Version 0.5.6
-------------

 * add `atomic update`_ (just save the document again)
 * add `init_type`_ to CustomType. This allow to fill an empty skeleton at instanciation
 * add debian package build rules. Thanks to Sebastien Estienne
 * add `lazy migration`_ and `bulk migration`_ support
 * fix a bug in CustomType
 * add `'check' option`_ in indexes descriptor
 * add `untyped field support`_
 * fix #58 - Document Validators not working for CustomType
 * improve DotCollapsedDict by adding reference structure


.. _`atomic update` : http://namlook.github.com/mongokit/tutorial.html#updating-data
.. _`init_type` : http://namlook.github.com/mongokit/tutorial.html#custom-types
.. _`lazy migration` : http://namlook.github.com/mongokit/migration.html
.. _`bulk migration` : http://namlook.github.com/mongokit/migration.html#bulk-migration
.. _`'check' option` : http://namlook.github.com/mongokit/tutorial.html#indexes
.. _`untyped field support` : http://namlook.github.com/mongokit/tutorial.html#untyped-field


Version 0.5.5
-------------
 * fix 54 - Add reload method. Please read the documentation_
 * put generate_index into Document.__init__. This is useful for instanciating Document like this : MyDoc(collection=mycol)
 * fix #44 - add  `set type support`_ + add validate() method to CustomType_
 * fix #52 - Custom validation error messages (thanks to @cjoudrey for the patch)
 * fix #50 - need optimizations in connection (won 20s on the benchmark_)
 * fix #48 - Tuple assignment does not convert to list
 * fix 49 - KeyError when using deep nested autorefs

.. _documentation : http://namlook.github.com/mongokit/tutorial.html#reload
.. _`set type support` : http://namlook.github.com/mongokit/tutorial.html#the-structure
.. _CustomType : http://namlook.github.com/mongokit/tutorial.html#custom-types
.. _benchmark : http://groups.google.com/group/mongodb-user/browse_thread/thread/77c91641858e1e20/ea71afc4de24f2e9

Version 0.5.4
-------------
 * A `lot of features in GridFS`_ with api change
 * fix bug in autorefs
 * fix #37 - find_random crash if no collection is empty
 * fix #38 - OverflowError in doc.to_json_type() when used over the datetime 2038
 * fix #41 - Warnings when setting attributes before enabling use_dot_notation
 * fix #40 - Better exception on bad structure. Thanks to peterbe for the patch
 * fix #43 - Add ability to `collect errors`_ in one place instead of throwing exceptions while validating
 * add `_dot_notation_warning` attribute. If false, disable all dot notation related warning
 * add patch to enable data load from map/reduce. See http://groups.google.com/group/mongokit/msg/34efea4c178573d7
 * fix bug spotted by Sebastien Estienne - error when using skip_validation with required_fields. Thanks
 * fix issue while using {unicode:unicode} in structure and i18n at the same time

.. _`lot of features in GridFS` : http://namlook.github.com/mongokit/gridfs.html
.. _`collect errors` : http://namlook.github.com/mongokit/tutorial.html#quiet-validation-detection

Version 0.5.3
-------------
 * fix default_value issue when using with dict and list (see #35)
 * fix bug reported by Andrew Degtiariov : http://bit.ly/c1vcUv
 * add clone and explain method to MongoDocumentCursor
 * add distinct to cursor (thanks to Flaper87)
 * fix index test
 * fix : when a field is added to a saved document and not specified in the structure, the validation wasn't work properly
 * use current database if DBRef has no database information. Please, see the doc
 * support of pymongo 1.4


Version 0.5.2
-------------
 * bugs fix in json import/export
 * bugs fix in default values and required values
 * `gridfs support`_

.. _`gridfs support` : http://namlook.github.com/mongokit/gridfs.html

Version 0.5.1
-------------
 * `save()` doesn't return ``self`` anymore (was an API monster)
 * fix bug in `find_one()` method. Now returns None if no Document is found
 * fix bug when using default values
 * adding i18n list support
 * add i18n inheritance support
 * adding index inheritance support

Version 0.5
-----------
 * refactoring API which is getting much much more cleaner. Please see the migration_ page to keep your code up to date
 * 100% code coverage by 162 unit tests
 * lot of bug fix (too many to list them here)
 * add document size validation
 * add cross database reference support
 * `i18n support`_

.. _migration : http://namlook.github.com/mongokit/migration.html
.. _`i18n support` : http://namlook.github.com/mongokit/i18n.html

Version 0.4
-----------
    * add autoref support to belong_to (delete cascade)
    * changing collection dynamically
    * add immutable field (python tuple support)
    * add direction and ttl to index support
    * add connection sharing support
    * add json import/export for MongoDocument
    * full relation support (related_to)
    * add long type support

Version 0.3.3
-------------

   * add autoref support (thanks to @bwmcadams)
   * add mongodb index support (thanks to @marcammann)
   * adding CustomType (original idea from Phillip Oldham)
   * support now all type of subclassed supported type
   * add "delete cascade" feature
   * add the possibility to skip the validation layer for more performances
   * fix issue while passing queries to fetch() and update tutorial
   * self._collection must not be None in __init__
   * fix #11 - pylons_env extension documentation typo
   * add more complete test + docstring
   * fix issue #9 - bug with custom_types and nested dict in list