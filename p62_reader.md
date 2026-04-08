**Стандартная монада Reader**

Предназначена для безопасной и удобной передачи неизменяемого окружения (конфигурации, настроек) через цепочку вычислений без необходимости явно пробрасывать один и тот же аргумент в каждую функцию.

```haskell
class Monad m => MonadReader r m | m -> r where
  ask :: m r
  local :: (r -> r) -> m a -> m a
  reader :: (r -> a) -> m a

newtype Reader r a = Reader { runReader :: r -> a }
```
Функция `ask` позволяет получить всё окружение целиком. Еще есть функция `asks` (`(r -> a) -> Reader r a`), которая позволяет отобрать часть окружения с использованем функции отбора (например имени поля). Функция `local` позволяет изменить окружение для вложенного вычисления (временно). `runReader` устанавливает начальное окружение в каком-то значении.

Пример:
```haskell
module Main (main) where

import Control.Monad.Reader

data MyEnv = MyEnv {
    val :: Int, 
    str :: String
} deriving (Show)

getAnswer :: Reader MyEnv String
getAnswer = do
       v <- asks val
       pure $ "the answer is " ++ show v

fakeAnswer :: Reader MyEnv String
fakeAnswer = local (\cfg -> cfg {val = 33}) getAnswer

main :: IO ()
main = do
     let cfg = MyEnv 42 "some"
     print $ runReader fakeAnswer cfg -- "the answer is 33"
     print $ runReader getAnswer cfg -- "the answer is 42"
     print $ runReader ask cfg -- MyEnv {val = 42, str = "some"}
```
