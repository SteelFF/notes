await和async是ES2017（ES8）中引入的两个关键字，用于简化异步操作的编程模型。

在JavaScript中，当我们需要处理多个异步操作时，我们可以使用Promise chaining来避免<font color = "red">回调地狱</font>的问题。回调地狱是指在处理多个异步操作时，层层嵌套的回调函数导致代码难以阅读和维护。

然而，Promise chaining也有其缺点。当我们需要处理多个异步操作时，每个操作都必须返回一个Promise对象，这可能会导致嵌套很深的链式结构，使代码难以阅读和理解。此外，<font color ="yellow">Promise chaining也不容易处理错误，因为错误处理必须在每个then()方法中进行，而不是在catch()方法中集中处理</font>。
这就是为什么在ES8中引入了async和await关键字，以简化处理异步操作的代码。使用async和await，我们可以将异步操作转换为同步操作的形式，使代码更加清晰和易于阅读。


- async：async函数是一个异步函数，它返回一个<font color="red"><strong>Promise对象</strong></font>，可以使用await关键字来等待异步操作的结果。async函数可以包含多个异步操作，这些操作可以使用await关键字来等待其结果，而不必使用回调函数或Promise链来处理。
    
- await：await关键字只能在async函数内部使用，它可以等待Promise对象的状态变化，如果Promise对象的状态变为resolved，则返回Promise对象的结果；如果Promise对象的状态变为rejected，则抛出Promise对象的错误。使用await关键字可以简化异步代码的编写，使异步代码看起来更像同步代码。
    

使用async和await可以使异步代码更加易读和易于维护，例如以下是一个使用Promise chaining处理异步操作的示例：

``` js
fetch('https://jsonplaceholder.typicode.com/posts')
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => console.error(error));
```

而使用async和await关键字，我们可以将其转换为以下代码：

``` js
async function fetchPosts() {
  try {
    const response = await fetch('https://jsonplaceholder.typicode.com/posts');
    const data = await response.json();
    console.log(data);
  } catch (error) {
    console.error(error);
  }
}

fetchPosts();
```

在这个例子中，我们使用async和await关键字来等待fetch()方法返回的Promise对象，并使用try-catch块来处理错误。这使得我们的代码更加简洁和易于阅读。

### 异步处理

异步处理在JavaScript中被广泛使用，因为JavaScript通常在浏览器中运行，并且需要处理许多异步操作，例如：

1. 处理用户交互：当用户与Web应用程序交互时，例如提交表单、点击按钮等，需要执行异步操作来更新UI，并响应用户交互。
    
2. 处理网络请求：在Web应用程序中，需要从服务器获取数据或将数据发送到服务器。由于网络请求需要较长的时间才能完成，因此需要在异步操作中执行它们。
    
3. 处理文件读写：在Node.js环境中，需要读取或写入文件时，这也需要异步操作。
    
4. 处理定时器：JavaScript中的定时器函数（如setInterval()和setTimeout()）是异步的，因为它们在一段时间后才会执行代码。
    

因此，当需要处理这些异步操作时，需要使用异步处理。在JavaScript中，可以使用回调函数、Promise、async/await等方法来处理异步操作。