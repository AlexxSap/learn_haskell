**Форматирование текста с fmt**

Для форматирования строк в haskell существует несколько библиотек, но сегодня наиболее распространёнными являются `fmt`, `PyF` и `formatting` - они типобезопасны и более выразительны. Сегодня рассмотрим работу с `fmt`. 

Библиотека `fmt` позволяет собрать шаблон строки для форматирования с помощью операторов интерполяции - оператор `+|` начинает вставку значения, а `|+` заканчивает её.
```haskell
{-# LANGUAGE OverloadedStrings #-}

module Main (main) where

import Fmt ((+|), (|+), fmt)
import Data.Text (Text)
import qualified Data.Text.IO as TIO

main :: IO ()
main = do
   let name = "Dude" :: Text
       age = 30 :: Int

   let message = fmt ("Hi " +| name |+", you're " +| age |+" years old.")
   TIO.putStrLn message -- Hi Dude, you're 30 years old.
```

Библиотека `fmt` содержит некоторые функции-модификаторы, которые можно использовать для отображения:
- `listF` - для форматирования списка,
- `padLeftF` и `padRightF` - для заполнения числа слева или справа выбранным символом до заданной ширины,
- `maybeF` - для форматирования значения `Maybe`.
```haskell
let msg = fmt ("Числа: "
                +| listF numbers |+
                ", Код: "
                +| padLeftF 5 '0' code |+
                " или "
                +| padRightF 5 '-' code |+
                " , Значение: "
                +| maybeF maybeVal |+
                "")
TIO.putStrLn msg --  Числа: [1, 2, 3, 4], Код: 00255 или 255-- , Значение: <Nothing>
```

Если нам нужно форматировано распечатать значение собственного типа, то он должен реализовывать класс типов `Buildable`. Функция `build` должна возвращать `Builder` (который автоматически преобразуется в `Text`):
```haskell
data User = User {
  userName :: Text,
  userAge  :: Int
}

instance Buildable User where
    build (User n a) = "User{ name=" 
            <> build n 
            <> ", age=" 
            <> build a 
            <> "}"
...
let user = User "Василий" 993
let msg = fmt("Данные пользователя: "  +| user |+ "")
TIO.putStrLn msg -- Данные пользователя: User{ name=Василий, age=993}
```
