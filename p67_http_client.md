**http-client**

Это распространённая библиотека для работы с http. 
`http_client` позволяет управлять соединениями, работать cookies и другими аспектами http. Библиотека поддерживает повторное использование соединений и пулинг сокетов, что оптимизирует производительность. 
Эта библиотека не поддерживает https, вмечто неё для работы с ним можно использовать `http-client-tls` или `http-client-openssl`. 

Чтобы выполнить простой запрос на сервер (который просто выдаёт строку `Hello web`) нужно:
1. Создать менеджер (`Manager`) соединения - это контроллер пула соединений, который отвечает за создание, переиспользование, кеширование и корректное завершение всех сетевых операций. 
2. Составить запрос через `parseRequest`. Эта функция автоматически распарсит запрос и заполнит недостающие поля значениями по умолчанию.
```haskell
parseRequest :: MonadIO m => String -> m Request
```
3. Отправить запрос через `httpLbs` и получить ответ:
```haskell
httpLbs :: Request -> Manager -> IO (Response L.ByteString)
```
Функции `parseRequest` и `httpLbs` бросают исключения, если что-то пошло не так, и их следует обрабатывать, но для примера такое не делается.

Пример:
```haskell
module Main (main) where

import Network.HTTP.Client
import Network.HTTP.Client.TLS (getGlobalManager)
import qualified Data.ByteString.Lazy as BL

main :: IO ()
main = do
  manager <- getGlobalManager
  request <- parseRequest "http://localhost:8080"
  response <- httpLbs request manager
  print $ responseStatus response -- Status {statusCode = 200, statusMessage = "OK"}
  print $ responseBody response  -- "Hello web"
```

Библиотека позволяет задавть тип запроса (через `setRequestMethod`), заголовок (`setRequestHeader`) и тело запроса (`setRequestBodyLBS`).

Другими популярными и более высокоуровневыми библиотеками для работы с http являются `req` (обеспечивающая большую типобезопасность и расширяемость) и `wreq` (использует линзы для доступа к данным запросов и ответов).
