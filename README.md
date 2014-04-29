Grunge
======

A generator-based underscore and Range like utility library that helps you deal with finite and infinite sequences.

## About
Grunge is inspired by Ruby's Range Class, Underscore.js and Lazy.js, it implements parts of all three and some other things.
Grunge should be your go to library for creating non-trivial sequences, when you need maximum performance.

Underscore and Lazy.js have great tools for dealing with finite collection of numbers. Grunge is there to tackle infinite sequences. e.g fibonacci numbers, prime numbers etc.
In terms of the utility functions Grunge takes an approach closer to Lazy.js rather than Underscore, and no actual computation is done till you ask for values.

Grunge is also a simple utility for composing recursive functions of the time where the solution for n depends on the solution for n-1.

**Note:** Grunge is an experimental library in an early stage of development. There probably will be bugs, and it is by no means ready for production. Tests, bug-fixes and enhancements are welcome.

## Installation
On the browser, you can just download the index.js file from right here and use it.
For Node
```
npm install grunge
```
should work!

## Why Generators?
Generators are slowly finding support and are already available in Chrome, Firefox and Node 0.11.x. Moreover with many transpilers it's easy to convert Grunge into ES5 compliant code. (I will soon add a ES5 distribution ready to use, and available on various Package Managers)

Grunge *could* be written without generators. In fact, for finite sequences, Lazy.js does a great job at achieving lazy evaluation. Generators however, make whole new set of things possible.
And again, with the availability of transpilers, I think the expressiveness of the code is more important.

## Show me some examples

Grunge is made for chaining. When dealing with arrays, Grunge behaves just like Lazy.js

```
// take an array and do what underscore does, but evaluate lazily. No intermediate arrays.
var result = Grunge([1,2,3,4,5]).map(function(n){return n*n;}).map(function(n){return n+1}).filter(isPrime).toArray();

// Make a sequence starting at 1 and a step function 1
var result = Grunge(1,1).map(function(n){return n*n;}).map(function(n){return n+1}).filter(isPrime).take(5).forEach(doSomething);
```

Things can get more interesting when you start feeding step functions into Grunge

```
var rps = Grunge([['rock'], ['paper'], ['scissors']], function(el){
  var results = [];
  for(var i = 0; i < el.length; i++){
    results.push(['rock'].concat(el[i]));
    results.push(['paper'].concat(el[i]));
    results.push(['scissors'].concat(el[i]));
  }
  return results;
}).skip(2).take(1).toArray();
```
Here are you are solving for all possible moves in a game of Rock Paper Scissors in three games, by starting with the base case and iterating rather that recursing.



You can even go all out and feed Generator functions into Grunge.

```
var fibinacciSquaredFiltered = Grunge(function*(){
  var first = 1;
  var second = 1;
  yield first;
  yield second;
  while(true){
    let elem = first + second;
    yield elem;
    first = second;
    second = elem;
  }
}).map(function(n){return n*n;}).filter(function(n){return n%2 !== 0;}).step(3).take(100).toArray();
```

Here you have an infinite sequence of fibonacci numbers, you can map each of them to squares, filter out all the even numbers, then pick every third element, and finally get hundred such elements.

Another situation where this might be useful is generating Prime numbers.

```
var Primes = Grunge(function*(){
  for(let i = 2; true; i++){
    yeild Math.pow(2, i) - 1;
  }
}).filter(isPrime).toArray();
```

You could see how you can add a chain of filters instead of simple isPrime function to make it more performant.

## Future
Grunge is just a small tool right now, with very limited use-cases.
I hope to add support for asynchronous computation in the future. It mostly comes down to design decisions, but with asynchronous results, parallel computation with web workers, and some smart users, Grunge could be a powerful niche tool.

