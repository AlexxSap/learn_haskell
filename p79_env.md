**Переменные среды**

Для работы с переменными среды есть модуль `System.Environment`. Основными функциями являются:
- `lookupEnv :: String -> IO (Maybe String)` - возвращает `Just` значение по имени переменной или `Nothing`, если такой переменной нет,
- `getEnv :: String -> IO String` - возвращает значение переменной или бросает исключение `IOError`,
- `getEnvironment :: IO [(String, String)]` - возвращает все переменные среды с их именами,
- `setEnv :: String -> String -> IO ()` - меняет значение переменной в текущем процессе и его дочерних процессах,
- `unsetEnv :: String -> IO ()` - удаляет переменную среды в текущем процессе и его дочерних процессах.

Хотя запрос значения переменной будет выполнен сразу, стоит помнить, что строки в haskell ленивы, потому лучше здесь использовать оператор `(!)` для того, чтобы прочитать результат сразу:
```haskell
!someValue <- lookupEnv "SOME"
```

Так же стоит помнить, что значение переменных среды всегда `String`, для преобразования в нужный тип можно использовать `Text.Read.readMaybe`:
```haskell
module Main (main) where

import System.Environment (lookupEnv, setEnv)
import Text.Read (readMaybe)

readInt :: String ->IO (Maybe Int)
readInt n = lookupEnv n >>= pure . (>>= readMaybe)

name = "STRONG_PASSWORD"

main :: IO ()
main = do
     setEnv name "12345678"
     !someValue <- lookupEnv name
     print someValue -- Just "12345678"
     !someIntValue <- readInt name
     print someIntValue  -- Just 12345678
     !someWrongIntValue <- readInt "SOME"
     print someWrongIntValue -- Nothing
```

Для работы с переменными среды есть несколько других библиотек, например [optparse-applicative](https://github.com/pcapriotti/optparse-applicative) и [dotenv-hs](https://github.com/stackbuilders/dotenv-hs).
