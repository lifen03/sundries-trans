## Javascript中async和await循环

作者：Zell Liew @2019-05-21

*翻译&校验：[freedom](https://github.com/yylifen)*

![JavaScript async and await in loops](https://cdn-images-1.medium.com/max/800/1*p2upvpYKRT0-qtTP61LOYg.gif)

基本的`async`和`await`是简单的。当你尝试使用`await`循环时，事情会变得复杂一些。在这篇文章中，我想分享一些值得注意的问题，如果你打算循环使用`await`的话。


### **在开始之前**

我假设你已经知道如何使用`async`和`await`。如果没有，请阅读[上一篇文章](https://zellwk.com/blog/async-await)在继续之前先自学熟悉一下。

### **准备一个例子**

在本文中，假设您希望从水果篮中获取水果的数量。

```javascript
const fruitBasket = { apple: 27, grape: 0, pear: 14};
```
你想从水果篮中得到每个水果的数量。要获得水果的数量，可以使用`getNumFruit `函数。

```javascript
const getNumFruit = fruit => { return fruitBasket[fruit];};
```

```javascript
const numApples = getNumFruit(“apple”);console.log(numApples); // 27
```
现在，假设水果篮生活在远程服务器上。访问它需要一秒钟。我们可以用超时来模拟这个一秒的延迟。(如果理解超时代码有问题，请参阅[前一篇文章](https://zellwk.com/blog/async-await))。

```javascript
const sleep = ms => { return new Promise(resolve => setTimeout(resolve, ms));};
```
```javascript
const getNumFruit = fruit => { return sleep(1000).then(v => fruitBasket[fruit]);};
```
```javascript
getNumFruit(“apple”).then(num => console.log(num)); // 27
```
最后，假设您希望使用`await`和`getNumFruit`来获取异步函数中每个水果的数量。
```javascript
const control = async _ => { console.log(“Start”);
```
```javascript
const numApples = await getNumFruit(“apple”); console.log(numApples);
```
```javascript
const numGrapes = await getNumFruit(“grape”); console.log(numGrapes);
```
```javascript
const numPears = await getNumFruit(“pear”); console.log(numPears);
```
```javascript
console.log(“End”);
```
有了这个，我们就可以开始研究`await`循环了。

    ### **Await in a for loop**

    Let's say we have an array of fruits we want to get from the fruit basket.
    <!--kg-card-begin: code--><pre>`const fruitsToGet = [“apple”, “grape”, “pear”];`</pre><!--kg-card-end: code-->

    We are going to loop through this array.
    <!--kg-card-begin: code--><pre>`const forLoop = async _ =&gt; { console.log(“Start”);`</pre><!--kg-card-end: code--><!--kg-card-begin: code--><pre>`for (let index = 0; index &lt; fruitsToGet.length; index++) { // Get num of each fruit }`</pre><!--kg-card-end: code--><!--kg-card-begin: code--><pre>`console.log(“End”);};`</pre><!--kg-card-end: code-->

    In the for-loop, we will use `getNumFruit` to get the number of each fruit. We'll also log the number into the console.

    Since `getNumFruit` returns a promise, we can `await` the resolved value before logging it.
    <!--kg-card-begin: code--><pre>`const forLoop = async _ =&gt; { console.log(“Start”);`</pre><!--kg-card-end: code--><!--kg-card-begin: code--><pre>`for (let index = 0; index &lt; fruitsToGet.length; index++) { const fruit = fruitsToGet[index]; const numFruit = await getNumFruit(fruit); console.log(numFruit); }`</pre><!--kg-card-end: code--><!--kg-card-begin: code--><pre>`console.log(“End”);};`</pre><!--kg-card-end: code-->

    When you use `await`, you expect JavaScript to pause execution until the awaited promise gets resolved. This means `await`s in a for-loop should get executed in series.

    The result is what you'd expect.
    <!--kg-card-begin: code--><pre>`“Start”;“Apple: 27”;“Grape: 0”;“Pear: 14”;“End”;`</pre><!--kg-card-end: code--><!--kg-card-begin: image--><figure class="kg-card kg-image-card kg-card-hascaption">![](https://cdn-media-1.freecodecamp.org/images/sU0OzGuNuH5BwMoYmNcmNLfQzcexW0m7e08K)<figcaption>Console shows ‘Start’. One second later, it logs 27. Another second later, it logs 0. One more second later, it logs 14, and ‘End’</figcaption></figure><!--kg-card-end: image-->

    This behavior works with most loops (like `while` and `for-of` loops)...

    But it won't work with loops that require a callback. Examples of such loops that require a fallback include `forEach`, `map`, `filter`, and `reduce`. We'll look at how `await` affects `forEach`, `map`, and `filter` in the next few sections.

    ### **Await in a forEach loop**

    We'll do the same thing as we did in the for-loop example. First, let's loop through the array of fruits.
    <!--kg-card-begin: code--><pre>`const forEachLoop = _ =&gt; { console.log(“Start”);`</pre><!--kg-card-end: code--><!--kg-card-begin: code--><pre>`fruitsToGet.forEach(fruit =&gt; { // Send a promise for each fruit });`</pre><!--kg-card-end: code--><!--kg-card-begin: code--><pre>`console.log(“End”);};`</pre><!--kg-card-end: code-->

    Next, we'll try to get the number of fruits with `getNumFruit`. (Notice the `async` keyword in the callback function. We need this `async` keyword because `await` is in the callback function).
    <!--kg-card-begin: code--><pre>`const forEachLoop = _ =&gt; { console.log(“Start”);`</pre><!--kg-card-end: code--><!--kg-card-begin: code--><pre>`fruitsToGet.forEach(async fruit =&gt; { const numFruit = await getNumFruit(fruit); console.log(numFruit); });`</pre><!--kg-card-end: code--><!--kg-card-begin: code--><pre>`console.log(“End”);};`</pre><!--kg-card-end: code-->

    You might expect the console to look like this:
    <!--kg-card-begin: code--><pre>`“Start”;“27”;“0”;“14”;“End”;`</pre><!--kg-card-end: code-->

    But the actual result is different. JavaScript proceeds to call `console.log('End') `before the promises in the forEach loop gets resolved.

    The console logs in this order:
    <!--kg-card-begin: code--><pre>`‘Start’‘End’‘27’‘0’‘14’`</pre><!--kg-card-end: code--><!--kg-card-begin: image--><figure class="kg-card kg-image-card kg-card-hascaption">![](https://cdn-media-1.freecodecamp.org/images/DwUgo9TAK8PXNLIAv3-27UZMSrEkfNx778hS)<figcaption>Console logs ‘Start’ and ‘End’ immediately. One second later, it logs 27, 0, and 14.</figcaption></figure><!--kg-card-end: image-->

    JavaScript does this because `forEach` is not promise-aware. It cannot support `async` and `await`. You __cannot__ use `await` in `forEach`.

    ### **Await with map**

    If you use `await` in a `map`, `map` will always return an array of promise. This is because asynchronous functions always return promises.
    <!--kg-card-begin: code--><pre>`const mapLoop = async _ =&gt; { console.log(“Start”);`</pre><!--kg-card-end: code--><!--kg-card-begin: code--><pre>`const numFruits = await fruitsToGet.map(async fruit =&gt; { const numFruit = await getNumFruit(fruit); return numFruit; });`</pre><!--kg-card-end: code--><!--kg-card-begin: code--><pre>`console.log(numFruits);`</pre><!--kg-card-end: code--><!--kg-card-begin: code--><pre>`console.log(“End”);};`</pre><!--kg-card-end: code--><!--kg-card-begin: code--><pre>`“Start”;“[Promise, Promise, Promise]”;“End”;`</pre><!--kg-card-end: code--><!--kg-card-begin: image--><figure class="kg-card kg-image-card kg-card-hascaption">![](https://cdn-media-1.freecodecamp.org/images/JD3o7BUxILoFP-hVwv5920JtHJPB8l6IrMNs)<figcaption>Console logs ‘`Start`’, ‘[Promise, Promise, Promise]’, and ‘End’ immediately</figcaption></figure><!--kg-card-end: image-->

    Since `map` always return promises (if you use `await`), you have to wait for the array of promises to get resolved. You can do this with` await Promise.all(arrayOfPromises)`.
    <!--kg-card-begin: code--><pre>`const mapLoop = async _ =&gt; { console.log(“Start”);`</pre><!--kg-card-end: code--><!--kg-card-begin: code--><pre>`const promises = fruitsToGet.map(async fruit =&gt; { const numFruit = await getNumFruit(fruit); return numFruit; });`</pre><!--kg-card-end: code--><!--kg-card-begin: code--><pre>`const numFruits = await Promise.all(promises); console.log(numFruits);`</pre><!--kg-card-end: code--><!--kg-card-begin: code--><pre>`console.log(“End”);};`</pre><!--kg-card-end: code-->

    Here's what you get:
    <!--kg-card-begin: code--><pre>`“Start”;“[27, 0, 14]”;“End”;`</pre><!--kg-card-end: code--><!--kg-card-begin: image--><figure class="kg-card kg-image-card kg-card-hascaption">![](https://cdn-media-1.freecodecamp.org/images/Clz579WsPZ0Tv4iiA5rN1960VP0xx4x66dAz)<figcaption>Console logs ‘Start’. One second later, it logs ‘[27, 0, 14] and ‘End’</figcaption></figure><!--kg-card-end: image-->

    You can manipulate the value you return in your promises if you wish to. The resolved values will be the values you return.
    <!--kg-card-begin: code--><pre>`const mapLoop = async _ =&gt; { // … const promises = fruitsToGet.map(async fruit =&gt; { const numFruit = await getNumFruit(fruit); // Adds onn fruits before returning return numFruit + 100; }); // …};`</pre><!--kg-card-end: code--><!--kg-card-begin: code--><pre>`“Start”;“[127, 100, 114]”;“End”;`</pre><!--kg-card-end: code-->

    ### **Await with filter**

    When you use `filter`, you want to filter an array with a specific result. Let's say you want to create an array with more than 20 fruits.

    If you use `filter` normally (without await), you'll use it like this:
    <!--kg-card-begin: code--><pre>`// Filter if there’s no awaitconst filterLoop = _ =&gt; { console.log(‘Start’)`</pre><!--kg-card-end: code--><!--kg-card-begin: code--><pre>`const moreThan20 = await fruitsToGet.filter(fruit =&gt; { const numFruit = fruitBasket[fruit] return numFruit &gt; 20 })`</pre><!--kg-card-end: code--><!--kg-card-begin: code--><pre>`console.log(moreThan20) console.log(‘End’)}`</pre><!--kg-card-end: code-->

    You would expect `moreThan20` to contain only apples because there are 27 apples, but there are 0 grapes and 14 pears.
    <!--kg-card-begin: code--><pre>`“Start”[“apple”];(“End”);`</pre><!--kg-card-end: code-->

    `await` in `filter` doesn't work the same way. In fact, it doesn't work at all. You get the unfiltered array back...
    <!--kg-card-begin: code--><pre>`const filterLoop = _ =&gt; { console.log(‘Start’)`</pre><!--kg-card-end: code--><!--kg-card-begin: code--><pre>`const moreThan20 = await fruitsToGet.filter(async fruit =&gt; { const numFruit = getNumFruit(fruit) return numFruit &gt; 20 })`</pre><!--kg-card-end: code--><!--kg-card-begin: code--><pre>`console.log(moreThan20) console.log(‘End’)}`</pre><!--kg-card-end: code--><!--kg-card-begin: code--><pre>`“Start”[(“apple”, “grape”, “pear”)];(“End”);`</pre><!--kg-card-end: code--><!--kg-card-begin: image--><figure class="kg-card kg-image-card kg-card-hascaption">![](https://cdn-media-1.freecodecamp.org/images/xI8y6n2kvda8pz9i7f5ffVu92gs7ISj7My9M)<figcaption>Console loggs ‘Start’, ‘[‘apple’, ‘grape’, ‘pear’]’, and ‘End’ immediately</figcaption></figure><!--kg-card-end: image-->

    Here's why it happens.

    When you use `await` in a `filter` callback, the callback always a promise. Since promises are always truthy, everything item in the array passes the filter. Writing `await` in a `filter` is like writing this code:
    <!--kg-card-begin: code--><pre>`// Everything passes the filter…const filtered = array.filter(true);`</pre><!--kg-card-end: code-->

    There are three steps to use `await` and `filter` properly:

    1. Use `map` to return an array promises

    2. `await` the array of promises

    3. `filter` the resolved values
    <!--kg-card-begin: code--><pre>`const filterLoop = async _ =&gt; { console.log(“Start”);`</pre><!--kg-card-end: code--><!--kg-card-begin: code--><pre>`const promises = await fruitsToGet.map(fruit =&gt; getNumFruit(fruit)); const numFruits = await Promise.all(promises);`</pre><!--kg-card-end: code--><!--kg-card-begin: code--><pre>`const moreThan20 = fruitsToGet.filter((fruit, index) =&gt; { const numFruit = numFruits[index]; return numFruit &gt; 20; });`</pre><!--kg-card-end: code--><!--kg-card-begin: code--><pre>`console.log(moreThan20); console.log(“End”);};`</pre><!--kg-card-end: code--><!--kg-card-begin: code--><pre>`Start[“apple”];End;`</pre><!--kg-card-end: code--><!--kg-card-begin: image--><figure class="kg-card kg-image-card kg-card-hascaption">![](https://cdn-media-1.freecodecamp.org/images/KbvkmmX4K77pSq29OVj8jhK0KNdyCYQsH1Sn)<figcaption>Console shows ‘Start’. One second later, console logs ‘[‘apple’]’ and ‘End’</figcaption></figure><!--kg-card-end: image-->

    ### **Await with reduce**

    For this case, let's say you want to find out the total number of fruits in the fruitBastet. Normally, you can use `reduce` to loop through an array and sum the number up.
    <!--kg-card-begin: code--><pre>`// Reduce if there’s no awaitconst reduceLoop = _ =&gt; { console.log(“Start”);`</pre><!--kg-card-end: code--><!--kg-card-begin: code--><pre>`const sum = fruitsToGet.reduce((sum, fruit) =&gt; { const numFruit = fruitBasket[fruit]; return sum + numFruit; }, 0);`</pre><!--kg-card-end: code--><!--kg-card-begin: code--><pre>`console.log(sum); console.log(“End”);};`</pre><!--kg-card-end: code-->

    You'll get a total of 41 fruits. (27 + 0 + 14 = 41).
    <!--kg-card-begin: code--><pre>`“Start”;“41”;“End”;`</pre><!--kg-card-end: code--><!--kg-card-begin: image--><figure class="kg-card kg-image-card kg-card-hascaption">![](https://cdn-media-1.freecodecamp.org/images/1cHrIKZU2x4bt0Cl6NmnrNA9eYed0-3n4SIq)<figcaption>Console logs ‘Start’, ‘41’, and ‘End’ immediately</figcaption></figure><!--kg-card-end: image-->

    When you use `await` with reduce, the results get extremely messy.
    <!--kg-card-begin: code--><pre>`// Reduce if we await getNumFruitconst reduceLoop = async _ =&gt; { console.log(“Start”);`</pre><!--kg-card-end: code--><!--kg-card-begin: code--><pre>`const sum = await fruitsToGet.reduce(async (sum, fruit) =&gt; { const numFruit = await getNumFruit(fruit); return sum + numFruit; }, 0);`</pre><!--kg-card-end: code--><!--kg-card-begin: code--><pre>`console.log(sum); console.log(“End”);};`</pre><!--kg-card-end: code--><!--kg-card-begin: code--><pre>`“Start”;“[object Promise]14”;“End”;`</pre><!--kg-card-end: code--><!--kg-card-begin: image--><figure class="kg-card kg-image-card kg-card-hascaption">![](https://cdn-media-1.freecodecamp.org/images/vziO1ieaUzFZNE1p3FlaOBJLEQtAL21CYDKw)<figcaption>Console logs ‘Start’. One second later, it logs ‘[object Promise]14’ and ‘End’</figcaption></figure><!--kg-card-end: image-->

    What?! `[object Promise]14`?!

    Dissecting this is interesting.

*   In the first iteration, `sum` is `0`. `numFruit` is 27 (the resolved value from `getNumFruit(‘apple’)`). `0 + 27 `is 27.
*   In the second iteration, `sum` is a promise. (Why? Because asynchronous functions always return promises!) `numFruit` is 0. A promise cannot be added to an object normally, so the JavaScript converts it to `[object Promise]` string. `[object Promise] + 0 `is `[object Promise]0`
*   In the third iteration, `sum` is also a promise. `numFruit` is `14`. `[object Promise] + 14` is `[object Promise]14`.

    Mystery solved!

    This means, you can use `await` in a `reduce` callback, but you have to remember to `await` the accumulator first!
    <!--kg-card-begin: code--><pre>`const reduceLoop = async _ =&gt; { console.log(“Start”);`</pre><!--kg-card-end: code--><!--kg-card-begin: code--><pre>`const sum = await fruitsToGet.reduce(async (promisedSum, fruit) =&gt; { const sum = await promisedSum; const numFruit = await getNumFruit(fruit); return sum + numFruit; }, 0);`</pre><!--kg-card-end: code--><!--kg-card-begin: code--><pre>`console.log(sum); console.log(“End”);};`</pre><!--kg-card-end: code--><!--kg-card-begin: code--><pre>`“Start”;“41”;“End”;`</pre><!--kg-card-end: code--><!--kg-card-begin: image--><figure class="kg-card kg-image-card kg-card-hascaption">![](https://cdn-media-1.freecodecamp.org/images/a3ZEbODMdVmob-31Qh0qfFugbLJoNZudoPwM)<figcaption>Console logs ‘Start’. Three seconds later, it logs ‘41’ and ‘End’</figcaption></figure><!--kg-card-end: image-->

    But... as you can see from the gif, it takes pretty long to `await` everything. This happens because `reduceLoop` needs to wait for the `promisedSum` to be completed for each iteration.

    There's a way to speed up the reduce loop. (I found out about this thanks to [Tim Oxley](https://twitter.com/timkevinoxley). If you `await getNumFruits(`) first before `await promisedSum`, the `reduceLoop` takes only one second to complete:
    <!--kg-card-begin: code--><pre>`const reduceLoop = async _ =&gt; { console.log(“Start”);`</pre><!--kg-card-end: code--><!--kg-card-begin: code--><pre>`const sum = await fruitsToGet.reduce(async (promisedSum, fruit) =&gt; { // Heavy-lifting comes first. // This triggers all three getNumFruit promises before waiting for the next iteration of the loop. const numFruit = await getNumFruit(fruit); const sum = await promisedSum; return sum + numFruit; }, 0);`</pre><!--kg-card-end: code--><!--kg-card-begin: code--><pre>`console.log(sum); console.log(“End”);};`</pre><!--kg-card-end: code--><!--kg-card-begin: image--><figure class="kg-card kg-image-card kg-card-hascaption">![](https://cdn-media-1.freecodecamp.org/images/Vm9olChpCbbEgBmub6OuOS2r0QD5SwwW9y9i)<figcaption>Console logs ‘Start’. One second later, it logs ‘41’ and ‘End’</figcaption></figure><!--kg-card-end: image-->

    This works because `reduce` can fire all three `getNumFruit` promises before waiting for the next iteration of the loop. However, this method is slightly confusing since you have to be careful of the order you `await` things.

    The simplest (and most efficient way) to use `await` in reduce is to:

    1. Use `map` to return an array promises

    2. `await` the array of promises

    3. `reduce` the resolved values
    <!--kg-card-begin: code--><pre>`const reduceLoop = async _ =&gt; { console.log(“Start”);`</pre><!--kg-card-end: code--><!--kg-card-begin: code--><pre>`const promises = fruitsToGet.map(getNumFruit); const numFruits = await Promise.all(promises); const sum = numFruits.reduce((sum, fruit) =&gt; sum + fruit);`</pre><!--kg-card-end: code--><!--kg-card-begin: code--><pre>`console.log(sum); console.log(“End”);};
<!--kg-card-end: code-->

This version is simple to read and understand, and takes one second to calculate the total number of fruits.
<!--kg-card-begin: image--><figure class="kg-card kg-image-card kg-card-hascaption">![](https://cdn-media-1.freecodecamp.org/images/rAN2FodB3Ff8FmQvMLy4R3mii1Kt41jRszkE)<figcaption>Console logs ‘Start’. One second later, it logs ‘41’ and ‘End’</figcaption></figure><!--kg-card-end: image-->

### **Key Takeaways**

1. If you want to execute `await` calls in series, use a `for-loop` (or any loop without a callback).

2. Don't ever use `await` with `forEach`. Use a `for-loop` (or any loop without a callback) instead.

3. Don't `await` inside `filter` and `reduce`. Always `await` an array of promises with `map`, then `filter` or `reduce` accordingly.

This article was originally posted on [_my blog_](https://zellwk.com/blog/async-await-in-loops/)_. _
Sign up for my[ newsletter](https://zellwk.com/) if you want more articles to help you become a better frontend developer.

                    </div>
            </section>

            <footer class="post-full-footer">

<section class="author-card">
        ![freeCodeCamp.org](//www.gravatar.com/avatar/fcda43852608626fe46d7fd43145766e?s=250&amp;d=mm&amp;r=x)
    <section class="author-card-content">

#### [freeCodeCamp.org](/news/author/freecodecamp/)

Read [more posts](/news/author/freecodecamp/) by this author.

    </section>
</section>
<div class="post-full-footer-right">
    [Read More](/news/author/freecodecamp/)
</div>

            </footer>
