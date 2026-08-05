**HLint**

это инструмент статического анализа для haskell. HLint анализирует код на предмет идиоматичности, читаемости и лаконичности. Он предлагает советы (hints), как переписать код.
Установить его можно как через ghcup, cabal или stack, так и через пакетный менеджер ОС.
HLint запускается из командной строки или как часть HLS, тогда он сможет выдавать свои рекомендации прямо в IDE без ручного запуска.
```
# Проверка одного файла
hlint src/Main.hs
# Проверка всей директории
hlint src/
# Проверка всего проекта (текущей директории)
hlint .
```

Например, если у нас есть такая функция:
```haskell
isPositive :: Int -> Bool
isPositive x = if x > 0 then True else False
```
и на ней запустить:
```
hlint Main.hs
```
то нам будет предложено:
```
Main.hs:5:16-44: Warning: Redundant if
Found:
  if x > 0 then True else False
Perhaps:
  x > 0

1 hint
```

Hlint может автоматически исправить ваш код в соответствии со своими рекомендациями, если указать флаг `--refactor`:
```
hlint Main.hs --refactor --refactor-options="--inplace"
```
Для этого нужно установить пакет `apply-refact`.

Если предлагаемые рекомендации не подходят, то можно отключить конкретную проверку для конкретной строки с помощью специальной аннотации:
```haskell
{-# ANN module ("HLint: ignore Redundant if" :: String) #-}
```
или создать глобальный конфиг `.hlint.yaml`, например:
```
# Игнорировать правило "Redundant do" во всем проекте
- ignore: {name: Redundant do}

# Игнорировать правило "Use camelCase" только в модуле Test.Spec
- ignore: {name: Use camelCase, within: Test.Spec}
```
