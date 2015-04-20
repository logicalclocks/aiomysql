.. _aiomysql-tutorial:

Tutorial
========

Python database access modules all have similar interfaces, described by the
:term:`DBAPI`. Most relational databases use the same synchronous interface,
*aiomysql* tries to provide same api you just need
to use  ``yield from conn.f()`` instead of just call ``conn.f()`` for
every method.

Installation
------------

.. code::

   pip3 install aiomysql

.. note:: :mod:`aiomysql` requires :term:`PyMySQL` library.

Getting Started
---------------

Lets start from basic example::


    import asyncio
    import aiomysql

    loop = asyncio.get_event_loop()

    @asyncio.coroutine
    def test_example():
        conn = yield from aiomysql.connect(host='127.0.0.1', port=3306,
                                           user='root', password='', db='mysql',
                                           loop=loop)

        cur = yield from conn.cursor()
        yield from cur.execute("SELECT Host,User FROM user")
        print(cur.description)
        r = yield from cur.fetchall()
        print(r)
        yield from cur.close()
        conn.close()

    loop.run_until_complete(test_example())


Connection is established by invoking the :func:`connect()` coroutine,
arguments list are keyword arguments, almost same as in :term:`PyMySQL`
corresponding method. Example makes connection to :term:`MySQL` server on
local host to access `mysql` database with user name `root`' and empty password.

If :func:`connect()` coroutine succeeds, it returns a :class:`Connection`
instance as the basis for further interaction with :term:`MySQL`.

After the connection object has been obtained, code in example invokes
:meth:`Connection.cursor()` coroutine method to create a cursor object for
processing  statements. Example uses cursor to issue a
``SELECT Host,User FROM user;`` statement, which returns a list of `host` and
`user` from :term:`MySQL` system table ``user``::

    cur = yield from conn.cursor()
    yield from cur.execute("SELECT Host,User FROM user")
    print(cur.description)
    r = yield from cur.fetchall()

The cursor object's :meth:`Cursor.execute()` method sends the query the server
and :meth:`Cursor.fetchall()` retrieves rows.

Finally, the script invokes :meth:`Cursor.close()` coroutine and
connection object's :meth:`Connection.close()` method to disconnect
from the server::

    yield from cur.close()
    conn.close()

After that, ``conn`` becomes invalid and should not be used to access the
server.

Inserting Data
--------------
