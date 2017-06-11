---
layout: post
title:  "Using Railroad Diagrams to Explain Process"
date:   2017-07-11 15:30:00 -0600
categories: railroad syntax sqlite
---
Every so often, we need to explain the steps to complete a process. When it is simple enough, a series of bullets or a numbered list will do:
- Go to the shop.
- Buy milk.

But it's not always that simple. So:
1. Go to a shop. Optionally, shop a or b.
2. If at shop A:
    * buy milk.
3. If you end up at shop b:
    * buy almond milk instead.


This can be particularly felt when trying to explain programming syntax. Language syntax is essentially a series of steps too.
 [Syntax](https://en.wikipedia.org/wiki/Syntax):

> ... set of rules, principles, and processes that govern the structure of sentences in a given language, specifically word order and punctuation.

For example, in [Clojure](clojure.org), a valid form is either a literal or a function composing other forms :

{% highlight clojure %}
user=> "foo"
"foo"
{% endhighlight %}

or

{% highlight clojure %}
(defn add1 [x] (+ x 1))
{% endhighlight %}

Railroad diagrams are a visual way to quickly express this equivalence:
![clojure forms]({{ site.url }}/assets/clojure_form.gif)

We can build on this definition. For instance we can show basic arithmetic:
![clojure arithmetic]({{ site.url }}/assets/clojure_arithmetic.gif)

This diagram tells us that:
- after the opening brace, we have a choice of various operators.
- we can follow the operator, with a form or with a literal (strictly speaking, we could have just had a form in here, since literals are forms too).
- we need a second argument.
- we can optionally provide more arguments.

Hence all of the following are valid formulations for clojure arithmetic:
{% highlight clojure %}
(+ 1 1)
(+ 1 2 3)
(+ 1 (+ 1 1))
{% endhighlight %}


The sqlite documentation makes pretty good use of these diagrams, indeed that is where I came across the idea. Checkout the beautiful image that is the SELECT query: https://sqlite.org/lang_select.html.

Railway diagrams are a good way for explaining and referencing syntax and are more approachable than other [approaches](https://en.wikipedia.org/wiki/Backus%E2%80%93Naur_form#Example).

Unfortunately, the tooling out there for generating your own isn't that great. I used TCL and this [script](https://www.sqlite.org/docsrc/artifact/46e904a66f).

You can find the repo of the diagrams as well as the code used to generate them here: https://github.com/machira/railway-diagrams. The definitions are in `bubble-generator-data.tcl`