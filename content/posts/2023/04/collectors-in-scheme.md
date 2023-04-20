---
title: "Collectors in Scheme"
date: 2023-04-20T09:47:49-07:00
categories: [code, scheme, racket, fp]
---

Collector functions are a functional way of processing and accumulating collections of data. I recently encountered this pattern while going through the book [The Little Schemer](https://mitpress.mit.edu/9780262560993/the-little-schemer/). In the discussion of lambdas, there's a function named `multirember&co`, which stands for *"remove multiple members and collect"*. Basically, it removes multiple elements from a list and collects the removed elements."

<!--more-->

This function is a good, nontrivial example of collector functions. Once I worked through it, I decided writing a short blog post about it would be helpful so others could benefit.


```scheme
(define multirember&co
  (lambda (a lat col)
    (cond
      ; (1) Base case. Collect empty lists.
      ((null? lat) (col (quote ()) (quote ())))
      ; (2) The head of the list is equal to `a`. Collect it in `seen`.
      ((eq? a (car lat))
       (multirember&co a
                       (cdr lat)
                       (lambda (newlat seen)
                         (col newlat (cons (car lat) seen)))))
      ; (3) The head of the list is not equal to `a`. Collect it in `newlat`.
      (else
       (multirember&co a
                       (cdr lat)
                       (lambda (newlat seen)
                         (col (cons (car lat) newlat) seen)))))))
```

This function has three arguments: the value `a` to be removed from the list, the list `lat`, and a collector function `col`. It looks at every element of the list `lat` to see if it is equal to `a`. If so, it collects them in a list (`seen` ). If not, it collects them in another list (`newlat`).

The recurrence for this function at first seems complicated. At each step, this function recurs by building a new lambda function that uses the given collector to prepend an element to one of the lists. It then passes this new lambda down to the next call of `multirember&co`.

Let's walk through the expansion of `(multirember&co 'b '(a b c b) list)`. I will name the collector lambda at each step to make it easier to see what's happening. At each step, comments describe what's going on with the expansion:

```scheme
; Initial: Name the collector that's passed in as `col-0`
(define col-0 list)

; Step-1:
(multirember&co 'b '(a b c b) col-0)

; `b` does not match the head of the list `a`. The code builds a
; new lambda for recursion. Call it `col-1`.
(define col-1
	(lambda (newlat seen)
		(col-0 (cons 'a newlat) seen)))


; Step-2:
(multirember&co 'b '(b c b) col-1)

; `b` matches the head of the list. Name the new lambda for the
; recursion as `col-2`.
(define col-2
  (lambda (newlat seen)
	  (col-1 newlat (cons 'b seen))))


; Step-3:
(multirember&co 'b '(c b) col-2)

; `b` does not match the head of the list. Name the new lambda
; for the recursion as `col-3`.
(define col-3
  (lambda (newlat seen)
	  (col-2 (cons 'c newlat) seen)))


; Step-4:
(multirember&co 'b '(b) col-3)

; `b` matches the head of the list. Name the new lambda for the
; recursion as `col-4`:
(define col-4
  (lambda (newlat seen)
	  (col-3 newlat (cons 'b seen))))


; Step-5:
(multirember&co 'b '() col-4)
((null? lat) (col-4 '() '()))
```

At the last step, the stack of function calls logically looks as follows:

```scheme
(col-4 '() '())
;|_____|
;   |
;   |
(define col-4
	(lambda (newlat seen)
		(col-3 newlat (cons 'b seen))))
	;   |_____|
	;      |
	;      |
		(define col-3
			(lambda (newlat seen)
				(col-2 (cons 'c newlat) seen)))
			;   |_____|
			;      |
			;      |
				(define col-2
					(lambda (newlat seen)
						(col-1 newlat (cons 'b seen))))
					;   |_____|
					;      |
					;      |
						(define col-1
							(lambda (newlat seen)
								(col-0 (cons 'a newlat) seen)))
							;   |_____|
							;      |
							;      |
								(define col-0 list)
```

Without the labels, the actual expansion (below) is much harder to parse and understand!

```scheme
((lambda (newlat4 seen4) ; col-4
   ((lambda (newlat3 seen3) ; col-3
      ((lambda (newlat2 seen2) ; col-2
         ((lambda (newlat1 seen1); col-1

            (col-0 (cons 'a newlat1) seen1)) ; arguments to col-0
          newlat2 (cons 'b seen2))) ; arguments to col-1
       (cons 'c newlat3) seen3)) ; arguments to col-2
    newlat4 (cons 'b seen4))) ; arguments to col-3

 '() '()
 )
```

Executing the above block results in `'((a c) (b b))`, which is what we expect.

Another way of looking at the expansion above is with the computed arguments for each lambda:

```scheme
(col-4 '() '())
(col-3 '() '(b))
(col-2 '(c) '(b))
(col-1 '(c) '(b b))
(col-0 '(a c) '(b b))

; `col-0` is `list`
(list '(a c) '(b b))
('(a c) '(b b))
```


Hopefully this expansion of `multirember&co` gives a clearer idea of how collector functions behave and are used in Scheme. This analysis applies generally to other functional programming languages as well.