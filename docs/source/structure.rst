The Structure
-------------

The ``structure`` is a simple dict which defines the document's schema.

Field Types
~~~~~~~~~~~
Field types are simple python types. By default, MongoKit allows the following types::

    # Common types between python 3 and python 2

    None # Untyped field
    bool
    int
    float
    list
    dict
    datetime.datetime
    bson.binary.Binary
    pymongo.objectid.ObjectId
    bson.dbref.DBRef
    bson.code.Code
    type(re.compile(""))
    uuid.UUID
    CustomType

    # Python 3 types
    bytes
    str

    # Python 2 types
    basestring
    long
    unicode


Untyped field
~~~~~~~~~~~~~

Sometimes you don't want to specify a type for a field. In order to allow a
field to have any of the authorized types, just set the field to ``None`` in the
structure::

    class MyDoc(Document):
      structure = {
        'foo': int,
        'bar': None
      }
In this example, ``bar`` can be any of the above types except for a CustomType.

Nested Structure
~~~~~~~~~~~~~~~~

MongoDB allows documents to include nested structures using lists and dicts.
You can also use the structure dict to specify these nested structures as
well.

Dicts
^^^^^

Python's dict syntax ``{}`` is used for describing nested structure::

    # Python 3
    class Person(Document):
      structure = {
        'biography': {
          'name': str,
          'age': int
        }
      }

    # Python 2
    class Person(Document):
      structure = {
        'biography': {
          'name': basestring,
          'age': int
        }
      }

This validates that each document has an author dict which contains a string
name and an integer number of books.

If you want to nest a dict without type validation, you must use the dict type
keyword instead::


    class Person(Document):
      structure = {
        'biography': dict
      }

If you don't specify the nested structure or don't use the dict type keyword,
you won't be able to add values to the nested structure:

.. code-block:: python

    class Person(Document):
      structure = {
        'biography': {}
      }

.. code-block:: pycon

    >>> bob = Person()
    >>> bob['biography']['foo'] = 'bar'
    >>> bob.validate()
    Traceback (most recent call last):
    ...
    StructureError: unknown fields : ['foo']

Using dict type is useful if you don't know what fields will be added *or* what
types they will be. If you know the type of the field, it's better to
explicitly specify it::

    # Python 3
    class Person(Document):
      structure = {
        'biography': {
          unicode: str
        }
      }

    # Python 2
    class Person(Document):
      structure = {
        'biography': {
          unicode: unicode
        }
      }

This will add another layer to validate the content. See the :ref:`validate-keys`
section for more information.

Lists
^^^^^

The basic way to use a list is without validation of its contents::

    class Article(Document):
        structure = {
            'tags': list
        }

In this example, the ``tags`` value must be a list but the contents of
``tags`` can be anything at all. To validate the contents of a list, you
use Python's list syntax ``[]`` instead::

    # Python 3
    class Article(Document):
        structure = {
            'tags': [str]
        }

    # Python 2
    class Article(Document):
        structure = {
            'tags': [basestring]
        }

You can also validate an array of complex objects by using a dict::

    # Python 3
    class Article(Document):
        structure = {
            'tags': [
                {
                'name': str,
                'count': int
                }
            ]
        }

    # Python 2
    class Article(Document):
        structure = {
            'tags': [
                {
                'name': basestring,
                'count': int
                }
            ]
        }

Tuples
^^^^^^

If you need a structured list with a fixed number of items, you can use tuple
to describe it::

    # Python 3
    class MyDoc(Document):
        structure = {
            'book': (int, str, float)
        }

    # Python 2
    class MyDoc(Document):
        structure = {
            'book': (int, basestring, float)
        }

.. code-block:: pycon

    >>> con.register([MyDoc])
    >>> mydoc = tutorial.MyDoc()
    >>> mydoc['book']
    [None, None, None]

Tuple are converted into a simple list and add another validation layer. Fields
must follow the right type::

    >>> # Python 3
    >>> mydoc['book'] = ['foobar', 1, 1.0]
    >>> mydoc.validate()
    Traceback (most recent call last):
    ...
    SchemaTypeError: book must be an instance of int not str

    >>> # Python 2
    >>> mydoc['book'] = ['foobar', 1, 1.0]
    >>> mydoc.validate()
    Traceback (most recent call last):
    ...
    SchemaTypeError: book must be an instance of int not basestring

And they must have the right number of items::

    >>> mydoc['book'] = [1, 'foobar']
    >>> mydoc.validate()
    Traceback (most recent call last):
    ...
    SchemaTypeError: book must have 3 items not 2

As tuples are converted to list internally, you can make all list operations::

    >>> mydoc['book'] = [1, 'foobar', 3.2]
    >>> mydoc.validate()
    >>> mydoc['book'][0] = 50
    >>> mydoc.validate()

Sets
^^^^

