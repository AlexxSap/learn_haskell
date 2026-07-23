**lucid**

это библиотека для генерации HTML страниц. Она предоставляет типобезопасный DSL для описания вэб-страниц и компонент прямо в коде.
`lucid` написана на чистом haskell (без синтаксических расширений), весь текст в ней автоматически экранируется (что является защитой от XSS).

В `lucid` есть два основных типа элементов:
- Теги: `div_`, `p_`, `h1_`, `a_` и т.д. - принимают два аргумента: список атрибутов и содержимое,
```haskell
-- Тег без атрибутов
div_ "Просто текст"

-- Тег с атрибутами и вложенным контентом (используем do-нотацию)
div_ [class_ "container", id_ "main"] $ do
    h1_ "Заголовок"
    p_ "Параграф"
```
- Атрибуты: `class_`, `id_`, `href_`, `style_` и т.д. - атрибуты всегда передаются списком.
```haskell
input_ [type_ "text", name_ "username", placeholder_ "Введите имя"]
```

Чтобы отреднерить созданную нами страницу Html в строку, используются функции:
- `renderText :: Html a -> Text` - рендеринг в Data.Text.Text,
- `renderBS :: Html a -> ByteString` - рендеринг в Data.ByteString.Lazy.ByteString,
- `renderTextT` и `renderBST` - версии для монады `ReaderT` и трансформеров, если вы используете их для передачи контекста.

Пример:
```haskell
{-# LANGUAGE OverloadedStrings #-}

module Main (main) where

import Lucid
import qualified Data.Text.Lazy.IO as TL

createPage :: Html ()
createPage = doctypehtml_ $ do
     head_ $ do
           title_ "simple web page"
           meta_ [charset_ "utf-8"]
           link_ [rel_ "stylesheet", href_ "style.css"]
     body_ $ do
           h1_ [class_ "main-title"] "hello"
           p_ $ do
                  "this text is "
                  strong_ "strong "
                  "and with "
                  a_ [href_ "https://haskell.org"] "link"
                  "."

main :: IO ()
main = do
     let htmlPage = renderText createPage
     TL.writeFile "sample.html" htmlPage
```
Получаем такой файлик:
```html
<!DOCTYPE HTML>
<html>
  <head>
    <title>simple web page</title>
    <meta charset="utf-8">
    <link rel="stylesheet" href="style.css">
  </head>
  <body>
    <h1 class="main-title">hello</h1>
    <p>this text is <strong>strong </strong>and with <a href="https://haskell.org">link</a>.</p>
  </body>
</html>
```

Чтобы рендерить собственные типы можно реализовать класс `ToHtml`:
```haskell
data User = User { 
    userName :: Text, 
    userAge :: Int 
}

instance ToHtml User where
  toHtml (User name age) = li_ $ do
    strong_ (toHtml name)
    " ("
    toHtml (show age) 
    " лет)"
  
  toHtmlRaw = toHtml

-- Использование:
ul_ $ mapM_ toHtml [User "Alice" 30, User "Bob" 25]
```
