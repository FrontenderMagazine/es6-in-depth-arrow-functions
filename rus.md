# ES6 в деталях: Стрелочные функции

_[ES6 In Depth][1] — это цикл статей о новых возможностях языка
программирования JavaScript, появившихся в 6 редакции стандарта ECMAScript,
кратко — ES6._

Стрелки были частью JavaScript с самого начала. Первые учебники по JavaScript
советовали оборачивать встроенные скрипты в комментарии HTML. Это не позволяло
тем браузерам, которые _не_ поддерживали JS, ошибочно отображать код JS как
текст. Вы бы писали примерно так:

    <script language="javascript">
    <!--
      document.bgColor = "brown";  // red
    // -->
    </script>

Старые браузеры видели два неподдерживаемых тега и комментарий, и только новые
браузеры видели в этом код JS.

Чтобы поддерживать этот костыль, движок JavaScript в вашем браузере трактует
символы `<!--` как начало однострочного комментария. Без шуток. Это в самом
деле было частью языка всё это время и работает по сей день, не только сразу
после открывающего тега `<script>`, но и вообще в любом месте кода JS. Это
даже работает в Node.

И между прочим, [этот стиль комментариев был впервые стандартизован в ES6][2].
Но статья вовсе не про эти стрелки.

Последовательность символов в виде стрелки `-->` также обозначает однострочный
комментарий. Интересно, что в HTML комментарием считаются символы _перед_
`-->`, а в JS комментарий — это всё, что _после_ `-->` и до конца строки.

А вот что ещё интересней. Эта стрелка обозначает комментарий _только_ если
находится в начале строки. Это потому что в других контекстах в JS  `-->` —
это оператор, оператор «направлается к»!

    function countdown(n) {
      while (n --> 0)  // "n направляется к нулю"
        alert(n);
      blastoff();
    }

[Этот код действительно работает.][3] Цикл выполняется, пока `n` не достигнет
0. Это тоже _не_ появилось в ES6, а лишь сочетание старых возможностей и
немного сбивающее с толку название. Сможете разобраться, как это работает?
Как обычно, разгадку можно найти [на Stack Overflow][4].

Разумеется, есть ещё оператор «меньше или равно», `<=`. Возможно, вы найдёте
в коде JS и другие стрелки, как в играх Поиск Предметов, но давайте
остановимся и обнаружим, что _одной стрелки не хватает_.

|        |                           |
|--------|---------------------------|
| `<!--` | однострочный комментарий  |
| `-->`  | оператор «направляется к» |
| `<=`   | меньше или равно          |
| `=>`   | ???                       |

Что случилось с `=>`? Сегодня и узнаем.

Но сначала немного о функциях.

## Функции-выражения повсюду

Забавная особенность JavaScript состоит в том, что если вам нужна функция, то
вы можете написать функцию, прямо посреди исполняемого кода.

