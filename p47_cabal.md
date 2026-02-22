**cabal**

Это пакетный менеджер и система сборки для haskell. Она помогает настраивать, собирать и устанавливать программное обеспечение на haskell, а также легко распространять его среди других пользователей и разработчиков.
cabal входит в состав ghcup.

Чтобы создать простое приложение с помощью cabal нужно набрать:
```shell
cabal init myapp --non-interactive
```
в результате будет создан каталог `myapp`:
```
└── myapp
 ├── app
 │ └── Main.hs
 ├── CHANGELOG.md
 └── myapp.cabal
```
С `Main.hs` всё понятно - это файл исходного кода с функцией `main`. Файл `myapp.cabal` содержит информацию о проекте (описание, версию, лицензию, автора и т.д.) и о сборке проекта:
```cabal
cabal-version:      3.16 -- текущая версия cabal
name:               myapp -- имя проекта
build-type:         Simple -- тип сборки
extra-doc-files:    CHANGELOG.md -- файл документации

common warnings -- именованая секция общих настроек
    ghc-options: -Wall -Werror -- флаги сборки для компилятора

executable myapp -- секция исполняемого файла
    import:           warnings -- добавление именованной секции настроек
    main-is:          Main.hs -- файл, содержащий функцию main 
    build-depends:    base ^>=4.22.0.0 -- зависимости проекта через запятую
        , containers >= 0.6 && < 0.8
    hs-source-dirs:   app -- папки где искать исходники
    default-language: Haskell2010 -- используемый стандарт языка
```

Для сборки проекта нужно ввести команду:
```shell
cabal build
```
для запуска:
```shell
cabal run myapp
```

Так как редактировать cabal-файл нужно ручками, то возникает потребность в его автоформатировании. Для этого существует утилита `cabal-gild`. 
```shell
cabal-gild --input myapp.cabal --output myapp.cabal
```
Вызов такой команды отформатирует и перезапишет файл `myapp.cabal`.
