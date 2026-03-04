**Работа с текстовыми файлами**

Чтение и запись файлов являются нечистыми операциями, потому выполняются в контексте `IO`. 

Выполнять простую работу с текстом в виде типа `String` можно функциями:
```haskell
readFile :: FilePath -> IO String
writeFile :: FilePath -> String -> IO ()
appendFile :: FilePath -> String -> IO ()
```

Однако тип `String` ленив и не эффективен и почти всегда с текстом работают через `Data.Text`. Для файловой работы в модуле `Data.Text.IO` есть точно такие же функции:
```haskell
module Main (main) where

import qualified Data.Text as Text
import qualified Data.Text.IO as TextIO

doSomeMagic :: Text.Text -> Text.Text
...

main :: IO ()
main = do
  input <- TextIO.readFile "some.txt"
  let result = doSomeMagic input
  TextIO.putStrLn result
```

Работать так же можно с дескрипторами файлов `IO Handle` с помощью модуля `System.IO`, при этом можно указать больше параметров для работы с файлом (способ открытия, кодировка). Для этого существуют функции:
```haskell
-- открыть файл с указанием режима
openFile :: FilePath -> IOMode -> IO Handle
-- закрыть файл
hClose :: IO.Handle -> IO ()
-- прочитать строку из файла
hGetLine :: IO.Handle -> IO String
-- читает весь файл в одну строку 
hGetContents :: IO.Handle -> IO String
-- записать строку в файл
hPutStrLn :: IO.Handle -> String -> IO ()
-- установить кодировку файла
hSetEncoding :: IO.Handle -> IO.TextEncoding -> IO ()
```
где `IOMode` определяет какая работа будет выполняться с файлом:
```haskell
data IOMode = ReadMode | WriteMode | AppendMode | ReadWriteMode
```

Однако ручное открытие-закрытие файла через `openFile` и `hClose` не безопасно - в случае ошибки или склероза файл может не быть закрытым. Для безопасной работы лучше использовать действие `withFile` из `System.IO`, которое откроет файл и закроет его, когда это будет нужно:
```haskell
withFile :: FilePath -> IOMode -> (IO.Handle -> IO r) -> IO r
```

Пример:
```haskell
import System.IO
import qualified Data.Text.IO as TextIO

main :: IO ()
main = do
    withFile "input.txt" ReadMode $ \handle -> do
        hSetEncoding handle utf8
        content <- TextIO.hGetContents handle
        putStrLn (show content)
```

Для работы с большими файлами (когда прочитать их целиком в `Data.Text` не получается) используют библиотеки потоковой обработки - `conduit`, `pipes` или `streaming`.

