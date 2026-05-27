**NamedFieldPuns и RecordWildCards**

`Record puns` это синтаксический сахар, который позволяет не дублировать имена полей при конструировании или сопоставлении с образцом, если имена локальных переменных полностью совпадают с именами полей записи. Добавляется расширением `NamedFieldPuns`, которое с `GHC 8` включено по умолчанию.
Использование `record puns` позволяет уменьшить количество кода, а значит потенциальное число ошибок и опечаток.

Пример:
```haskell
data Person = Person {
     name :: String,
     age  :: Int
} deriving Show

newPerson :: String -> Int -> Person
newPerson n a = Person {name = n, age = a}

newPersonWithPuns :: String -> Int -> Person
newPersonWithPuns name age = Person {name, age}
```
Запись `{ name, age }` автоматически раскрывается в `{ name = name, age = age }`.

Схожее поведение добавляет расширение `RecordWildCards` конструкцией `{..}`. При этом автоматически все поля записи разворачиваются в локальные переменные. Это удобнее, но менее безопасно (может неожиданно захватить поля, о которых забыли, или создать конфликт имён), наверно поэтому это расширение не включено по умолчанию.
```haskell
newPersonWithWildCard :: String -> Int -> Person
newPersonWithWildCard name age = Person {..}
```

Точно так же оба эти расширения работают в сопоставлении с образцом:
```haskell
describeWithPuns :: Person -> String
describeWithPuns Person {name, age} = "Hello " ++ name ++ " you are " ++ show age

describeWithWildCard :: Person -> String
describeWithWildCard Person {..} = "Hello " ++ name ++ " you are " ++ show age
```

