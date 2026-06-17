**Линзы**

Это удобный инструмент для работы с вложенными полями в структурах данных.

Если у нас есть такие типы:
```haskell
data Point = Point {
     _x :: Int,
     _y :: Int
} deriving (Show)

data Rectangle = Rectangle {
     _p1 :: Point,
     _p2 :: Point
} deriving (Show)
```
и нам нужно обновить координату X у точки P1. То мы пишем функцию:
```haskell
updateP1X :: Rectangle -> Int -> Rectangle
updateP1X r newX = r { _p1 = (_p1 r) {_x = newX}}
```
и так для каждого поля. Это довольно много функций. В случае изменения полей всё придётся переписывать или добавлять кучу функций для новых полей. Это жутко.

Линзы позволяют удобно решить эти проблемы:
```haskell
makeLenses ''Point
makeLenses ''Rectangle

updateP1XViaLens :: Rectangle -> Int -> Rectangle
updateP1XViaLens r newX = r & p1 . x .~ newX
```
Функция `makeLenses` автоматически создаёт линзы для всех полей указанного типа.

При использовании линз имена полей следует начинать с нижнего подчеркивания. Это такое соглашение для избегания конфликта имён - для поля `_p1` будет создана линза `p1`. Если такое не нравится или есть необходимость добавления какой-то логики в процедуру доступа к полю, то линзу можно создать вручную:
```haskell
p1XLens :: Lens' Rectangle Int
p1XLens = lens getter setter
  where
    getter rect = _x (_p1 rect)
    setter rect newX = rect { _p1 = (_p1 rect) {_x = newX}}

updateP1XViaLens' :: Rectangle -> Int -> Rectangle
updateP1XViaLens' r newX = r & p1XLens .~ newX
```

Три главные операции линз это:
- Чтение - `obj ^. lens` или `view lens obj`,
- Запись - `obj & lens .~ value` или `set lens value obj`,
- Применение - `obj & lens %~ func` или `over lens func obj`.

Можно выполнять композицию линз для одновременного изменения нескольких значений:
```haskell
updateP1 :: Rectangle -> Int -> Int -> Rectangle
updateP1 r newX newY = r & p1 . x .~ newX
                         & p1 . y .~ newY
```

Еще есть библиотека `microlens`. Это легковесная альтернатива `lens` - в ней гораздо меньше зависимостей, но почти весь тот же функционал с таким же синтаксисом.


