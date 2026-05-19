- комбинаторы и их функции в haskell
- работа с памятью
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
- '{..}'
- Inline
- openConnection :: HasCallStack => ConnectionArgs -> IO Connection - констрейнт (ограничение типа). Это не пользовательский класс типов, а встроенное ограничение GHC, которое автоматически привязывает стек вызовов к функции. Позволяет выводить точные сообщения об ошибках с указанием файла и строки, где произошло исключение.
- creds@MkConnectionArgs{...} : комбинация двух паттернов:
- @ (As-pattern): привязывает весь переданный аргумент к имени creds, позволяя использовать его целиком внутри функции.
- MkConnectionArgs{mHostname, mOsUser} : Record Puns (включается расширением NamedFieldPuns). Вместо явного MkConnectionArgs { mHostname = mh, mOsUser = ou } можно просто перечислить имена полей, если они совпадают с именами переменных. Компилятор сам создаст локальные переменные mHostname и mOsUser.
- stm
- concurency

