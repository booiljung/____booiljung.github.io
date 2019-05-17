## dart:async - asynchronous programming

Asynchronous programming often uses callback functions, but Dart provides alternatives: [Future](https://api.dartlang.org/stable/dart-async/Future-class.html) and [Stream](https://api.dartlang.org/stable/dart-async/Stream-class.html) objects. A Future is like a promise for a result to be provided sometime in the future. A Stream is a way to get a sequence of values, such as events. Future, Stream, and more are in the dart:async library ([API reference](https://api.dartlang.org/stable/dart-async/dart-async-library.html)).

**Note:** You don’t always need to use the Future or Stream APIs directly. The Dart language supports asynchronous coding using keywords such as `async` and `await`. See [Asynchrony support](https://dart.dev/guides/language/language-tour#asynchrony-support) in the language tour for details.

The dart:async library works in both web apps and command-line apps. To use it, import dart:async:

```dart
import 'dart:async';
```

**Version note:**   As of Dart 2.1, you don’t need to import dart:async to   use the Future and Stream APIs, because dart:core exports those classes.

###  Future

Future objects appear throughout the Dart libraries, often as the object returned by an asynchronous method. When a future *completes*, its value is ready to use.

#### Using await

Before you directly use the Future API, consider using `await` instead. Code that uses `await` expressions can be easier to understand than code that uses the Future API.

Consider the following function.  It uses Future’s `then()` method to execute three asynchronous functions in a row, waiting for each one to complete before executing the next one.

```dart
runUsingFuture() {
  // ...
  findEntryPoint().then((entryPoint) {
    return runExecutable(entryPoint, args);
  }).then(flushThenExit);
}
```

The equivalent code with await expressions looks more like synchronous code:

```dart
runUsingAsyncAwait() async {
  // ...
  var entryPoint = await findEntryPoint();
  var exitCode = await runExecutable(entryPoint, args);
  await flushThenExit(exitCode);
}
```

An async function can catch exceptions from Futures. For example:

```dart
var entryPoint = await findEntryPoint();
try {
  var exitCode = await runExecutable(entryPoint, args);
  await flushThenExit(exitCode);
} catch (e) {
  // Handle the error...
}
```

**Important:** Async functions return Futures. If you don’t want your function to return a future, then use a different solution. For example, you might call an async function from your function.

For more information on using `await` and related Dart language features, see [Asynchrony support](https://dart.dev/guides/language/language-tour#asynchrony-support).

#### Basic usage

You can use `then()` to schedule code that runs when the future completes. For example, `HttpRequest.getString()` returns a Future, since HTTP requests can take a while. Using `then()` lets you run some code when that Future has completed and the promised string value is available:

```dart
HttpRequest.getString(url).then((String result) {
  print(result);
});
```

Use `catchError()` to handle any errors or exceptions that a Future object might throw.

```dart
HttpRequest.getString(url).then((String result) {
  print(result);
}).catchError((e) {
  // Handle or ignore the error.
});
```

The `then().catchError()` pattern is the asynchronous version of `try`-`catch`.

**Important:** Be sure to invoke `catchError()` on the result of `then()`—not on the result of the original Future. Otherwise, the `catchError()` can handle errors only from the original Future’s computation, but not from the handler registered by `then()`.

#### Chaining multiple asynchronous methods

The `then()` method returns a Future, providing a useful way to run multiple asynchronous functions in a certain order. If the callback registered with `then()` returns a Future, `then()` returns an equivalent Future. If the callback returns a value of any other type, `then()` creates a new Future that completes with the value.

```dart
Future result = costlyQuery(url);
result
    .then((value) => expensiveWork(value))
    .then((_) => lengthyComputation())
    .then((_) => print('Done!'))
    .catchError((exception) {
  /* Handle exception... */
});
```

In the preceding example, the methods run in the following order:

1. `costlyQuery()`
2. `expensiveWork()`
3. `lengthyComputation()`

Here is the same code written using await:

```dart
try {
  final value = await costlyQuery(url);
  await expensiveWork(value);
  await lengthyComputation();
  print('Done!');
} catch (e) {
  /* Handle exception... */
}
```

#### Waiting for multiple futures

Sometimes your algorithm needs to invoke many asynchronous functions and wait for them all to complete before continuing. Use the [Future.wait()](https://api.dartlang.org/stable/dart-async/Future/wait.html) static method to manage multiple Futures and wait for them to complete:

```dart
Future deleteLotsOfFiles() async =>  ...
Future copyLotsOfFiles() async =>  ...
Future checksumLotsOfOtherFiles() async =>  ...

await Future.wait([
  deleteLotsOfFiles(),
  copyLotsOfFiles(),
  checksumLotsOfOtherFiles(),
]);
print('Done with all the long steps!');
```

###  Stream

Stream objects appear throughout Dart APIs, representing sequences of data. For example, HTML events such as button clicks are delivered using streams. You can also read a file as a stream.

#### Using an asynchronous for loop

Sometimes you can use an asynchronous for loop (`await for`) instead of using the Stream API.

Consider the following function. It uses Stream’s `listen()` method to subscribe to a list of files, passing in a function literal that searches each file or directory.

```dart
void main(List<String> arguments) {
  // ...
  FileSystemEntity.isDirectory(searchPath).then((isDir) {
    if (isDir) {
      final startingDir = Directory(searchPath);
      startingDir
          .list(
              recursive: argResults[recursive],
              followLinks: argResults[followLinks])
          .listen((entity) {
        if (entity is File) {
          searchFile(entity, searchTerms);
        }
      });
    } else {
      searchFile(File(searchPath), searchTerms);
    }
  });
}
```

The equivalent code with await expressions, including an asynchronous for loop (`await for`), looks more like synchronous code:

```dart
Future main(List<String> arguments) async {
  // ...
  if (await FileSystemEntity.isDirectory(searchPath)) {
    final startingDir = Directory(searchPath);
    await for (var entity in startingDir.list(
        recursive: argResults[recursive],
        followLinks: argResults[followLinks])) {
      if (entity is File) {
        searchFile(entity, searchTerms);
      }
    }
  } else {
    searchFile(File(searchPath), searchTerms);
  }
}
```

**Important:** Before using `await for`, make sure that it makes the code clearer and that you really do want to wait for all of the stream’s results. For example, you usually should **not** use `await for` for DOM event listeners, because the DOM sends endless streams of events. If you use `await for` to register two DOM event listeners in a row, then the second kind of event is never handled.

For more information on using `await` and related Dart language features, see [Asynchrony support](https://dart.dev/guides/language/language-tour#asynchrony-support).

#### Listening for stream data

To get each value as it arrives, either use `await for` or subscribe to the stream using the `listen()` method:

```dart
// Find a button by ID and add an event handler.
querySelector('#submitInfo').onClick.listen((e) {
  // When the button is clicked, it runs this code.
  submitData();
});
```

In this example, the `onClick` property is a Stream object provided by the “submitInfo” button.

If you care about only one event, you can get it using a property such as `first`, `last`, or `single`. To test the event before handling it, use a method such as `firstWhere()`, `lastWhere()`, or `singleWhere()`.

If you care about a subset of events, you can use methods such as `skip()`, `skipWhile()`, `take()`, `takeWhile()`, and `where()`.

#### Transforming stream data

Often, you need to change the format of a stream’s data before you can use it. Use the `transform()` method to produce a stream with a different type of data:

```dart
var lines = inputStream
    .transform(utf8.decoder)
    .transform(LineSplitter());
```

This example uses two transformers. First it uses utf8.decoder to transform the stream of integers into a stream of strings. Then it uses a LineSplitter to transform the stream of strings into a stream of separate lines. These transformers are from the dart:convert library (see the [dart:convert section](https://dart.dev/guides/libraries/library-tour#dartconvert---decoding-and-encoding-json-utf-8-and-more)).

#### Handling errors and completion

How you specify error and completion handling code depends on whether you use an asynchronous for loop (`await for`) or the Stream API.

If you use an asynchronous for loop, then use try-catch to handle errors. Code that executes after the stream is closed goes after the asynchronous for loop.

```dart
Future readFileAwaitFor() async {
  var config = File('config.txt');
  Stream<List<int>> inputStream = config.openRead();

  var lines = inputStream
      .transform(utf8.decoder)
      .transform(LineSplitter());
  try {
    await for (var line in lines) {
      print('Got ${line.length} characters from stream');
    }
    print('file is now closed');
  } catch (e) {
    print(e);
  }
}
```

If you use the Stream API, then handle errors by registering an `onError` listener. Run code after the stream is closed by registering an `onDone` listener.

```dart
var config = File('config.txt');
Stream<List<int>> inputStream = config.openRead();

inputStream
    .transform(utf8.decoder)
    .transform(LineSplitter())
    .listen((String line) {
  print('Got ${line.length} characters from stream');
}, onDone: () {
  print('file is now closed');
}, onError: (e) {
  print(e);
});
```

###  More information

For some examples of using Future and Stream in command-line apps, see the [dart:io tour](https://dart.dev/guides/libraries/library-tour#dartio). Also see these articles and tutorials:

- [Asynchronous Programming: Futures](https://dart.dev/tutorials/language/futures)
- [Futures and Error Handling](https://dart.dev/guides/libraries/futures-error-handling)
- [Asynchronous Programming: Streams](https://dart.dev/tutorials/language/streams)
- [Creating Streams in Dart](https://dart.dev/articles/libraries/creating-streams)