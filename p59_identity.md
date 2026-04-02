**Стандартная монада Identity**

Это минимальная, тождественная монада, которая не добавляет никакого дополнительного поведения или эффектов. То есть, эта монада, по сути, не меняет ни тип значений, ни стратегию связывания вычислений.
```haskell
instance Monad Identity where
    return x = Identity x
    (Identity x) >>= f = f x
```

Сама она реализована как просто обёртка вокруг значения:
```haskell
newtype Identity a = Identity { runIdentity :: a }
  deriving (Eq, Ord, Show, Read, Functor, Applicative, Monad)
```

`Identity` может использоваться для абстрагирования от конкретных монад в процессе разработки и в тестировании. Так же она используется с монадными трансформерами.
`Identity` - это способ притвориться, что у вас есть монада, но на самом деле выполнить чистый код. 

Например:
```haskell
mport Data.Functor.Identity (runIdentity)

calculate :: Monad m => Int -> m Int
...

pureResult :: Int
pureResult = runIdentity (calculate 42) 
```
если у нас есть функция `calculate`, которая работает с монадой `m`, но мы пока её не конкретизируем и используем для этого `Identity`.

