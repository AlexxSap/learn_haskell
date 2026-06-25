**ISO (изоморфизмы)**

Изоморфизмы в библиотеке `lens` позволяют проводить преобразование между типами в обе стороны без потерь. Для создания изоморфизма используется функция `iso`:
```haskell
iso :: forall s a b t. (s -> a) -> (b -> t) -> Iso s t a b
```

Естественно изоморфизмы работают совместно с линзами:
```haskell
module Main (main) where

import Control.Lens

data UserID = UserID {getID :: Int} deriving Show

_UserID :: Iso' UserID Int
_UserID = iso getID UserID

main :: IO ()
main = do
    let id = UserID 3
    print id                       -- UserID {getID = 3}
    print $ id ^. _UserID          -- 3
    print $ _UserID # 42           -- UserID {getID = 42}
    print $ id & _UserID %~ (+666) -- UserID {getID = 669}
```

В библиотеке `lens` уже содержится много готовых изоморфизмов:
- `_Text: String <-> Text`
- `_String: Text <-> String` - то же самое, что `_Text`, но симметрично
- `_List: Seq a <-> [a]`
- `_Show: Show a => a <-> String` - через функции `show` и `read`.

Изоморфизмы удобно использовать в качестве адаптеров данных, меняя тип для разных ситуаций, однако стоит помнить, что часто для этого может хватать стандартного сопоставления с образцом (см. `Boring Haskell`).
