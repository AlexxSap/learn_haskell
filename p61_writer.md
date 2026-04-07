**Стандартная монада Writer**

Предназдачена для накопления побочного вывода. Это может быть счётчик, строка для журнала - любой экземпляр `Monoid`. `Writer` позволяет автоматизировать этот сбор и накопление данных.
Для использования `Writer` нужно подключить в зависимости `mtl`.

```haskell
class (Monoid w, Monad m) => MonadWriter w m | m -> w where
  writer :: (a, w) -> m a
  tell :: w -> m ()
  listen :: m a -> m (a, w)
  pass :: m (a, w -> w) -> m a

newtype Writer w a = Writer { runWriter :: (a, w) }
```
`w` - это моноид для накопления данные в котором объединяются через `<>`(`mappend`).

Добавить данные для накопления в `Writer` можно с помощью функции `tell`, а прочитать результат и аккумулятор - `runWriter`:
```haskell
tell :: MonadWriter w m => w -> m ()
runWriter :: Writer w a -> (a, w)
```
Пример:
```haskell
import Control.Monad.Writer

addInt :: Int -> Int -> Writer [String] Int
addInt a b = do
       tell ["start function addInt"]
       tell ["add " ++ show a ++ " to " ++ show b]
       pure (a+b)

main :: IO ()
main = print (runWriter (addInt 3 4)) -- (7,["start function addInt","add 3 to 4"])
```

Важно отметить, что `Writer` ленив и может преводить к переполнению стэка при вызове `runWriter` и росту потребления памяти на накопление `thunk` при добавлении сообщений в лог. Чтобы избежать этого можно использовать строгую версию `Control.Monad.Writer.Strict` и использовать строгие моноиды для накопления (например `Data.Text.Strict`).
