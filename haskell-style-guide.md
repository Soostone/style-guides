Soostone Haskell Style Guide
==================================

(Adapted from [the Snap Framework's style
guide.](https://github.com/snapframework/snap-website/blob/master/snaplets/heist/templates/docs/style-guide.md))

This document describes coding and comment style for the Soostone projects.
When something isn't covered by this guide you should stay consistent with the
style used in our existing code.  The overall goal here is that code be easy
to read.  This means the coding style should be driven by broader guidelines
for good visual design.  See
[http://www.visualmess.com/index.html](http://www.visualmess.com/index.html)
for more detail about how seemingly small things like alignment help improve
readability.

1. Formatting
-------------

### Line Length

Maximum line length is *78 characters*.

### Indentation

#### Code

Tabs are illegal. Use spaces for indenting.  Indent your code blocks with *4
spaces*.  Indent the `where` keyword two spaces to set it apart from the rest
of the code and indent the definitions in a `where` clause 2 spaces. Usually we
indent guards two spaces. Some examples:

~~~~~~~~ {.haskell}
------------------------------------------------------------------------------
-- | Reads the user's name and prints a greeting to stdout.
sayHello :: IO ()
sayHello = do
    name <- getLine
    putStrLn $ greeting name
  where
    greeting name = "Hello, " ++ name ++ "!"

------------------------------------------------------------------------------
-- | 'filter', applied to a predicate and a list, returns the list of
-- those elements that satisfy the predicate; i.e.,
--
-- > filter p xs = [ x | x <- xs, p x]
filter :: (a -> Bool) -> [a] -> [a]
filter _ []     = []
filter p (x:xs)
  | p x       = x : filter p xs
  | otherwise = filter p xs
~~~~~~~~

#### Type Signatures

When a type signature fits completely on one 78-character line, we typically
just leave it as a single line.  When a signature is longer, indent as
follows:

~~~~~~~~ {.haskell}
reallyLongFunctionName
    :: (MonadIO m, MonadSnap m)
    => (Handler LongType LongerType ()
        -> Handler LongType LongerType a
        -> Handler b v a)
    -> (Text -> Text -> Text)
    -> Text
    -> Text
    -> m ()
reallyLongFunctionName = undefined
~~~~~~~~

This makes it so that if you change the name of a function to make it longer
or shorter you don't have to re-indent the rest of the type signature.  If you
have to split the type signature, you must put the type class constraints and
each argument on separate lines and they should be indented four spaces.

### Blank Lines

Two blank lines between top-level definitions, and a line of 78 "-"
characters to delineate top-level definitions from each other.  No blank lines between
type signatures and function definitions.  Add one blank line between
functions in a type class instance declaration if the functions bodies
are large.  Use your judgment.

### Whitespace

Surround binary operators with a single space on either side.  Use
your better judgment for the insertion of spaces around arithmetic
operators but always be consistent about whitespace on either side of
a binary operator.  Don't insert a space after a lambda.

### Data Declarations

Vertically align the constructors in a data type definition.  Example:

~~~~~~~~ {.haskell}
data Tree a = Branch a (Tree a) (Tree a)
            | Leaf
~~~~~~~~

For long type names the following formatting is also acceptable:


~~~~~~~~ {.haskell}
data HttpException
    = InvalidStatusCode Int
    | MissingContentHeader
~~~~~~~~

Format records as follows (but see below for the naming convention):

~~~~~~~~ {.haskell}
data Person = Person
    { firstName :: String  -- ^ First name
    , lastName  :: String  -- ^ Last name
    , age       :: Int     -- ^ Age
    } deriving (Eq, Show)
~~~~~~~~

### Records

To prevent record fields clashing as well as creating self-documenting functions, prefix record fields with the value constructor's name or abbreviated name:

~~~~~~~~ {.haskell}
data AppConfig = AppConfig {
      _acAppName    :: Text
    , _acLog        :: Severity
    , _acSqlitePath :: Maybe FilePath
    }
~~~~~~~~

### Pragmas

Put pragmas immediately following the function they apply to.
Example:

~~~~~~~~ {.haskell}
id :: a -> a
id x = x
{-# INLINE id #-}
~~~~~~~~

In the case of data type definitions you must put the pragma before
the type it applies to.  Example:

~~~~~~~~ {.haskell}
data Array e = Array
    {-# UNPACK #-} !Int
    !ByteArray
~~~~~~~~

### Hanging Lambdas

You may or may not indent the code following a "hanging" lambda.  Use
your judgment. Some examples:

~~~~~~~~ {.haskell}
bar :: IO ()
bar = forM_ [1, 2, 3] $ \n -> do
          putStrLn "Here comes a number!"
          print n

foo :: IO ()
foo = alloca 10 $ \a ->
      alloca 20 $ \b ->
      cFunction a b
~~~~~~~~

### Export Lists

Format export lists as follows:

~~~~~~~~ {.haskell}
module Data.Set
    (
      -- * The @Set@ type
      Set
    , empty
    , singleton

      -- * Querying
    , member
    ) where
~~~~~~~~

2. Imports
----------

Imports should be grouped in the following order:

1. standard library imports
2. related third party imports
3. local application/library specific imports

Put a line of '-' characters between each group of imports.  The imports in each
group should be sorted alphabetically, by module name.

Always use explicit import lists or `qualified` imports for standard
and third party libraries.  This makes the code more robust against
changes in these libraries.  Exception: The Prelude.

3. Comments
-----------

### Line Length

Maximum line length is *78 characters*.  This increases readability as
the eye has to travel back to the start of the next line.

### Punctuation

Write proper sentences; start with a capital letter and use proper
punctuation.

### Top-Level Definitions

Comment every top level function (particularly exported functions),
and provide a type signature; use Haddock syntax in the comments.
Comment every exported data type.  Some examples:

~~~~~~~~ {.haskell}
------------------------------------------------------------------------------
-- | Send a message on a socket.  The socket must be in a connected
-- state.  Returns the number of bytes sent.  Applications are
-- responsible for ensuring that all data has been sent.
send
    :: Socket      -- ^ Connected socket
    -> ByteString  -- ^ Data to send
    -> IO Int      -- ^ Bytes sent


------------------------------------------------------------------------------
-- | Bla bla bla.
data Person = Person
    { age  :: Int     -- ^ Age
    , name :: String  -- ^ First name
    }
~~~~~~~~

For functions the documentation should give enough information to
apply the function without looking at the function's definition.

### End-of-Line Comments

Separate end-of-line comments from the code using 2 spaces.  Align
comments for data type definitions.  Some examples:

~~~~~~~~ {.haskell}
data Parser = Parser
    Int         -- Current position
    ByteString  -- Remaining input

foo :: Int -> Int
foo n = salt * 32 + 9
  where
    salt = 453645243  -- Magic hash salt.
~~~~~~~~

4. Naming
---------

Use mixed-case when naming functions and camel-case when naming data
types.

For readability reasons, don't capitalize all letters when using an
abbreviation.  For example, write `HttpServer` instead of
`HTTPServer`.  Exception: Two letter abbreviations, e.g. `IO`.

5. Misc
-------

### Warnings ###

Code should be compilable with `-Wall -Werror -fno-warn-unused-binds`.
There should be no warnings.

