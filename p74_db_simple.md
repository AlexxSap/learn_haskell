**Работа с БД**

Для работы с БД в haskell сеть 2 подхода - прямые запросы (simple) и типобезопасный ORM-подобный (persistent). Ниже рассмотрен первый вариант на примере sqlite и библиотеки sqlite-simple. Подобные библиотеки есть для mysql (mysql-simple) и postgreSQL (postgresql-simple).

Для соединения с БД и закрытия соединения используются функции, оперирующие типом `Connection`:
```haskell
open :: String -> IO Connection
close :: Connection -> IO ()
withConnection :: String -> (Connection -> IO a) -> IO a
```
Функция `open` бросает исключение, если не удалось соединиться с БД. Так же есть функция `withConnection`, которая подключится к БД, выполнит действие `Connection -> IO a` и закроет соединение.

Для выполнения запросов есть функции `execute` (не возвращает результатов) и `query` (возвращает результат для запросов `SELECT`). 
```haskell
execute :: ToRow q => Connection -> Query -> q -> IO ()
query :: (ToRow q, FromRow r) => Connection -> Query -> q -> IO [r]
```

Их несколько вариантов с одинаковым паттерном наименования:
- `execute` и `query` - выполняет запрос с биндом значений,
- `execute_` и `query_` - выполняет запрос без подстановки значений,
- `executeNamed` и `queryNamed` - выполняет запрос с именованными плейсхолдерами. 

Пример:
```haskell
{-# LANGUAGE OverloadedStrings #-}
module Main (main) where

import Database.SQLite.Simple
import Data.Text as T (Text)

main :: IO ()
main = do
   conn <- open ":memory:"
   execute_ conn "CREATE TABLE IF NOT EXISTS Amounts (id INT PRIMARY KEY, name TEXT, amount DOUBLE);"

   executeNamed conn "INSERT INTO Amounts (id, name, amount) VALUES (:id, :name, :amount)"
     [":id" := (0 :: Int) , ":name" := ("SomeText" :: T.Text), ":amount" := (12.3 :: Double)]

   amounts <- query_ conn "SELECT id, name, amount FROM Amounts;" :: IO [(Int, T.Text, Double)]
   mapM_ print amounts -- 

   close conn
```

Также можно использовать транзакции с помощью функции `withTransaction`, которая оборачивает действие в транзакцию и делает `rollback`, если действие завершилось ошибкой.
```haskell
withTransaction :: Connection -> IO a -> IO a
-- пример:
_ <- withTransaction conn $ do
     executeNamed conn "INSERT INTO Amounts (id, name, amount) VALUES (:id, :name, :amount);"
         [":id" := (0 :: Int) , ":name" := ("SomeText" :: T.Text), ":amount" := (12.3 :: Double)]
```

Для чтения данных из базы в объекты типов haskell есть класс типов `FromRow`:
```haskell
class FromRow a where
    fromRow :: RowParser a
```
Для создания экземпляра `FromRow` нужно в функции `fromRow` определить, как конструировать наш тип:
```haskell
data Amount = Amount Int T.Text Double deriving Show

instance FromRow Amount where
  fromRow = Amount <$> field
                   <*> field
                   <*> field
```
Функция `field` используется внутри `SQLite.Simple` для извлечения данных из sql-строки и преобразования их в значения с помощью нашего конструктора.
После этого нужно просто указать наш тип данных при чтении из БД:
```haskell
amounts <- query_ conn "SELECT id, name, amount FROM Amounts;" :: IO [Amount]
```

