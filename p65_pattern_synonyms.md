**Pattern synonyms**

Это расширение (`PatternSynonyms`), которое позволяет создавать именованные паттерны, которые можно использовать в pattern matching. При этом новый тип не создаётся, а создаётся абстракное представление над существующими типами.
Использование паттернов в определении функций через сопоставление с образцом значительно упрощает код и делает его более понятным. Кроме того, если паттерн достаточно сложный, это позволяет не ошибиться при его повторном использовании. 

Пример:
```haskell
data SomeType = SomeType String Int

func :: SomeType -> String
func (SomeType "this is some large string1" x) = "this is t1 = " ++ show x
func (SomeType "this is some large string2" x) = "this is t2 = " ++ show x
```

Если у нас ест тип `SomeType`, который нужно обрабатывать исходя из его содержания, то мы применяем сопоставление с образцом. Если таки функций как `func` достаточно много, то это будет страшно. Чтобы избежать этого можно создать паттерн для сопоставления и использовать его:
```haskell
pattern T1 t <- SomeType "this is some large string1" t
pattern T2 t <- SomeType "this is some large string2" t

funcWithPattern :: SomeType -> String
funcWithPattern (T1 x) = "this is t1 = " ++ show x
funcWithPattern (T2 x) = "this is t2 = " ++ show x
```

Это однонаправленные (Unidirectional) поттерны, которые можно использовать только для сопоставления с образцом. Они объявляются с помощью `<-`.
Есть еще двунаправленные/двусторонние (bidirectional) и явно двунаправленные (explicitly bidirectional) которые можно использовать еще и как конструкторы данных:
```haskell
-- двунаправленный паттерн 
pattern T1 :: Int -> SomeType -- можно явно указать тип 
pattern T1 t = SomeType "this is some large string1" t
...
funcWithPattern (T1 1)
```

Для объявления явно двунаправленного шаблона нужно указать как конструировать и деконструировать тип:
```haskell
pattern HeadC x <- x:xs where
  HeadC x = [x]
```
Такие паттерны обладают рядом преимуществ:
- могут создавать различные конструкторы данных на основе базового типа данных, а не только того, что указан в сопоставлении с образцом;
- могут вызывать любые функции или использовать условную логику, при условии, что она возвращает результат нужного типа;
- могут использовать охранные выражения в левой части оператора `=`;
- может содержать несколько условий.

Пример:
```haskell
data PosNeg = Pos Int | Neg Int
pattern Smarter{ nonneg } <- Pos nonneg  where
  Smarter x | x >= 0    = (Pos x)
            | otherwise = (Neg x)
```



