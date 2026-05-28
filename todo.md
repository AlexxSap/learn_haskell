- линейные классы
- линзы
- brick - tui for haskell
- tchan - транзакционный канал Александр Вершилов
- web-sockets
- работа с БД - hasql
- работа с БД - postgresql-libpq
- работа с БД - persistent
- сериализация структур
- FFI
https://ghc.gitlab.haskell.org/ghc/doc/users_guide/exts/ffi.html
- класс типов Generic
https://ghc.gitlab.haskell.org/ghc/doc/users_guide/exts/generics.html
- req
- wreq
- join
- idiom Smart constructors
https://wiki.haskell.org/index.php?title=Smart_constructors
- идиомы
https://wiki.haskell.org/index.php?title=Category:Idioms
- *>
- Inline
- openConnection :: HasCallStack => ConnectionArgs -> IO Connection - констрейнт (ограничение типа). Это не пользовательский класс типов, а встроенное ограничение GHC, которое автоматически привязывает стек вызовов к функции. Позволяет выводить точные сообщения об ошибках с указанием файла и строки, где произошло исключение.
- stm
- concurency
- создание документации
- servant - for building web services
- pandoc - for transforming text document formats
- async - for concurrent programming
- esqueleto and persistent - for storing and updating data in databases
- Frames - for computing with some machine learning methods
- https://github.com/Gabriella439/post-rfc/blob/main/sotu.md
- text formating 
    - Data.Text.Lazy.Builder from the text package provides a very limited
builder without any formatting. We can use it when we have many Text values
that should be combined into one.
    - The printf function from the Text.Printf module provides many format
specifiers. It takes a format string and data and produces a String.
    - The text-format, formatting, and the more recent fmt packages are very
close in their goals but different in their interfaces and implementation.
    - The template package provides a very simple template substitution engine.
Other full-blown templating systems are used mainly in web programming.