The ``set`` python type is not supported in pymongo. If you want to use it
anyway, use the ``Set()`` custom type::

    # Python 3
    class MyDoc(Document):
      structure = {
        'tags': Set(str),
      }

    # Python 2
    class MyDoc(Document):
      structure = {
        'tags': Set(unicode),
      }

Using Custom Types
~~~~~~~~~~~~~~~~~~

Sometimes we need to work with complex objects while keeping
their footprint in the database fairly simple. Let's take a
datetime object. A datetime object can be useful to compute
complex date and though MongoDB can deal with datetime object,
we may just want to store its unicode representation.

MongoKit allows you to work on a datetime object and store the unicode
representation converted on the fly. In order to do this, we have to implement a CustomType
and fill the custom_types attributes:

>>> import datetime

A CustomType object must implement two methods and one attribute:

 -  ``to_bson(self, value)``: this method will convert the value to fit the
    correct authorized type before being saved in the db.
 -  ``to_python(self, value)``: this method will convert the value
    taken from the db into a python object
 -  ``validate(self, value, path)``: this method is optional and will add a
    validation layer. Please, see the `Set()` CustomType code for more example.
 -  You must specify a ``mongo_type`` property in the ``CustomType`` class. this
    will describe the type of the value stored in the mongodb.
 -  If you want more validation, you can specify a ``python_type`` property which is
    the python type the value will be converted to. It is a good thing to specify it
    as it make a good documentation.
 -  ``init_type`` attribute will allow to describe an empty value. For example,
    if you implement the python set as CustomType, you'll set ``init_type`` to
    ``Set``. Note that ``init_type`` must be a type or a callable instance.

::

    # Python 3
    class CustomDate(CustomType):
        mongo_type = str
        python_type = datetime.datetime # optional, just for more validation
        init_type = None # optional, fill the first empty value

        def to_bson(self, value):
            """convert type to a mongodb type"""
            return unicode(datetime.datetime.strftime(value,'%y-%m-%d'))

        def to_python(self, value):
            """convert type to a python object"""
            if value is not None:
               return datetime.datetime.strptime(value, '%y-%m-%d')

        def validate(self, value, path):
            """OPTIONAL : useful to add a validation layer"""
            if value is not None:
                pass # ... do something here

    # Python 2
    class CustomDate(CustomType):
        mongo_type = unicode
        python_type = datetime.datetime # optional, just for more validation
        init_type = None # optional, fill the first empty value

        def to_bson(self, value):
            """convert type to a mongodb type"""
            return unicode(datetime.datetime.strftime(value,'%y-%m-%d'))

        def to_python(self, value):
            """convert type to a python object"""
            if value is not None:
               return datetime.datetime.strptime(value, '%y-%m-%d')

        def validate(self, value, path):
            """OPTIONAL : useful to add a validation layer"""
            if value is not None:
                pass # ... do something here

Now, let's create a Document::

    class Foo(Document):
        structure = {
            'foo':{
                'date': CustomDate(),
            },
        }

Now, we can create Foo objects and work with python datetime objects::

>>> foo = Foo()
>>> foo['_id'] = 1
>>> foo['foo']['date'] = datetime.datetime(2003,2,1)
>>> foo.save()

The object saved in the db has the unicode footprint as expected:

>>> tutorial.find_one({'_id':1})
{u'_id': 1, u'foo': {u'date': u'03-02-01'}}

Querying an object will automatically convert the CustomType into the correct
python object:

>>> foo = tutorial.Foo.get_from_id(1)
>>> foo['foo']['date']
datetime.datetime(2003, 2, 1, 0, 0)

OR, NOT, and IS operators
~~~~~~~~~~~~~~~~~~~~~~~~~

You can also use boolean logic to do field type validation.

OR operator
^^^^^^^^^^^

Let's say that we have a field which can be either unicode, int or a float.
We can use the OR operator to tell MongoKit to validate the field :

>>> # Python 3
>>> from mongokit import OR
>>> from datetime import datetime
>>> class Account(Document):
...     structure = {
...         "balance": {'foo': OR(str, int, float)}
...     }
>>> # Validation
>>> con.register([Account])
>>> account = tutorial.Account()
>>> account['balance']['foo'] = '3.0'
>>> account.validate()
>>> account['balance']['foo'] = 3.0
>>> account.validate()
>>> # but
>>> account['balance']['foo'] = datetime.now()
>>> account.validate()
Traceback (most recent call last):
...
SchemaTypeError: balance.foo must be an instance of <str or int or float> not datetime

>>> # Python 2
>>> from mongokit import OR
>>> from datetime import datetime
>>> class Account(Document):
...     structure = {
...         "balance": {'foo': OR(unicode, int, float)}
...     }
>>> # Validation
>>> con.register([Account])
>>> account = tutorial.Account()
>>> account['balance']['foo'] = u'3.0'
>>> account.validate()
>>> account['balance']['foo'] = 3.0
>>> account.validate()
>>> # but
>>> account['balance']['foo'] = datetime.now()
>>> account.validate()
Traceback (most recent call last):
...
SchemaTypeError: balance.foo must be an instance of <unicode or int or float> not datetime

