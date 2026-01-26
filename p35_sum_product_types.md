**Тип-сумма и тип-произведение**

Тип-сумма (sum type) и тип-произведение (product type) — это фундаментальные концепции из теории типов, которые отражают структуру составных типов данных в haskell.

Типы-произведения определяются посредством комбинирования двух или более существующих типов с помощью «И»:
```haskell 
data AuthorName = AuthorName String String
```

это аналог простых структур в императивных языках, например в `C`:
```c
struct author_name {
    char *first_name;
    char *last_name;
};
```

В большинстве языков это единственный способ группировки типов в структуры.

Типы-суммы позволяют комбинировать типы с помощью «ИЛИ». Самым понятным примером тут является тип `Bool` который может принимать одно из двух значений - `True` или `False` (вот оно ИЛИ тут и проявляется).
```haskell
data Bool = False | True
```

Типы-суммы позволяют создавать типы набор полей которых может быть различен, но это не мешает им оставаться одним и тем же типом. Например тип имени можно представить как "имя + фамилия" или "имя+отчество+фамилия" или "инициалы+фамилия":
```haskell
type FirstName = String
type LastName = String
type MiddleName = String

data Name = Name FirstName LastName
    | NameWithMiddle FirstName MiddleName LastName
    | TwoInitialsWithLast Char Char LastName
```

Чтобы получить доступ к полю каждого возможного варианта типа нужно указать имя конструктора при определении функции:
```haskell
lastName :: Name -> String
lastName (Name _ n) = n
lastName (NameWithMiddle _ _ n) = n
lastName (TwoInitialsWithLast _ _ n) = n
```
