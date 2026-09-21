**Создание cli с помощью optparse-applicative**

`optparse-applicative` это популярная библиотека для парсинга опций командной строки. Она позволяет описывать разбор опций декларативно и может автоматически сгенерировать справку по команде `--help`.

Основой библиотеки является тип `Parser` - это апптикативный функтор, который позволяет комбинировать различные парсеры аргументов, такие как:
- `strOption` - строковое значение,
- `switch` - булев флаг,
- `auto` - для чисел или других типов, для которых настроен читатель (reader),
- `flag` - флаги с перечисляемым значением.

Допустим у нас есть тип `Options`, который хранит настройки:
```haskell
data Options = Options
    { optName :: String
    , optLoud :: Bool
    }
    deriving (Show)
```

Для неё мы создаём парсер:
```haskell
optionsParser :: Parser Options
optionsParser =
    Options
        <$> strOption
            ( long "name"
                <> short 'n'
                <> metavar "NAME"
                <> help "Имя пользователя"
            )
        <*> switch
            ( long "loud"
                <> short 'l'
                <> help "Кричать"
            )
```

Можно задать следующие модификаторы:
- `long "name"` - длинное имя (`--name`),
- `short 'n'` - короткое имя (`-n`),
- `metavar "NAME"` -  Имя переменной в справке,
- `help "..."` - описание для справки,
- `value "default"` - значение по умолчанию (делает опцию необязательной).

Для того, чтобы по команде `--help` выдавалась нормальное описание нужно обязательно добавить `helper` с описанием:
```haskell
parserWithHelp :: ParserInfo Options
parserWithHelp =
    info
        (helper <*> optionsParser)
        ( fullDesc
            <> progDesc "Простая программа для приветствия"
            <> header "greeter - утилита для тестирования optparse-applicative"
        )
```

Далее нужно запустить парсер:
```haskell
greet :: Options -> IO ()
greet opts = putStrLn $ if optLoud opts then map toUpper greeting else greeting
  where
    greeting = "Привет, " ++ optName opts ++ "!"

main :: IO ()
main = do
    opts <- execParser parserWithHelp
    greet opts
```

Далее смотрим help:
```shell
❯ cabal run optparser -- --help
greeter - утилита для тестирования optparse-applicative

Usage: optparser (-n|--name NAME) [-l|--loud]

  Простая программа для приветствия

Available options:
  -h,--help                Show this help text
  -n,--name NAME           Имя пользователя
  -l,--loud                Кричать приветствие
```

и используем:
```shell
❯ cabal run optparser -- -n Василий -l
ПРИВЕТ, ВАСИЛИЙ!
```
