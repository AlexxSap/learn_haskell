**Аргументы командной строки**

Функция `main` не принимает аргументы командной строки напрямую. Вместо этого используются функции пакета `System.Environment`, аргументы командной строки извлекаются внутри `IO`-контекста:
- `getArgs :: IO [String]` - возвращает список аргументов, НО без традиционного имени исполняемого первым аргументом,
- `getProgName :: IO String` - возвращает имя исполняемого файла,
- `getExecutablePath :: IO FilePath` - возвращает абсолютный путь к исполняемому файлу. 
- `withArgs :: [String] -> IO a -> IO a` - позволяет запустить `IO`-действие с заданными аргументами. Это очень полезно при тестировании, чтобы эмулировать запуск действия без необходимости дёргать реальный файл. 

```haskell
module Main (main) where

import System.Environment 

main :: IO ()
main = do
     args <- getArgs
     name <- getProgName
     -- запуск: cabal run sample -- --some --args=value
     putStrLn ("name: " ++ name) 
     -- name: sample
     putStrLn ("args: " ++ show args) 
     -- args: ["--some","--args=value"]
     
     withArgs ["--env", "test"] $ do
        localArgs <- getArgs
        -- local: ["--env","test"]
        putStrLn ("local: " ++ show localArgs)
```

Естественно есть библиотека, которая предоставляет больше средств для работы с аргументами командной строки - `optparse-applicative` (https://github.com/pcapriotti/optparse-applicative). Она автоматически генерирует `--help`, валидирует типы, поддерживает флаги, опции и позиционные аргументы. 
