**Endo**

это тип‑обёртка из модуля `Data.Monoid`, представляющая эндоморфизм.
Эндоморфизм - функция, которая принимает и возвращает значение одного типа: `a -> a`. 
Функции типа `a -> a` можно комбинировать с помощью оператора композиции `(.)`, а `id` служит нейтральным элементом. Это позволяет сделать `Endo` экземпляром класса типов `Monoid`.
```haskell
newtype Endo a = Endo { appEndo :: a -> a }
instance Monoid (Endo a) where
    mempty = Endo id
    Endo f <> Endo g = Endo (f . g)
```

Благодаря этому можно собирать коллекции обработчиков (эдноморфизмов) и применять их к значениям какого-то типа как единый объект:
```haskell
import Data.Monoid

main :: IO ()
main = do
    -- коллекция обработчиков
    let functions = [(+1), (*2), negate]
        -- объединяем эндофункции в одну - моноид по композиции
        combined = mconcat $ map Endo functions
        -- извлекаем и применяем
        result = appEndo combined 5
    print result  -- -9
```
