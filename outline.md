Yesod!
======

About this presentation
-----------------------

* Just an overview:
  * Yesod features,
  * Yesod ecosystem, and
  * Blog example (if time allows)
* Based on Michael Snoyman's QCon presentation.
* Homework:
  * Go to <http://www.yesodweb.com/>.
  * Read the Yesod book!


Yesod features
--------------

### Type-safe routes

* Routes are represented by data types.

        /             RootR     GET
        /blog/#BlogId BlogPostR GET

* Typos are caught in compile time.

        Not in scope: data constructor `BlogPsotR'
        Perhaps you meant `BlogPostR'

* Parsing and rendering always in sync: no broken links, ever!

### Compile-time templates

* Syntax checked at compile time.

* Only type-safe Haskell values used.

* Supports:
  * HTML (Hamlet),
  * CSS (Cassius, Lucius),
  * JS (Julius, CoffeeScript, Roy).

* Called "Shakespearean templates".

### XSS protection

* Text within your app is your text, always.

* App boundaries are correctly treated, automatically, always.

  * Templates,

  * Routes,

  * Forms,

  * Database queries.

* You never directly call any (un)escaping function.

### Persistent

* Declare entity definitions in a DSL (domain specific language).

        Person
          name String
          age Int Maybe
        BlogPost
          title String
          authorId PersonId

* Generates data types, marshalling functions, schemas.

* Automatic schema migration.

* SQL backends: PostgreSQL, MySQL, SQLite.

* NoSQL backends: MongoDB.

### Esqueleto

* Type checked embedded DSL for SQL queries.

* Built on top of persistent.

* Supports very complex queries.

        SELECT BlogPost.*, Person.*
        FROM BlogPost, Person
        WHERE BlogPost.authorId = Person.id
        ORDER BY BlogPost.title ASC

        select $
        from $ \(b, p) -> do
        where_ (b ^. BlogPostAuthorId ==. p ^. PersonId)
        orderBy [asc (b ^. BlogPostTitle)]
        return (b, p)


        SELECT Person.*
        FROM Person
        WHERE EXISTS (SELECT *
                      FROM BlogPost
                      WHERE BlogPost.authorId = Person.id)

        select $
        from $ \person -> do
        where_ $ exists $
                 from $ \post -> do
                 where_ (post ^. BlogPostAuthorId ==. person ^. PersonId)
        return person


* Composable queries.

        blogPostsFrom personId =
          from $ \post -> do
          where_ (post ^. BlogPostAuthorId ==. personId)
          return post

        bloggers =
          from $ \person -> do
          where_ $ exists $ void $ blogPostsFrom (person ^. PersonId)
          return person


### Widgets

* HTML, CSS, JavaScript and Haskell code in a single bundle.

* Affects both <body> and <head> (CSS and JS are concatenated in
  into a single, minified file).

### Others

* Type-safe I18n via messages as data types.

* Subsites, may define their own subroutes.

* Authentication with many ready-to-use plugins (e-mail, Google, Facebook...).

* Server middlewares (logging, gzip, JSON-P...).

* Automatic deployment via keter.

* And much more!


Yesod ecosystem
---------------

### Team

* Yesod was created by Michael Snoyman but currently is a work of
  many people.

* About a dozen people have commit access to Yesod repos right now.

* Many other contributors without commit access (yet!).

* Very friendly community, come join us =).

(Data from January 23rd, 2013.)

### Libraries

* The Yesod Team maintains a lot of libraries, many of which are
  not Yesod-specific and are used elsewhere.

* Some tracks of libraries:

  * Persistent: core, mysql, postgresql, sqlite, mongoDB.

  * Yesod: core, auth (+ plugins), form, persistent, json, default, platform.

  * Shakespeare: core, css, js, text, hamlet.

  * Conduit: core, attoparsec, base64, blaze-builder, crypto,
    http, network, pool, xml, zlib.

  * WAI: wai, wai-app-static, wai-extra, warp, warp-tls.

  * Others: cookie, clientsession, esqueleto, keter...

* However, none of these libraries would exist without the
  awesome ecosystem that has been flourishing on the Haskell land!


Blog example
------------

### Entities
### Routes
### Handlers
### Screenshot