Например, предположим, вы хотите сказать браузеру, что ему следует делать,
если пользователь нажмёт определённую кнопку. Вы начинаете печатать:

    $("#confetti-btn").click(

Метод `.click()` jQuery принимает один аргумент — функцию. Без проблем. Вы
можете впечатать функцию прямо туда:

    $("#confetti-btn").click(function (event) {
      playTrumpet();
      fireConfettiCannon();
    });

Мы уже привыкли писать так, это для нас уже вполне естественно. И странно
вспоминать, что до того, как благодаря JavaScript такой подход к
программированию стал популярен, во многих языках _не было такой
функциональности_. Само собой, в Lisp были функции-выражения, они же
_лямбда-функции_, ещё с 1958. Но C++, Python, C# и Java, просущестовали годы
без них.

Но теперь всё. Во всех четырёх теперь есть лямбды. Во всех более новых языках
есть свтроенная поддержка лямбд. Мы должны поблагодарить за это JavaScript — и
ранних программистов на JavaScript, которые бесстрашно создавали сильно
зависящие от лямбд библиотеки, и которые тем самым привели к повсеместному
принятию этой функциональности.

Хотя немного жаль, что из всех упомянутых языков, в JavaScript синтаксис лямбд
оказался самым многословным.

    // Очень простые функции на шести языках.
    function (a) { return a > 0; } // JS
    [](int a) { return a > 0; }  // C++
    (lambda (a) (> a 0))  ;; Lisp
    lambda a: a > 0  # Python
    a => a > 0  // C#
    a -> a > 0  // Java

## Новая стрела в ваш колчан

В ES6 появился новый синтаксис функций.

    // ES5
    var selected = allJobs.filter(function (job) {
      return job.isSelected();
    });

    // ES6
    var selected = allJobs.filter(job => job.isSelected());

Если вам нужна простая функция с одним аргументом, то синтаксис новых,
стрелочных, функций — это просто `Идентификатор => Выражение`. Не нужно
печатать ни `function`, ни `return`, ни круглых скобок с фигурными и точкой с
запятой.

(Лично я очень благодарен за этот синтаксис. Для меня очень важно, что
печатать `function` больше не надо, потому что у меня постоянно вместо этого
получается `functoin`, и мне приходится возвращаться и исправлять опечатку.)

Чтобы создать функцию с несколькими аргументами (или без аргументов, или с
[остаточными параметрами или значениями по умолчанию][5], или с
[деструктурированием][6] в аргументе), нужно добавить скобки вокруг списка
аргументов.

    // ES5
    var total = values.reduce(function (a, b) {
      return a + b;
    }, 0);

    // ES6
    var total = values.reduce((a, b) => a + b, 0);

I think it looks pretty nice.

Arrow functions work just as beautifully with functional tools provided by libraries, like [Underscore.js][7] and [Immutable][8]. In fact, the examples in [Immutable’s documentation][9] are all written in ES6, so many of them already use arrow functions.

What about not-so-functional settings? Arrow functions can contain a block of statements instead of just an expression. Recall our earlier example:

    // ES5
    $("#confetti-btn").click(function (event) {
      playTrumpet();
      fireConfettiCannon();
    });

Here’s how it will look in ES6:

    // ES6
    $("#confetti-btn").click(event => {
      playTrumpet();
      fireConfettiCannon();
    });

A minor improvement. The effect on code using [Promises][10] can be more dramatic, as the `}).then(function (result) {` lines can pile up.

Note that an arrow function with a block body does not automatically return a value. Use a `return` statement for that.

There is one caveat when using arrow functions to create plain objects. Always wrap the object in parentheses:

    // create a new empty object for each puppy to play with
    var chewToys = puppies.map(puppy => {});   // BUG!
    var chewToys = puppies.map(puppy => ({})); // ok

Unfortunately, an empty object `{}` and an empty block `{}` look exactly the same. The rule in ES6 is that `{` immediately following an arrow is always treated as the start of a block, never the start of an object. The code `puppy => {}` is therefore silently interpreted as an arrow function that does nothing and returns `undefined`.

Even more confusing, an object literal like `{key: value}` looks exactly like a block containing a labeled statement—at least, that’s how it looks to your JavaScript engine. Fortunately `{` is the only ambiguous character, so wrapping object literals in parentheses is the only trick you need to remember.

### What’s `this`?

There is one subtle difference in behavior between ordinary `function` functions and arrow functions. **Arrow functions do not have their own `this` value.** The value of `this` inside an arrow function is always inherited from the enclosing scope.

Before we try and figure out what that means in practice, let’s back up a bit.

How does `this` work in JavaScript? Where does its value come from? [There’s no short answer.][11] If it seems simple in your head, it’s because you’ve been dealing with it for a long time!

One reason this question comes up so often is that `function` functions receive a `this` value automatically, whether they want one or not. Have you ever written this hack?

    {
      ...
      addAll: function addAll(pieces) {
        var self = this;
        _.each(pieces, function (piece) {
          self.add(piece);
        });
      },
      ...
    }

Here, what you’d _like_ to write in the inner function is just `this.add(piece)`. Unfortunately, the inner function doesn’t inherit the outer function’s `this` value. Inside the inner function, `this` will be `window` or `undefined`. The temporary variable `self` serves to smuggle the outer value of `this` into the inner function. (Another way is to use `.bind(this)` on the inner function. Neither way is particularly pretty.)

In ES6, `this` hacks mostly go away if you follow these rules:

*   Use non-arrow functions for methods that will be called using the `object.method()` syntax. Those are the functions that will receive a _meaningful_ `this` value from their caller.
*   Use arrow functions for everything else.

    // ES6
    {
      ...
      addAll: function addAll(pieces) {
        _.each(pieces, piece => this.add(piece));
      },
      ...
    }

In the ES6 version, note that the `addAll` method receives `this` from its caller. The inner function is an arrow function, so it inherits `this` from the enclosing scope.

As a bonus, ES6 also provides a shorter way to write methods in object literals! So the code above can be simplified further:

    // ES6 with method syntax
    {
      ...
      addAll(pieces) {
        _.each(pieces, piece => this.add(piece));
      },
      ...
    }

Between methods and arrows, I might never type `functoin` again. It’s a nice thought.

There’s one more minor difference between arrow and non-arrow functions: arrow functions don’t get their own `arguments` object, either. Of course, in ES6, you’d probably rather use a rest parameter or default value anyway.

### Using arrows to pierce the dark heart of computer science

We’ve talked about the many practical uses of arrow functions. There’s one more possible use case I’d like to talk about: ES6 arrow functions as a learning tool, to uncover something deep about the nature of computation. Whether that is practical or not, you’ll have to decide for yourself.

In 1936, Alonzo Church and Alan Turing independently developed powerful mathematical models of computation. Turing called his model _a-machines_, but everyone instantly started calling them Turing machines. Church wrote instead about functions. His model was called the [λ-calculus][12]. (λ is the lowercase Greek letter lambda.) This work was the reason Lisp used the word `LAMBDA` to denote functions, which is why we call function expressions “lambdas” today.

But what is the λ-calculus? What is “model of computation” supposed to mean?

It’s hard to explain in just a few words, but here is my attempt: the λ-calculus is one of the first programming languages. It was not _designed_ to be a programming language—after all, stored-program computers wouldn’t come along for another decade or two—but rather a ruthlessly simple, stripped-down, purely mathematical idea of a language that could express any kind of computation you wished to do. Church wanted this model in order to prove things about computation in general.

And he found that he only needed one thing in his system: _functions._

Think how extraordinary this claim is. Without objects, without arrays, without numbers, without `if` statements, `while` loops, semicolons, assignment, logical operators, or an event loop, it is possible to rebuild every kind of computation JavaScript can do, from scratch, using only functions.

Here is an example of the sort of “program” a mathematician could write, using Church’s λ notation:

    fix = λf.(λx.f(λv.x(x)(v)))(λx.f(λv.x(x)(v)))

The equivalent JavaScript function looks like this:

    var fix = f => (x => f(v => x(x)(v)))
                   (x => f(v => x(x)(v)));

That is, JavaScript contains an implementation of the λ-calculus that actually runs. _The λ-calculus is in JavaScript._

The stories of what Alonzo Church and later researchers did with the λ-calculus, and how it has quietly insinuated itself into almost every major programming language, are beyond the scope of this blog post. But if you’re interested in the foundations of computer science, or you’d just like to see how a language with nothing but functions can do things like loops and recursion, you could do worse than to spend some rainy afternoon looking into [Church numerals][13] and [fixed-point combinators][14], and playing with them in your Firefox console or [Scratchpad][15]. With ES6 arrows on top of its other strengths, JavaScript can reasonably claim to be the best language for exploring the λ-calculus.

### When can I use arrows?

ES6 arrow functions were implemented in Firefox by me, back in 2013. Jan de Mooij made them fast. Thanks to Tooru Fujisawa and ziyunfei for patches.

Arrow functions are also implemented in the Microsoft Edge preview release. They’re also available in [Babel][16], [Traceur][17], and [TypeScript][18], in case you’re interested in using them on the Web right now.

Our next topic is one of the stranger features in ES6. We’ll get to see `typeof x` return a totally new value. We’ll ask: When is a name not a string? We’ll puzzle over the meaning of equality. It’ll be weird. So please join us next week as we look at ES6 symbols in depth.


[1]: https://hacks.mozilla.org/category/es6-in-depth/
[2]: http://people.mozilla.org/~jorendorff/es6-draft.html#sec-html-like-comments
[3]: http://codepen.io/anon/pen/oXZaBY?editors=001
[4]: http://stackoverflow.com/questions/1642028/what-is-the-name-of-the-operator
[5]: https://hacks.mozilla.org/2015/05/es6-in-depth-rest-parameters-and-defaults/
[6]: https://hacks.mozilla.org/2015/05/es6-in-depth-destructuring/
[7]: http://underscorejs.org/
[8]: https://facebook.github.io/immutable-js/
[9]: https://facebook.github.io/immutable-js/docs/#/
[10]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[11]: http://stackoverflow.com/questions/3127429/how-does-the-this-keyword-work
[12]: https://en.wikipedia.org/wiki/Lambda_calculus
[13]: https://en.wikipedia.org/wiki/Church_encoding
[14]: https://en.wikipedia.org/wiki/Fixed-point_combinator#Strict_fixed_point_combinator
[15]: https://developer.mozilla.org/en-US/docs/Tools/Scratchpad
[16]: http://babeljs.io/
[17]: https://github.com/google/traceur-compiler#what-is-traceur
[18]: http://www.typescriptlang.org/