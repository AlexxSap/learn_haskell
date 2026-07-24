**colonnade**

это библиотека для представления табличных данных. Она позволяет отделить структуру таблицы от представления данных и отобразить её в разных вариантах - html (в связке с `lucid` через библиотеку `lucid-colonnade`) или csv (в связке с `cassava`).

Задать структуру таблицы нужно через тип `Colonnade` - который представляет коллекцию колонок:
`Colonnade head a c` , где:
- `head` — тип, указывающий, есть ли у таблицы заголовки - `Headed` или `Headless`,
- `a` — тип строки (наши данные, например, User),
- `c` — тип ячейки (то, во что мы извлекаем данные, например, `Text` или `Html ()`).

Отрендерить нашу таблицу в html можно с помощью функции `encodeHtmlTable`, которая принимает список аттрибутов (который может быть пуст - `[]`), структуру таблицы и данные для отображения.
```haskell
encodeHtmlTable :: forall (h :: Type -> Type) (f :: Type -> Type) d a. (Headedness h, Foldable f, Monoid d) => [Attribute] -> Colonnade h a (Html d) -> f a -> Html d
```

Пример с использованием библиотеки `lucid`:
```haskell
{-# LANGUAGE OverloadedStrings #-}

module Main (main) where

import Colonnade
import Lucid.Colonnade
import Lucid
import Data.Text (Text)
import qualified Data.Text.Lazy.IO as TL

data User = User {
     userID :: Int,
     name :: Text,
     age :: Int
} deriving Show

userTable :: Colonnade Headed User (Html ()) 
userTable = headed "ID"   (fmap (toHtml . show) userID)
         <> headed "Name" (fmap toHtml name)
         <> headed "Age"  (fmap (toHtml . show) age)

renderUsers :: [User] -> Html ()
renderUsers users = encodeHtmlTable [class_ "striped"] userTable users 

main :: IO ()
main = do
     let users = [ User 1 "user1" 11,
                   User 2 "user2" 22,
                   User 3 "user3" 33 ]
     let htmlText = renderText $ renderUsers users
     TL.writeFile "table.html" htmlText 

```
Выдаст нам файл:
```html
<table class="striped">
  <thead>
    <tr>
      <th>ID</th><th>Name</th><th>Age</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td><td>user1</td><td>11</td>
    </tr>
    <tr>
      <td>2</td><td>user2</td><td>22</td>
    </tr>
    <tr><td>3</td><td>user3</td><td>33</td>
    </tr>
  </tbody>
</table>
```
