**Работа с бинарными данными**

Для работы с двоичными данными используется тип `Data.ByteString`. Внутри он представлен как простой массив байт. 

Строковые литералы можно использовать как значения `Data.ByteString`, если включено расширение `OverloadedString`. Однако функции `pack` и `unpack` (из `Data.ByteString`) для этого не подходят, в отличии от аналогичных из `Data.Text`. Для такого нужны функции из `Data.ByteString.Char8`:
```haskell
Data.ByteString.pack :: [GHC.Word.Word8] -> Data.ByteString.ByteString
Data.ByteString.unpack :: Data.ByteString.ByteString -> [GHC.Word.Word8]
Data.ByteString.Char8.pack :: String -> Data.ByteString.ByteString
Data.ByteString.Char8.unpack :: Data.ByteString.ByteString -> [Char]
```

Для простого чтения и записи бинарного файла целиком есть функции `readFile` и `writeFile` из `Data.ByteString.Char8`, которые работают аналогично их текстовым тёзкам.

Для ленивой работы с бинарными данными есть `Data.ByteString.Lazy`, который представлен как связаный список чанков (chunk) байт, которые заполняются при необходимости. Ленивый аналог позволяет работать потоково с большими файлами.
```haskell
module Main (main) where

import qualified Data.ByteString.Lazy as BSL

countSome :: FilePath -> IO Int
countSome path = do
  content <- BSL.readFile path
  pure $ fromIntegral $ BSL.length $ BSL.filter (==6) content

main :: IO ()
main = do
  let dataToFile = BSL.pack [1,2,3,6,4,5,6,6] -- представим, что это большой файл
  let fileName = "test.bin"
  BSL.writeFile fileName dataToFile
  cnt <- countSome fileName
  print cnt -- выведем 3
```
