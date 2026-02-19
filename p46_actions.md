**Полезные функции для работы с действиями**

**when из Control.Monad**
```haskell
when :: Applicative f => Bool -> f () -> f ()
```
Она принимает булево значение и действие ввода-вывода, которое выполняется только если первый параметр `True`. Можно использовать как замену `return ()` в выражении `if`:
```haskell
input <- getLine
-- это
when (input == "someStr") $ do
    putStrLn input
-- эквивалентно этому
if (input == "someStr")
    then putStrLn input
    else return ()
```

**sequence**
```haskell
sequence :: (Traversable t, Monad m) => t (m a) -> m (t a)
```
Принимает последовательность действий ввода-вывода и возвращает одно действие ввода-вывода, последовательно выполняющее входные действия:
```haskell
rs <– sequence [getLine, getLine, getLine]
```
создаст действие, которое выполнит `getLine` 3 раза. 

**mapM и mapM_**
```haskell
mapM :: (Traversable t, Monad m) => (a -> m b) -> t a -> m (t b)
mapM_ :: (Foldable t, Monad m) => (a -> m b) -> t a -> m ()
```
Функция `mapM` принимает функцию и список, применяет функцию к элементам списка, сводит элементы в одно действие ввода-вывода и выполняет их. Функция `mapM_` работает так же, но отбрасывает результат действия ввода-вывода.

**forever из Control.Monad**
```haskel
forever :: Applicative f => f a -> f b
```
создаёт бесконечный цикл (внутри там конечно же рекурсия) действий. Функция принмает одно действие и выполняет его снова и снова, пока программа не будет остановлена.

**forM и forM_ из Control.Monad**
```haskell
forM :: (Traversable t, Monad m) => t a -> (a -> m b) -> m (t b)
forM_ :: (Foldable t, Monad m) => t a -> (a -> m b) -> m ()
```
похожи на `mapM` и `mapM_`, но параметры поменяны местами. Первый параметр это список, второй – это функция, которую надо применить к списку и затем свести действия из списка в одно действие.
Использование `forM` более похоже на использование цикла `for` в императивных языках:
```haskell
forM [1, 2, 3] (\x -> putStrLn (show x))
```

**replicateM и replicateM_ из Control.Monad**
```haskell
replicateM :: Applicative f => Int -> f a -> f [a]
replicateM_ :: Applicative f => Int -> f a -> f ()
```
предназначены для выполнения одного и того же действия (второй параметр) определённое число раз (первый параметр). Как и в прочих функциях выше `_` в конце означенет, что функция вернёт пустое действие (результат нам не нужен).
В `replicateM` список результатов строится лениво. Однако сами действия выполняются строго по порядку.
```haskell
-- напечатать 5 случайных чисел до 100
numbers <- replicateM 5 (randomRIO (1, 100))
print numbers 
-- напечатать 3 раза в консоль
replicateM_ 3 (putStrLn "Привет!")
```
