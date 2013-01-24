# Yesod!

.fx: title

Felipe Lessa (<felipe.lessa@loyful.com>)

2nd São Paulo Haskellers Meeting

January 23rd, 2013


---

# About this presentation

* Just an overview:
    * Yesod features,
    * Yesod ecosystem, and
    * Blog example (if time allows)
* Somewhat based on Michael Snoyman's QCon 2011 presentation.

---

# Yesod features

---

# Type-safe routes

* Routes are represented by data types.

        !haskell
        /             RootR     GET
        /blog/#BlogId BlogPostR GET

* Typos are caught in compile time.

        Not in scope: data constructor `BlogPsotR'
        Perhaps you meant `BlogPostR'

* Parsing and rendering always in sync: no broken links, ever!

---

# Compile-time templates

* Called "Shakespearean templates".

* Syntax checked at compile time.

* Only type-safe Haskell values used.

* Supports:
    * HTML (Hamlet),
    * CSS (Cassius, Lucius),
    * JavaScript (Julius, CoffeeScript, Roy).

---

# Example

* Hamlet:

        !html
        $doctype 5
        <html>
            <head>
                <title>#{pageTitle} - My Site
                <link rel=stylesheet href=@{Stylesheet}>
            <body>
                <h1 .page-title>#{pageTitle}
                <p>Here is a list of your friends:
                $if null friends
                    <p>Sorry, I lied, you don't have any friends.
                $else
                    <ul>
                        $forall Friend name age <- friends
                            <li>#{name} (#{age} years old)
                ^{footer}

* Cassius:

        !css
        #myid
            color: #{red}
            font-size: #{bodyFontSize}
        foo bar baz
            background-image: url(@{MyBackgroundR})

---

# Security

* Avoids XSS attacks and SQL injections:

    * Text within your app is your text, always.

    * App boundaries are correctly treated, automatically, always.

        * Templates (interpolation),

        * Routes (parsing and rendering),

        * Forms (decoding and encoding),

        * Database queries (decoding and encoding).

    * You never directly call any (un)escaping function.

* Avoids XSRF attacks:

    * Forms always query a token used to avoid XSRF attacks.

* Entire classes of bugs are eliminated!

---

# Persistent

* Declare entity definitions in a DSL (domain specific language).

        !haskell
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

---

# Persistent

* CRUD example:

        !haskell
          johnId <- insert $ Person "John Doe" (Just 35)
          janeId <- insert $ Person "Jane Doe" Nothing

          insert $ BlogPost "My fr1st p0st" johnId
          insert $ BlogPost "One more for good measure" johnId

          oneJohnPost <- selectList [BlogPostAuthorId ==. johnId] [LimitTo 1]
          liftIO $ print (oneJohnPost :: [Entity BlogPost])

          john <- get johnId
          liftIO $ print (john :: Maybe Person)

          delete janeId
          deleteWhere [BlogPostAuthorId ==. johnId]

---

# Esqueleto

* Type checked embedded DSL for SQL queries.

* Built on top of persistent.

* Supports very complex queries.  For example:

    * SQL:

            !sql
            SELECT BlogPost.*, Person.*
            FROM BlogPost, Person
            WHERE BlogPost.authorId = Person.id
            ORDER BY BlogPost.title ASC

    * Haskell using esqueleto:

            !haskell
              select $
              from $ \(b, p) -> do
              where_ (b ^. BlogPostAuthorId ==. p ^. PersonId)
              orderBy [asc (b ^. BlogPostTitle)]
              return (b, p)


---

# Esqueleto

* Queries are composable as well!

        !haskell
        blogPostsFrom personId =
          from $ \post -> do
          where_ (post ^. BlogPostAuthorId ==. personId)
          return post

        bloggers =
          from $ \person -> do
          where_ $ exists $ void $ blogPostsFrom (person ^. PersonId)
          return person


* Using it inside Yesod:

        !haskell
          people <- runDB (select bloggers)   {- (= -}

---

# Widgets

* HTML, CSS, JavaScript and Haskell code in a single bundle.

* Affects both <body> and <head> (CSS and JS are concatenated in
  into a single, minified file).

* Example:

        !haskell
        footer = do
            toWidget [lucius|
              footer {
                font-weight: bold; text-align: center
              }
            |]
            toWidget [hamlet|
              <footer>
                <p>That's all folks!
            |]

* Templates could be in another file.

* There could be arbitrary I/O actions, such as database queries.

---

# Type-safe i18n messages

* I18n messages are generated from a file that does not scare translators.

* However, a data type is created from them (just like routes).

* Example:

    * English:

            !makefile
            CommentsHeading: Comments
            NoComments: There are no comments
            AddCommentHeading: Add a Comment
            CommentAuthor author@Text: Comment by #{author}

    * Portuguese:

            !makefile
            CommentsHeading: Comentários
            NoComments: Não há nenhum comentário
            AddCommentHeading: Deixar um Comentário
            CommentAuthor author@Text: Comentário de #{author}

---

# Others

* Subsites, may define their own subroutes.

* Authentication with many ready-to-use plugins (e-mail, Google, Facebook...).

* Server middlewares (logging, gzip, JSON-P...).

* Automatic deployment via keter.

* And much more!

---

# Yesod ecosystem

---

# Team

* Yesod was created by Michael Snoyman but currently is a work of
  many people.

* About a dozen people have commit access to Yesod repos right now.

* Many other contributors without commit access (yet!).

* Very friendly community, come join us =).

---

# Libraries

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

---

# Thanks!

* Contact info: Felipe Lessa (<felipe.lessa@loyful.com>)

* Homework:
    * Go to <http://www.yesodweb.com/>.
    * Read the Yesod book!
