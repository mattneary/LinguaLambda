---
layout: post
title:  "Translating Programs from Imperative to Functional Style"
date:   2013-08-08 03:23:00
categories: style functional
excerpt: "There are two basic approaches to programming, derived from the two original theories of computation. The approach more familiar to most programmers is imperative; however, this is mostly due to the prevalence of certain languages and perhaps the less steep learning curve of the approach. Functional programming is just as robust, often more succinct, and almost always renders a more easily tested product."
---

The Two Styles
--------------
There are two basic approaches to programming, derived from the two original theories of computation. The approach more familiar to most programmers is imperative; however, this is mostly due to the prevalence of certain languages and perhaps the less steep learning curve of the approach. Functional programming is just as robust, often more succinct, and almost always renders a more easily tested product.


A Basic Example
---------------
The following is a factorial function implemented in C. The result is achieved by defining a sequence of numbers over which to iterate, and then upon each step multiplying an accumulator by one of those numbers.

{% highlight c %}
int fact(int num) {
	int ans = 1;
	for( int i = 1; i <= num; i++ ) {
		ans *= i;
	}
	return ans;
}
int main() {
	printf("5!: %d", fact(5));
}
{% endhighlight %}

The following solves the same problem, but this example is written in Haskell. Haskell is a functional language that accommodates our now recursive definition nicely. The following program is much like a mathematical definition of the factorial.

{% highlight c %}
fact 0 = 1
fact n = n * (fact $ n - 1)
main = putStrLn $ "5!: " ++ (show $ fact 5)
{% endhighlight %}

The above closely resembles a Mathematic definition, which is very a much characteristic of functional programs. Their declarative definition results in communication not of a process of computation, but rather, a definition of a relation.

It should be noted that recursion is not limited to those few languages that are perfectly functional; rather, recursion, as well as all of the general principles of functional programming, may be utilized in your language of choice.


Translating an Algorithm
------------------------
Now we will jump right in to a non-trivial problem, stated below.

	Given a string of nested parenthesized groups, return a nested 
	list containing the contents of these groups. For example, given 
	the list of characters `a(bc)d` return `[a, [b, c], d]`.

We will begin with an imperative approach. Thus think, what is the easily defined iterative process underlying this problem? The answer is clearly navigation of the string, and so we begin with a `for` loop that will cycle through each character of the string in order.

{% highlight javascript %}
var parse = function(expr) {
	for( var i = 0; i < expr.length; i++ ) {
		var read = expr[i];
		// ...
	}
}
{% endhighlight %}

Now we will need to describe a slightly more specific strategy in performing the desired process.

1. A parenthetical will be split from the string, with a segment, although possibly an empty one, before and after it.
2. Once a parenthetical has been removed, we will need to recurse on these segments, i.e., the parenthetical and the portion after it.

To make our way toward this implementation, we will define a variable `before` that will hold the segment of the string occurring prior to any parenthetical; a variable `accum` that will hold characters that have been read in but whose destination has yet to be determined, in this way serving as a cache; `paren` which will hold a separated out parenthetical; and `found` which will be true if and only if a parenthetical has been parsed.

{% highlight javascript %}
var parse = function(expr) {
	var before,
		accum = [],
		paren,
		found = false;		
	for( var i = 0; i < expr.length; i++ ) {
		var read = expr[i];
		// ...
	}
}
{% endhighlight %}

In order to parse out the parenthetical, however, we will need an additional variable. This variable will aid us in parsing nested parentheses to separate out the top-level parenthetical.

We will need to handle three obvious classes of characters in our parsing of the parentheses:

1. An opening parenthesis.
2. A closing parenthesis.
3. Any other character.

Additionally, the class of a character may be disregarded if we have already parsed a top-level parenthetical. Its parsing will be handled when we are ready to recurse. Given these additions of case-handling, we insert `if ... else` statements as in the following.

{% highlight javascript %}
var parse = function(expr) {
	var nested = 0,
		before,
		paren,
		accum = [],
		found = false;
	for( var i = 0; i < expr.length; i++ ) {
		var read = expr[i];
		if( read == '(' && !found ) {
			// Class 1. An opening parenthesis.
			// ...
		} else if( read == ')' && !found ) {
			// Class 2. A closing parenthesis.
			// ...
		} else {
			// Class 3. Any other character.
			// ...
		}
	}
};
{% endhighlight %}

Of course, we will need to combine any separated out parenthetical with the components occurring before and after it to form the designated response. Hence we provide the following `return` statement.

{% highlight javascript %}
var parse = function(expr) {
	// variables...
	for( var i = 0; i < expr.length; i++ ) {
		// parse...
	}
	if( paren ) {
		return before.concat([paren]).concat(parse(accum));
	} else {
		return expr;
	}
};
{% endhighlight %}

Now we implement our nesting logic and the final algorithm. Nesting will be handled based on one of the following occurences.

