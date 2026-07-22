**cassava**

это библиотека для чтения и записи csv файлов. Позволяет работать как с файлами в которых есть заголовок, так и просто по позициям.

Предположим, что у нас есть такой простой тип:
```haskell
data User = User {
     id :: Int,
     name :: Text,
     value :: Double
} deriving Show
```

Чтобы использовать библиотеку `cassava` для представления вектора (библиотека работает с `Vector`) объектов этого типа нужно реализовывать классы:
- `FromNamedRecord` и `ToNamedRecord` - для чтения и записи с заголовками,
- `FromRecord` и `ToRecord` - для чтения и записи по индексу колонки,
- `FromField` и `ToField` - для преобразования одной ячейки, это нужно, если данные имеют специфичный формат представления в строку.

Допустим мы работаем с файлов с заголовком:
```haskell
instance FromNamedRecord User where
    parseNamedRecord m = User
        <$> m .: "id"
        <*> m .: "name"
        <*> m .: "value"

instance ToNamedRecord User where
    toNamedRecord (User i n v) = namedRecord [
        "id" .= i,
        "name" .= n,
        "value" .= v ]
```
В `parseNamedRecord` оператор `.:` извлекает значение по имени. Если колонка может отсутствовать, можно использовать `.:?` (возвращает `Maybe`) и `.!=` (задает значение по умолчанию):
```haskell
<*> m .:? "value" .!= 21
```
В `toNamedRecord` оператор `.=` связывает имя колонки со значением.

Для кодирования и декодирования файла используется функции `encodeByName` и `decodeByName` (если нет заголовка то `encode` и `decode`). 
При этом разделителем по умолчанию является запятая (`,`), её можно заменить на `;` через указание настроек `DecodeOptions` и `EncodeOptions`:
```haskell
csvOption :: DecodeOptions
csvOption = defaultDecodeOptions {
    decDelimiter = fromIntegral (ord ';')
}
```

Пример использования:
```haskell
{-# LANGUAGE OverloadedStrings #-}
{-# LANGUAGE RecordWildCards #-}

module Main (main) where

import Data.Csv
import qualified Data.Vector as V
import qualified Data.ByteString.Lazy as BL 
import Data.Text (Text)
import Data.Char (ord)

-- тут User, FromNamedRecord, ToNamedRecord и csvOption пропустим

main :: IO ()
main = do
    -- читаем файл в ByteString
    csvData <- BL.readFile "users.csv"
    -- декодируем в вектор с указанием настроек (разделитель это ;)
    case decodeByNameWith csvOption csvData  of
         Left err -> putStrLn $ "Error: " ++ err
         Right (fileHeader, usersVector :: V.Vector User) -> do
               print usersVector
               let updated = V.map (\u -> u {value = value u + 100}) usersVector
               -- кодируем обратно в csv без настроек с тем же заголовком
               let outData = encodeByName fileHeader (V.toList updated)
               -- записываем в новый файл
               BL.writeFile "newUsers.csv" outData
```

Функции `decode` и `decodeByName` загружают весь `Vector` прочитанных данных в память. Если csv файл очень большой, то лучше использовать модуль `Data.Csv.Incremental`.
