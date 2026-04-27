**Исключения**

Как и прочих языках в haskell исключения служат для обработки неожиданных ситуаций. Они могут возникнуть как в чистом коде, так и в контексте `IO`. Но чистый код не знает ничего об исключениях, это же побочный эффект, так что обработать исключения можно только внутри контекста `IO`.
В чистом коде рекомендуется использовать `Maybe` и `Either` и не порождать исключений. Стоит оставлять исключения для действительно исключительных ситуаций.

Исключения являются обычными типами входящими в иерархию с корнем `SomeException`), которые реализуют класс `Exception`:
```haskell
class (Typeable e, Show e) => Exception e where
  toException :: e -> SomeException
  fromException :: SomeException -> Maybe e
  displayException :: e -> String 
```

В модуле `Control.Exception` есть функции для создания и отлова исключений:
- `try` - пытается выполнить действие и возвращает результат в виде `Either` - если вычисление успешно, результат возвращается в `Right`, если возникло исключение указанного типа - в `Left`.
```haskell
try :: Exception e => IO a -> IO (Either e a)
```
Пример:
```haskell
result <- try (evaluate (42 `div` 0)) :: IO (Either ArithException Int)
case result of
     Left ex  -> putStrLn $ "Caught exception: " ++ show ex
     Right val -> putStrLn $ "The answer was: " ++ show val
```

- `catch` - принимает действие и обработчик исключения. Если при выполнении действия возникает исключение, вызывается обработчик. 
```haskell
catch :: Exception e => IO a -> (e -> IO a) -> IO a
```
Пример:
```haskell
catch (print $ 42 `div` 0) handler
  where
    handler :: ArithException -> IO ()
    handler ex = putStrLn $ "Caught exception: " ++ show ex
```

- `handle` - это `catch` с обратным порядком аргументов
```haskell
handle :: Exception e => (e -> IO a) -> IO a -> IO a
```
- `tryJust`, `catchJust`, `handleJust` — позволяют указать функцию выбора, которая фильтрует, какие исключения обрабатывать.
- `throw` и `throwIO` - бросают исключения из чистого кода и кода в контексте `IO`. 
```haskell
throw :: Exception e => e -> a
throwIO :: Exception e => e -> IO a
```
- `finally` - позволяет выполнить `b` независимо от успеха `a`
```haskell
finally :: IO a -> IO b -> IO a
```
- `bracket` - для работы с ресурсами, которые требуют корректного освобождения. Первым аргументом она принимает функцию, выделяющую ресурс, вторым - функцию, освобождающую ресурс, а третьим - функцию, непосредственно использующую этот ресурс. Ресурс освобождается независимо от того, было брошено при работе с ним исключение, или нет.
```haskell
bracket :: IO a -> (a -> IO b) -> (a -> IO c) -> IO c
```

