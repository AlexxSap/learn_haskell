- линейные классы
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
- форматирование текста с помощью PyF
- форматирование текста с помощью formatting
- монадические парсеры
- cassava - чтение csv
- blaze-htm - генерирование html https://jaspervdj.be/blaze/tutorial.html
- lucid - генерирование html https://chrisdone.com/posts/lucid/
- colonnade - представление таблиц 
- Optics — это обобщённая версия линз, которая включает в себя:
  Prisms (призмы) для работы с sum types (например, Either).
  Traversal для работы с коллекциями или множеством значений.
  Iso (изоморфизмы) для преобразования данных между двумя формами.