1. A once nested expression was just opened.
2. An expression was just closed to be un-nested.
3. Parentheses occurred within a nested expression.

Cases `1.` and `2.` are handled under the conditionals for their respective character classes, and in either class under another nesting case case `3.` is handled.

The last components missing from our implementation are the building up of an accumulator and the setting of the various components to the accumulator. We will implement these portions in the following code.

- When the parenthetical is closed, it is recursively `parsed` and set to the `paren` variable.
- When a parenthetical is open, `before` receives the accumulator value.

{% highlight javascript %}
var parse = function(expr) {
	var nested = 0,
		before,
		paren,
		accum = [],
		found = false;
	for( var i = 0; i < expr.length; i++ ) {
		var read = expr[i];
		if( read == '(' && !found ) {
			nested++;
			if( nested == 1 ) {
				before = accum;
				accum = [];
			} else {
				accum.push(read);
			}
		} else if( read == ')' && !found ) {
			nested--;
			if( nested == 0 ) {
				found = true;
				paren = parse(accum);
				accum = [];
			} else {
				accum.push(read);
			}
		} else {
			accum.push(read);
		}
	}
	if( paren ) {
		return before.concat([paren]).concat(parse(accum));
	} else {
		return expr;
	}		
};
{% endhighlight %}

From the above final implementation of our program we can derive a functional version. The differences will be based on the following principles of functional programming:

1. Values shall not be mutated.
2. Control-flow shall not be explicit.
3. Recursion is dope.

Let's begin by abiding to rule `2`, inspired by principle number three. The first thing you will notice is that all variables were made function arguments. This is because in a pure function, the only state is provided by the arguments. Hence when recursing, we will need to pass all required data to the function as argument.

Also of note is the fact that rather than maintain an index of the list on which we are operating, we pass as argument to the recursive call only subsequent characters, i.e., those which have yet to be read. This is both logical in that our progress in navigating the list is maintained, and idiomatic in that Lisp, for example, provides a function for trailing values of a list, `cdr`, as well as a function to get the first element, `car`.

{% highlight javascript %}
var fparse = function(expr, nested, before, paren, accum, found) {
	if( expr.length == 0 ) {
		if( paren ) {
			return before.concat([paren]).concat(parse(accum));
		} else {
			return expr;
		}
	} else {
		var read = expr[0];
		if( read == '(' && !found ) {
			nested++;
			if( nested == 1 ) {
				before = accum;
				accum = [];
			} else {
				accum.push(read);
			}
		} else if( read == ')' && !found ) {
			nested--;
			if( nested == 0 ) {
				found = true;
				paren = parse(accum);
				accum = [];
			} else {
				accum.push(read);
			}
		} else {
			accum.push(read);
		}
		return fparse(expr.slice(1), nested, before, paren, accum, found);
	}
};
var fparse_ = function(expr) {
	return fparse(expr, 0, nil, nil, nil, false);
};
{% endhighlight %}

The final portion of our program includes a definition of `fparse_`. This was merely for convenience, as `fparse_` provides all of the initialization values as argument to `fparse`.

We now remove mutation to achieve implementation of the final principle we listed. Our means of achieving this is by allowing all values to be function arguments or expressions operating on arguments.

{% highlight javascript %}
var fparse = function(expr, nested, before, paren, accum, found) {
	if( expr.length == 0 ) {
		if( paren ) {
			return before.concat([paren]).concat(parse(accum));
		} else {
			return expr;
		}
	} else {
		if( expr[0] == '(' && !found ) {
			if( nested == 0 ) {
				return fparse(
				  expr.slice(1), 
				  nested+1, accum, 
				  paren, [], found);
			} else {
				return fparse(
				  expr.slice(1), 
				  nested+1, 
				  before, paren, 
				  accum.concat(expr[0]), found);
			}
		} else if( expr[0] == ')' && !found ) {
			if( nested == 1 ) {
				return fparse(
				  expr.slice(1), 
				  nested-1, 
				  before, parse(accum), 
				  [], true);
			} else {
				return fparse(
				  expr.slice(1), 
				  nested-1, 
				  before, paren, 
				  accum.concat(expr[0]), found);
			}
		} else {
			return fparse(
			  expr.slice(1), 
			  nested, 
			  before, paren, 
			  accum.concat(expr[0]), found);
		}		
	}
};
var fparse_ = function(expr) {
	return fparse(expr, 0, nil, nil, nil, false);
};
{% endhighlight %}

You should begin to see how our rewrite of this algorithm reads much more as an inductive definition than as a description of a process. In the following section we will make this even more evident.

Approaching a Functional Language
---------------------------------
We have successfully implemented our algorithm while following the principles of functional programming. Now let's consider definitions which will enable us to separate ourselves from the Object-Oriented foundation of our current language of operation. A few of these will have the potential to serve as *the* primitive functions of a functional language; however, we will not bother to take the route of derivation from first elements in this post.

We begin by defining functions for nearly all of the operations we performed above.

