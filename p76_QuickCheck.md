**QuickCheck**

Это библиотека для автоматического property-based тестирования. 
Вместо ручного написания конкретных тестов мы формулирем инвариант, а `QuickCheck` автоматически генерирует случайные входные данные и выполняет вычисление.
Чтобы использовать `QuickCheck` нужно добавить его в зависимости (build-depends) проекта. Сейчас последняя версия - 2.14.2. 
Так же его можно использовать через ghci, но для этого нужно его отдельно установить:
```shell
cabal install --lib QuickCheck
```

Допустим у нас есть файл `prop.hs` в котором есть функция `prop`:
```haskell
prop :: [Int] -> Bool
prop xs = reverse (reverse xs) == xs
```

Далее запускаем ghci и выполняем:
```shell
ghci> import Test.QuickCheck
ghci> :l prop
Ok, one module loaded.
ghci> quickCheck prop
+++ OK, passed 100 tests.
```

Видно, что QuickCheck сформировал 100 вариантов входных данных и выполнил `prop` на них. Чтобы совсем было всё видно, можно вызвать проверку через `verboseCheck prop` и тогда будет выведен каждый кейс проверки с входными данными.

Еще можно использовать сразу лямбды в вызове, что позволяет не писать отдельную функцию для сравнения:
```shell
ghci> quickCheck ((\s -> s == s) :: [Char] -> Bool)
+++ OK, passed 100 tests.
```

Если нам интересна статистика входных даннных, то можно использовать функции `collect` (которая выведет статистику входных данных по выбранному показателю) или `classify` (которая покажет из этих показателей конкретный вариант):
```shell
ghci> import Test.QuickCheck
ghci> prop xs = collect (length xs) $  reverse (reverse xs) == xs
ghci> quickCheck prop
+++ OK, passed 100 tests:
 ...
 7% 0
 ...
ghci> prop xs = classify (length xs == 0) "empty" $  reverse (reverse xs) == xs
ghci> quickCheck prop
+++ OK, passed 100 tests (7% empty).
```
тут видно, что 7% всех входных данных - это пустой список. Нам такое не интересно и подобные варианты можно отфильтровать с помощью оператора `==>`:
```haskell
ghci> prop xs = classify (length xs == 0) "empty" $ (length xs > 0) ==> reverse (reverse xs) == xs
ghci> quickCheck prop
+++ OK, passed 100 tests; 17 discarded (0% empty).
```

