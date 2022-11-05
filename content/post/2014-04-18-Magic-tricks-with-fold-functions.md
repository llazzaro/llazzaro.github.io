+++
title = "Magic tricks with fold functions"
date = "2014-04-18"
lang = "en"
+++


The first time you deal with foldr seems to be nothing more than a new function like map, filter.

However, when you get the concept of a recursion scheme, foldr becames a magical function.
Commonly recursion calls are like this one:

{{< highlight haskell >}}
sum:: Num a => [a] -> a
sum [] = 0
sum (x:xs) = x + (sum xs)
{{< / highlight >}}

Very easy and intuitive to read, but what if we try to compute prod?

{{< highlight haskell >}}
prod:: Num a => [a] -> a
prod [] = 1
prod (x:xs) = x * (sum xs)
{{< / highlight >}}

Nice! but we feel that this idea we wrote the same except for the base case and the function we want to use. Actually, in sum, we hardcoded "+" and in prod we hardcoded "*", the same with the base case.

This is where the recursion scheme comes in to save our life and using foldr we can avoid "hardcoding" the function and base case. Let's see some magic, simply magic to compute "sum":

{{< highlight haskell >}}
sum = foldr (+) 0
{{< / highlight >}}

yes! this new function "sum" does the same as the first one. The first time I saw this my brain was throwing exceptions...WTF is going on here? Well actually is pretty simple, but we need to look at the definition of foldr:

{{< highlight haskell >}}
foldr:: (a->b->b) -> b -> [a] -> b
foldr combine base [] = base
foldr combine base (x:xs) = combine x (foldr combine base xs)
{{< / highlight >}}

if we pass the list [1..13] to the latest version of sum, we can think that foldr will do this

{{< highlight haskell >}}
  (1+(2+(3+(4+(5+(6+(7+(8+(9+(10+(11+(12+(13+0)))))))))))))
{{< / highlight >}}

so we can think of foldr as a way to associate parenthesis in our "expressions". So, foldl instead will associate this way:

{{< highlight haskell >}}
  (((((((((((((0+1)+2)+3)+4)+5)+6)+7)+8)+9)+10)+11)+12)+13)
{{< / highlight >}}

In sum, there is no difference in the result for obvious reasons.

Another interesting problem to study is to try dropWhile with foldr. I read the solution of dropWhile (on the Monad.Reader issue 6) after trying many ideas. Let's look at my first idea:

let dropWhile' predicate = foldr (\x rec -> if predicate x then rec else x:rec) []
Let's try this with dropWhile':

{{< highlight haskell >}}
dropWhile' (<3) [1,1,1,1,3,4,5,6]
{{< / highlight >}}

Which returns [3,4,5,6] and I was pretty happy with this function until I tried:

{{< highlight haskell >}}
dropWhile' (odd) [1,1,1,1,3,4,5,6]
{{< / highlight >}}

Which returns [4,6] and it makes obvious where the error is. Well, the first working solution (and human understandable) that Monad. Reader suggests is to use high order.

let dropWhile'' predicate list= (foldr (\x rec -> if precidate x then rec.tail else id) id list) list
Yes! Magic again and a pretty cool idea of using the tail as a "filter" and id for leaving what we want.

But a different approach and that will be using primitive recursion...

{{< highlight haskell >}}
recr:: b->(a->[a]->b->b) -> [a] -> b
recr base combine [] = base
recr base combine (x:xs) = f x xs (recr base combine xs)
{{< / highlight >}}

And now let's define dropWhile with recr:

{{< highlight haskell >}}
dropWhile''' = recr [] (\x xs rec -> if length xs == 0 then [x] else rec)
{{< / highlight >}}

recr is not in the Prelude, not sure why. With recr is pretty is to write the function insert, which is pretty hard to write with fold!

{{< highlight haskell >}}
insert x = recr [x] (\y ys zs -> if x < y then (x:y:ys) else (y:zs))
{{< / highlight >}}