{% highlight javascript %}
var nil = [];
var isNull = function(x) { return x.length == 0; };
var push = function(a, b) { return a.concat([b]); };
var concat = function(a, b) { return a.concat(b); };
var car = function(x) { return x[0]; };
var cdr = function(x) { return x.slice(1); };
var add = function(a, b) { return a+b; };
var sub = function(a, b) { return a-b; };
var eq = function(a, b) { return a==b; };
var and = function(a, b) { return a && b; };
var not = function(a) { return !a; };
{% endhighlight %}

Now we incorporate these functions into our previous program.

{% highlight javascript %}
var fparse = function(expr, nested, before, paren, accum, found) {
	if( isNull(expr) )
		if( not(isNull(paren)) )
			return concat(push(before, paren), fparse_(accum));
			else return accum;
	else
		if( and(eq(car(expr), '('), not(found)) )
			if( eq(nested, 0) )
				return fparse(
				  cdr(expr), 
				  add(nested, 1), 
				  accum, paren, 
				  nil, found);
				else return fparse(
				  cdr(expr), 
				  add(nested, 1), 
				  before, paren, 
				  push(accum, car(expr)), found);
		else if( and(eq(car(expr), ')'), not(found)) )
			if( eq(nested, 1) )
				return fparse(
				  cdr(expr), 
				  sub(nested, 1), 
				  before, fparse_(accum), 
				  nil, true);
				else return fparse(
				  cdr(expr), sub(nested, 1), 
				  before, paren, 
				  push(accum, car(expr)), found);
		else
			return fparse(
			  cdr(expr), nested, 
			  before, paren, 
			  push(accum, car(expr)), found);		
};
var fparse_ = function(expr) {
	return fparse(expr, 0, nil, nil, nil, false);
};
{% endhighlight %}

The above is more readable to those familiar with the naming scheme of the functions, and more importantly, more homoiconic. We have removed the remnants of an Object-Oriented Language to leave only function application, `return` statements, and conditionals.

Writing in Functional Languages
-------------------------------
The prior definitions of a prelude of functions was intentionally very much like Lisp or Scheme. We will now translate the code into Scheme, and you will see that it is nearly identical to our prior JavaScript.

{% highlight scheme %}
(define (fparse expr nested before paren accum found)
  (if
    (null? expr)
    (if
      (not (null? paren))
      (concat (push before paren) (fparse_ accum))
      accum)
    (cond (((and (eqv? (car expr) 'po) (not found))
            (if
             (eqv? nested 0)
             (fparse (cdr expr) (+ nested 1) accum paren '() found)
             (fparse (cdr expr) (+ nested 1) before paren (push accum (car expr)) found))) 
           ((and (eqv? (car expr) 'pc) (not found))
            (if
             (eqv? nested 1)
             (fparse (cdr expr) (- nested 1) before (fparse_ accum) '() #t)
             (fparse (cdr expr) (- nested 1) before paren (push accum (car expr)) found)))
           (#t (fparse (cdr expr) nested before paren (push accum (car expr)) found))))))
(define (fparse_ expr)
  (fparse expr 0 '() '() '() #f))
{% endhighlight %}

Finally, we translate into Haskell! Haskell is a purely functional language with super-powerful pattern matching.

{% highlight haskell %}
fparse [] n before [] accum found = accum
fparse [] n before paren accum found = 
  (push before paren) ++ (fparse_ accum)
fparse ('(':rest) 0 before paren accum found = 
  fparse (head expr) 1 accum paren [] found
fparse ('(':rest) n before paren accum found = 
  fparse (head expr) (nested + 1) before paren (push accum '(') found
fparse (')':rest) 1 before paren accum found = 
  fparse (tail expr) (nested - 1) before (fparse_ accum) (push accum ')') found
fparse (')':rest) n before paren accum found = 
  fparse (tail expr) (nested - 1) before paren (push accum ')') found

fparse_ expr = fparse expr 0 [] [] [] False
{% endhighlight %}	

The above code epitomizes the algorithm-as-a-definition approach that is Functional Programming. It is far more succinct than any prior communication of the algorithm, and simply a clearer representation than most people could communicate given complete freedom.

Hopefully you are beginning to understand what it means for a program to be functional and beginning to see the merits of a functional approach. If you wish to learn more about functional programming, scheme, lisp, or functional rudiments, checkout the following references.

- McCarthy's [Recursive Functions of Symbolic Expressions and Their Computation by Machine][1] - The paper that introduced not only Lisp, but functional programming as well, to the world.
- [Write Yourself a Scheme in 48 Hours][2] by Jonathan Tang - A tutorial on implementing a Scheme in Haskell.
- *Tarpits & Abstraction* by Matt Neary - A book which has yet to be released on building languages, simulators, and layers of abstraction.

[1]: http://www-formal.stanford.edu/jmc/recursive.pdf
[2]: http://en.wikibooks.org/wiki/Write_Yourself_a_Scheme_in_48_Hours