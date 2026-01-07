**Тесты**

В haskell нет стандартных средств для написания тестов, но есть специализированные библиотеки, например **hspec**.
Далее приведена пошаговая установка для linux, если вы используете windows - сочувствую.

**Установка hspec**
```shell
cabal update && cabal install --package-env=. --lib hspec hspec-contrib QuickCheck HUnit
```
Далее добавляем его в path (в файле .bashrc)
```shell
export PATH="$HOME/.cabal/packages/hackage.haskell.org/:$PATH"
```

**Использование**
Создаём новый проект `example`. Переходим в каталог проекта и находим файл `example.cabal`. 
Находим секцию `test-suite example-test` и в `build-depends:` и дописываем зависимости
```
  hspec >= 2.7
, hspec-discover >= 2.7
```

Добавляем файл `src/MySum.hs`
```haskell
module MySum where

sum' :: Int -> Int -> Int
sum' x y = x + y
```

Добавляем файл `test/Spec.hs`
```haskell
{-# OPTIONS_GHC -F -pgmF hspec-discover #-}
```

Добавляем тестовый файл `test/MySumSpec.hs`
```haskell 
module MySumSpec (spec) where

import Test.Hspec
import MySum

spec :: Spec
spec = do
  describe "testing sum' function" $ do
    it "sum some two integers" $ do
      sum' 3 4 `shouldBe` 7
      sum' 2 1 `shouldBe` 3
      sum' -11 1 `shouldBe` -10
```
