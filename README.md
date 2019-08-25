## Chapter 2. Basic Parallelism: The Eval Monad

### Lazy Evaluation and Weak Head Normal Form 

```
λ: let x = 1 + 2 :: Int
λ: let y = x + 1
λ: :sprint x
x = _
λ: :sprint y
y = _
λ: seq y ()
()
λ: :sprint x
x = 3
λ: :sprint y
y = 4
```

```
λ: let x = 1 + 2 :: Int
λ: let z = (x,x)
λ: :sprint z
z = (_,_)
```
The variable z itself refers to the pair (x,x), but the components of the pair both point to the unevaluated thunk for x


```
λ: import Data.Tuple
λ: let z = swap (x,x+1)   -- swap (a,b) = (b,a)
λ: :sprint z
z = _
λ: seq z ()
()
λ: :sprint z
z = (_,_)
```

* Applying seq to z caused it to be evaluated to a pair, but the components of the pair are still unevaluated.
* The seq function evaluates its argument only as far as the first constructor, and doesn’t evaluate any more of the structure.
* We say that seq evaluates its first argument to **weak head normal form**.


### The Eval Monad, rpar, and rseq

```
 runEval $ do
     a <- rpar (f x)
     b <- rpar (f y)
     rseq a
     rseq b
     return (a,b)
```

#### Run Parallel Fibonacci examples with 2 cores

```
stack run rpar-exe 0 --RTS +RTS -N2
stack run rpar-exe 1 --RTS +RTS -N2
stack run rpar-exe 2 --RTS +RTS -N2
stack run rpar-exe 3 --RTS +RTS -N2
```


#### Sequential Sudoku example

```
stack run sudoku1-exe sudoku17.1000.txt --RTS -- +RTS -s
```

#### Parallel Sudoku example

```
stack run sudoku2-exe sudoku17.1000.txt --RTS -- +RTS -N2 -s
```

#### Analyse the performance using ThreadScope

```
stack run sudoku2-exe sudoku17.1000.txt --RTS -- +RTS -N2 -l
```

```
threadscope sudoku2-exe.eventlog
```