NOT operator
^^^^^^^^^^^^

You can also use the NOT operator to tell MongoKit that you don't want a given type
for a field :

    >>> # Python 3
    >>> from mongokit import NOT
    >>> class Account(Document):
    ...     structure = {
    ...         "balance": {'foo': NOT(str, datetime)}
    ...     }
    >>> # Validation
    >>> con.register([Account])
    >>> account = tutorial.Account()
    >>> account['balance']['foo'] = 3
    >>> account.validate()
    >>> account['balance']['foo'] = 3.0
    >>> account.validate()
    >>> # but
    >>> account['balance']['foo'] = datetime.now()
    >>> account.validate()
    Traceback (most recent call last):
    ...
    SchemaTypeError: balance.foo must be an instance of <not str, not datetime> not datetime
    >>> account['balance']['foo'] = u'3.0'
    >>> account.validate()
    Traceback (most recent call last):
    ...
    SchemaTypeError: balance.foo must be an instance of <not str, not datetime> not str

    >>> # Python 2
    >>> from mongokit import NOT
    >>> class Account(Document):
    ...     structure = {
    ...         "balance": {'foo': NOT(unicode, datetime)}
    ...     }
    >>> # Validation
    >>> con.register([Account])
    >>> account = tutorial.Account()
    >>> account['balance']['foo'] = 3
    >>> account.validate()
    >>> account['balance']['foo'] = 3.0
    >>> account.validate()
    >>> # but
    >>> account['balance']['foo'] = datetime.now()
    >>> account.validate()
    Traceback (most recent call last):
    ...
    SchemaTypeError: balance.foo must be an instance of <not unicode, not datetime> not datetime
    >>> account['balance']['foo'] = u'3.0'
    >>> account.validate()
    Traceback (most recent call last):
    ...
    SchemaTypeError: balance.foo must be an instance of <not unicode, not datetime> not unicode

IS operator
^^^^^^^^^^^

Sometimes you might want to define a field which accepts only values limited to a predefined
set. The IS operator can be used for this purpose::

    >>> # Python 3
    >>> from mongokit import IS
    >>> class Account(Document):
    ...     structure = {
    ...         "flag": {'foo': IS('spam', 'controversy', 'phishing')}
    ...     }
    >>> # Validation
    >>> con.register([Account])
    >>> account = tutorial.Account()
    >>> account['flag']['foo'] = 'spam'
    >>> account.validate()
    >>> account['flag']['foo'] = 'phishing'
    >>> account.validate()
    >>> # but
    >>> account['flag']['foo'] = 'foo'
    >>> account.validate()
    Traceback (most recent call last):
    ...
    SchemaTypeError: flag.foo must be in ['spam', 'controversy', 'phishing'] not foo

    >>> # Python 2
    >>> from mongokit import IS
    >>> class Account(Document):
    ...     structure = {
    ...         "flag": {'foo': IS(u'spam', u'controversy', u'phishing')}
    ...     }
    >>> # Validation
    >>> con.register([Account])
    >>> account = tutorial.Account()
    >>> account['flag']['foo'] = u'spam'
    >>> account.validate()
    >>> account['flag']['foo'] = u'phishing'
    >>> account.validate()
    >>> # but
    >>> account['flag']['foo'] = u'foo'
    >>> account.validate()
    Traceback (most recent call last):
    ...
    SchemaTypeError: flag.foo must be in [u'spam', u'controversy', u'phishing'] not foo


Schemaless Structure
~~~~~~~~~~~~~~~~~~~~

One of the main advantages of MongoDB is the ability to insert schemaless
documents into the database. As of version 0.7, MongoKit allows you to
save partially structured documents. For now, this feature must be activated.
It will be the default behavior in a future release.

To enable schemaless support, use the ``use_schemaless`` attribute::

    class MyDoc(Document):
        use_schemaless = True

Setting ``use_schemaless`` to ``True`` allows to have an unset structure, however
you can still specify a structure::

    # Python 3
    class MyDoc(Document):
        use_schemaless = True
        structure = {
            'title': str,
            'age': int
        }
        required_fields = ['title']

    # Python 2
    class MyDoc(Document):
        use_schemaless = True
        structure = {
            'title': basestring,
            'age': int
        }
        required_fields = ['title']


MongoKit will raise an exception only if required fields are missing::

    >>> doc = MyDoc({'age': 21})
    >>> doc.save()
    Traceback (most recent call last):
    ...
    StructureError: missed fields : ['title']
    >>> doc = MyDoc({'age': 21, 'title': 'Hello World !'})
    >>> doc.save()

