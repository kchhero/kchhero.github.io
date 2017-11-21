---
title: clojure '4-clojure problem'
layout: post
tags:
  - Language
category: language
---
### http://www.4clojure.com/ 문제 풀이 요약

#### 24
```
(= (#(loop [result 0 x %]
        (if (empty? x)
          result
          (recur (+ result (first x)) (rest x))))
        '(-1 0 2 3 5 5)) 14)
```

#### 71
The -> macro threads an expression x through a variable number of forms. First, x is inserted as the second item in the first form, making a list of it if it is not a list already. Then the first form is inserted as the second item in the second form, making a list of that form if necessary. This process continues for all the forms. Using -> can sometimes make your code more readable.
```
user> (-> [2 5 4 1 3 6]
          reverse
          rest
          sort
          last)
5
```

#### 72
The ->> macro threads an expression x through a variable number of forms. First, x is inserted as the last item in the first form, making a list of it if it is not a list already. Then the first form is inserted as the last item in the second form, making a list of that form if necessary. This process continues for all the forms. Using ->> can sometimes make your code more readable
```
user> (->> [2 5 4 1 3 6]
          (drop 2)
          (take 3)
          (map inc)
          (reduce +))
11
```
