**Работа с JSON**

Для работы с json в haskell существует библиотека Aeson (названия как эсон и ясон из мифов древней греции).
Библиотека Aeson предоставляет функции для кодирования и декодирования json - `encode` и `decode`:
```haskell
decode :: FromJSON a => ByteString -> Maybe a
eitherDecode :: FromJSON a => ByteString -> Either String a
encode :: ToJSON a => a -> ByteString
```
Функция `decode` существует в двух вариантах - с `Maybe` и с `Either` - для разной детализации ошибок.

Для работы библиотеки нужно расширение `DeriveGeneric`, оно добавляет воддержку продвинутого обощённого программирования.

Допустим у нас есть простой тип, который мы хотим отобразить в json. Для этого нужно добавить добавить `Generic` в инструкцию `deriving` и сделать его экземпляром классов `FromJSON` и `ToJSON`. Если класс тривиален и нет конфликтов имён полей и метками в json, то это сделать просто:
```haskell
data Person = Person {
      name :: Text
    , age  :: Int
    } deriving (Show,Generic)
instance FromJSON Person
instance ToJSON Person
```

После этого конвертировать значение в json и обратно очень легко:
```haskell
let p1 = Person {name="Some", age=198}
let json = encode p1
print json -- "{\"age\":198,\"name\":\"Some\"}"
let p2::Maybe Person = decode json
print p2 -- Just (Person {name = "Some", age = 198})
```
Для того чтобы магия `Generics` правильно десериализовала строку json в значение нужного нам типа, нужно явно указать тип переменной через аннотацию типа.

Если нас не устраивает стандартное представление нашего типа в json, можно написать собственное инстанцирование классов `FromJSON` и `ToJSON`:
```haskell
instance FromJSON Person where
    parseJSON (Object v) =
        Person <$> v .: "name"
               <*> v .: "age"

instance ToJSON Person where
         toJSON (Person name age) =
            object [ "name" .= name
                    , "age" .= age
                   ]
```
Как можно заметить функция `parseJSON` основана на вызове конструктора нашего типа с помощью операторов `<$>` `<*>`, а функция `toJSON` - на сопоставлении с образцом, с добавлением операторов библиотеки Aeson.
