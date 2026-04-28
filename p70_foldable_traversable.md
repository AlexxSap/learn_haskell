**Foldable и Traversable**

Класс типов `Foldable` позволяет сводить структуры данных к одному значению, абстрагировать операцию сворачивания (`fold`). 
А класс типов `Traversable` - позволяет обходить структуры с выполнением эффектов, сохраняя их форму, объединяя `Functor` (отображение) и `Foldable` (сворачивание) с поддержкой эффектов.

```haskell
class Foldable t where
  foldMap :: Monoid m => (a -> m) -> t a -> m
  foldr :: (a -> b -> b) -> b -> t a -> b
  foldl :: (b -> a -> b) -> b -> t a -> b
  toList :: t a -> [a]
  null :: t a -> Bool
  length :: t a -> Int

class (Functor t, Foldable t) => Traversable t where
  traverse :: Applicative f => (a -> f b) -> t a -> f (t b)
  sequenceA :: Applicative f => t (f a) -> f (t a)
  mapM :: Monad m => (a -> m b) -> t a -> m (t b)
  sequence :: Monad m => t (m a) -> m (t a)
```

Минимальным полным определением для `Foldable` является `foldr` или `foldMap` (предпочтительнее и проще), а для `Traversable` - `traverse` или `sequenceA`.

Основными функциями `Foldable` являются:
- `foldr` и `foldl` - правая и левая свёртки,
- `foldMap` - отображение элементов в моноид и их объединение,
- `toList` - преобразование в список,
- `null` и `length` — проверка на пустоту и подсчёт элементов.

Пример:
```haskell
data User = User { name :: String, age :: Int }
-- Проверка, все ли пользователи старше 18
allAdults :: Foldable t => t User -> Bool
allAdults = all ((>= 18) . age)
```

Основными функциями `Traversable` являются:
- `traverse` - применяет функцию `a -> f b` к каждому элементу структуры и комбинирует результаты,
- `sequenceA` - частный случай `traverse` (`traverse id`):
- `mapM` и `sequence` - устаревшие функции (замена `traverse` и `sequenceA` соответственно) для `Monad`. 

Пример:
```haskell
doubled :: (Traversable t, Num a, Ord a) => t a -> Maybe (t a)
doubled = traverse $ \x -> if x >= 0 then Just(x*2) else Nothing
```
