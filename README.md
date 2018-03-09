# RxJava2 Nullable
[![Build Status](https://travis-ci.org/XDean/rx2-nullable.svg?branch=master)](https://travis-ci.org/XDean/rx2-nullable)
[![codecov.io](http://codecov.io/github/XDean/rx2-nullable/coverage.svg?branch=master)](https://codecov.io/gh/XDean/rx2-nullable/branch/master)
[![Maven Central](https://maven-badges.herokuapp.com/maven-central/com.github.XDean/rx2-nullable/badge.svg)](https://maven-badges.herokuapp.com/maven-central/com.github.XDean/rx2-nullable)

RxJava2 Null Handler

## Motivation

Since [RxJava 2.x no longer accepts `null` values](https://github.com/ReactiveX/RxJava/wiki/What's-different-in-2.0#nulls), but in so many cases we do need `null`. 

For example, you get an array from some place(most time is 3rd lib), there is null value in it. `Observable.fromArray(array)` will throw `NullPointerException`. You have to do some processing like this to drop null value:

```
Observable.fromArray(Arrays.stream(array)
  .filter(e -> e!=null)
  .toArray(YourClass[]::new))
```

or do this to give a default value:

```
Observable.fromArray(Arrays.stream(array)
  .map(e -> e == null? defaultValue : e)
  .toArray(YourClass[]::new))
```

It's painful to do this handling every time we can't control the `null` value. (It must be 3rd party library's wrong. I hope there is no null value in the world.)

By using `rx2-nullable`, you can do this:

```
RxNullable.fromArray(array)
  .onNullDrop()
  .observable()
```

or

```
RxNullable.fromArray(array)
  .onNullWrap()
  .observable() // Observable<Optional<T>>
```

It gives you a easy, readable, flexible solution to `null` value in RxJava2.

## Usage

`RxNullable` is the entrance. It provides following methods to create RxJava objects:

- `fromArray`
- `fromIterable`
- `fromCallable`
- `fromPublisher`
- `fromFuture`

They will return `NullableObservableFlowable` object.

Then you can set your `NullHandler` onto the `NullableObservableFlowable`. Or you can use following build-in handlers:

- `onNullDrop`. Drop `null` value.
- `onNullWrap`. Wrap values as `Optional`, you will get `Observable<Optional<T>>`.
- `onNullDefault`. Map `null` value to default value.
- `onNullRun`. When `null` comes, do something and drop it.

It's easy to customize your own `NullHandler`(But it is unnecessary in most time). 
For example, use a random value instead of `null`:

```
double[] array = {1d, null, 2d, null, 3d};
RxNullable.fromArray(array).observable(i -> i == null ? Math.random() : i)
``` 

