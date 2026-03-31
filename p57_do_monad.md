**Монады и do-нотация**

`do`-нотация, является синтаксическим сахаром, который позволяет записать вызов монадических вычислений в более простом и понятном виде.

Например:
```haskell
echoPrefix :: String -> String
echoPrefix s = "echo:" ++ s

monadEcho :: IO()
monadEcho = putStrLn "put some line to echo:"
                >> getLine
                >>= (\line -> pure (echoPrefix line))
                >>= putStrLn

doEcho :: IO()
doEcho = do
    putStrLn "put some line to echo:"
    line <- getLine 
    putStrLn (echoPrefix line)
```

Функции `monadEcho` и `doEcho` эквивалентны, просто записанны в разных стилях, однако `doEcho` проще для восприятия и не перегружена операторыми.
На самом деле компилятор в процессе дешугаринга преврящает `do`-блок в цепочку `>>=` и `doEcho` превращается практически в `monadEcho`, за исключением использования `>>`:
```haskell
desugarEcho :: IO()
desugarEcho = putStrLn "put some line to echo:"
            >>= \_ -> getLine
            >>= \line -> pure (echoPrefix line)
            >>= putStrLn
```
