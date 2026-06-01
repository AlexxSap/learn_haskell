**Генерация псевдослучайных чисел**

для работы с рандомом есть пакет `random`, который исторически делится на 2 этапа: 1. до версии `1.2.1.1` с глобальным состоянием генератора и 2. начиная с `1.2.1.1` с явной передачей состояния генератора.
Для получения случайного числа внутри `IO` существуют функции:
- `mkStdGen :: Int -> StdGen` для создания стандартного генератора `StdGen` с начальным `seed`,
- `uniform :: forall g a. (RandomGen g, Uniform a) => g -> (a, g)` - выдаёт случайное значение из генератора `g` и возвращает новое состояние генератора,
- `uniformR :: forall g a. (RandomGen g, UniformRange a) => (a, a) -> g -> (a, g)` - выдаёт случайное значение в диапазоне и новое состояние генератора.
Новое состояние генератора нужно для использования его в следующей генерации, если вызывать эти функции на одном и том же состоянии, то они будут выдавать один и тот же результат.

Для монадических вычислений пакет `System.Random.Stateful` предоставляет следующие функции:
- `runStateGen_ :: forall g a. RandomGen g => g -> (StateGenM g -> State g a) -> a` - для выполнения вычислений с внутри контекста сохранения состояния генератора,
- `uniformM :: forall a g (m :: Type -> Type).(Uniform a, StatefulGen g m) => g -> m a` - для получения случайного значения,
- `uniformRM :: forall a g (m :: Type -> Type).(UniformRange a, StatefulGen g m) => (a, a) -> g -> m a` - для получения случайного значения в диапазоне.

```haskell
module Main (main) where

import System.Random
import System.Random.Stateful

genPair :: (Int, Int)
genPair = runStateGen_ (mkStdGen 100) $ \gen -> do
  x <- uniformM gen
  y <- uniformRM (1,100) gen
  pure (x, y)

main :: IO ()
main = do
  let gen1 = mkStdGen 42
  let (a :: Int, gen2) = uniform gen1
  let (b :: Int, _) = uniformR (1, 1000) gen2
  let (sameb :: Int, _) = uniformR (1, 1000) gen2
  print a -- 1275548033995301424
  print b -- 664
  print sameb -- 664
  print genPair -- (9216477508314497915,39)
```

Если у нас есть какой-то свой тип (например "перечисление") и мы хотим генерировать для него случайные значения, то он должен реализовывать классы типов `UniformRange` (для выдачи значения в диапазоне) и `Uniform` (для выдачи значений из всех возможных значений):
```haskell
data Direction = North | East | South | West
  deriving (Eq, Enum, Bounded, Show)

instance UniformRange Direction where
  uniformRM (lo, hi) rnd = do
    res <- uniformRM (fromEnum lo :: Int, fromEnum hi) rnd
    pure $ toEnum res

instance Uniform Direction where
  uniformM rnd = uniformRM (minBound, maxBound) rnd
  
main :: IO ()
main = do
  let gen1 = mkStdGen 42
  let (d1 :: Direction, gen2) = uniform gen1
  let (d2 :: Direction, _) = uniform gen3
  print d1 -- North
  print d2 -- West
```

Но начиная с версии `1.3.0` пакета `random` для `UniformRange` еще нужно реализовывать функцию `isInRange :: (a, a) -> a -> Boolean` для проверки нахождения значения в диапазоне.
