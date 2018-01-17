# Promise in js

> 回调函数真正的问题在于他剥夺了我们使用 return 和 throw 这些关键字的能力。而 Promise 很好地解决了这一切。

2015 年 6 月，[ECMAScript 6 的正式版](https://link.jianshu.com?t=http://www.ecma-international.org/publications/standards/Ecma-262.htm) 终于发布了。

ECMAScript 是 JavaScript 语言的国际标准，JavaScript 是 ECMAScript 的实现。ES6 的目标，是使得 JavaScript 语言可以用来编写大型的复杂的应用程序，成为企业级开发语言。

## 概念

ES6 原生提供了 Promise 对象。

所谓 Promise，就是一个对象，用来传递异步操作的消息。它代表了某个未来才会知道结果的事件（通常是一个异步操作），并且这个事件提供统一的 API，可供进一步处理。

Promise 对象有以下两个特点。

（1）对象的状态不受外界影响。Promise 对象代表一个异步操作，有三种状态：Pending（进行中）、Resolved（已完成，又称 Fulfilled）和 Rejected（已失败）。只有异步操作的结果，可以决定当前是哪一种状态，任何其他操作都无法改变这个状态。这也是 Promise 这个名字的由来，它的英语意思就是「承诺」，表示其他手段无法改变。

（2）一旦状态改变，就不会再变，任何时候都可以得到这个结果。Promise 对象的状态改变，只有两种可能：从 Pending 变为 Resolved 和从 Pending 变为 Rejected。只要这两种情况发生，状态就凝固了，不会再变了，会一直保持这个结果。就算改变已经发生了，你再对 Promise 对象添加回调函数，也会立即得到这个结果。这与事件（Event）完全不同，事件的特点是，如果你错过了它，再去监听，是得不到结果的。

有了 Promise 对象，就可以将异步操作以同步操作的流程表达出来，避免了层层嵌套的回调函数。此外，Promise 对象提供统一的接口，使得控制异步操作更加容易。

Promise 也有一些缺点。首先，无法取消 Promise，一旦新建它就会立即执行，无法中途取消。其次，如果不设置回调函数，Promise 内部抛出的错误，不会反应到外部。第三，当处于 Pending 状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）。

    <span class="hljs-keyword">var</span> promise = <span class="hljs-keyword">new</span> <span class="hljs-built_in">Promise</span>(<span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">resolve, reject</span>) </span>{
     <span class="hljs-keyword">if</span> (<span class="hljs-comment">/* 异步操作成功 */</span>){
     resolve(value);
     } <span class="hljs-keyword">else</span> {
     reject(error);
     }
    });

    promise.then(<span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">value</span>) </span>{
     <span class="hljs-comment">// success</span>
    }, <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">value</span>) </span>{
     <span class="hljs-comment">// failure</span>
    });
    `</pre>

    Promise 构造函数接受一个函数作为参数，该函数的两个参数分别是 resolve 方法和 reject 方法。

    如果异步操作成功，则用 resolve 方法将 Promise 对象的状态，从「未完成」变为「成功」（即从 pending 变为 resolved）；

    如果异步操作失败，则用 reject 方法将 Promise 对象的状态，从「未完成」变为「失败」（即从 pending 变为 rejected）。

    ## 基本的 api

1.  Promise.resolve()
2.  Promise.reject()
3.  Promise.prototype.then()
4.  Promise.prototype.catch()
5.  Promise.all()    // 所有的完成
    <pre class="hljs javascript">`<span class="hljs-keyword">var</span> p = <span class="hljs-built_in">Promise</span>.all([p1,p2,p3]);
    `</pre>
6.  Promise.race()       // 竞速，完成一个即可

    ## 进阶

    promises 的奇妙在于给予我们以前的 return 与 throw，每个 Promise 都会提供一个 then() 函数，和一个 catch()，实际上是 then(null, ...) 函数，

    <pre class="hljs ruby">`    somePromise().<span class="hljs-keyword">then</span>(functoin(){
            <span class="hljs-regexp">//</span> <span class="hljs-keyword">do</span> something
        });

    `</pre>

    我们可以做三件事，

    1. return 另一个 promise

    2. return 一个同步的值 (或者 undefined)

    3. throw 一个同步异常 `throw new Eror('');`

    #### 1. 封装同步与异步代码

    <pre class="hljs javascript">`<span class="hljs-string">``</span><span class="hljs-string">`
    new Promise(function (resolve, reject) {
    resolve(someValue);
    });
    `</span><span class="hljs-string">``</span>
    写成

    <span class="hljs-string">``</span><span class="hljs-string">`
    Promise.resolve(someValue);
    `</span><span class="hljs-string">``</span>
    `</pre>

    #### 2. 捕获同步异常

    <pre class="hljs javascript">` <span class="hljs-keyword">new</span> <span class="hljs-built_in">Promise</span>(<span class="hljs-function"><span class="hljs-keyword">function</span> (<span class="hljs-params">resolve, reject</span>) </span>{
     <span class="hljs-keyword">throw</span> <span class="hljs-keyword">new</span> <span class="hljs-built_in">Error</span>(<span class="hljs-string">'悲剧了，又出 bug 了'</span>);
     }).catch(<span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">err</span>)</span>{
     <span class="hljs-built_in">console</span>.log(err);
     });
    `</pre>

    如果是同步代码，可以写成

    <pre class="hljs javascript">`    <span class="hljs-built_in">Promise</span>.reject(<span class="hljs-keyword">new</span> <span class="hljs-built_in">Error</span>(<span class="hljs-string">"什么鬼"</span>));

    `</pre>

    #### 3. 多个异常捕获，更加精准的捕获

    <pre class="hljs php">`somePromise.then(<span class="hljs-function"><span class="hljs-keyword">function</span><span class="hljs-params">()</span> </span>{
     <span class="hljs-keyword">return</span> a.b.c.d();
    }).<span class="hljs-keyword">catch</span>(TypeError, <span class="hljs-function"><span class="hljs-keyword">function</span><span class="hljs-params">(e)</span> </span>{
     <span class="hljs-comment">//If a is defined, will end up here because</span>
     <span class="hljs-comment">//it is a type error to reference property of undefined</span>
    }).<span class="hljs-keyword">catch</span>(ReferenceError, <span class="hljs-function"><span class="hljs-keyword">function</span><span class="hljs-params">(e)</span> </span>{
     <span class="hljs-comment">//Will end up here if a wasn't defined at all</span>
    }).<span class="hljs-keyword">catch</span>(<span class="hljs-function"><span class="hljs-keyword">function</span><span class="hljs-params">(e)</span> </span>{
     <span class="hljs-comment">//Generic catch-the rest, error wasn't TypeError nor</span>
     <span class="hljs-comment">//ReferenceError</span>
    });
    `</pre>

    #### 4. 获取两个 Promise 的返回值

    <pre class="hljs bash">`1. .<span class="hljs-keyword">then</span> 方式顺序调用
    2. 设定更高层的作用域
    3. spread
    `</pre>

    #### 5. finally

    <pre class="hljs cpp">`任何情况下都会执行的，一般写在 <span class="hljs-keyword">catch</span> 之后
    `</pre>

    #### 6. bind

    <pre class="hljs javascript">`somethingAsync().bind({})
    .spread(<span class="hljs-function"><span class="hljs-keyword">function</span> (<span class="hljs-params">aValue, bValue</span>) </span>{
     <span class="hljs-keyword">this</span>.aValue = aValue;
     <span class="hljs-keyword">this</span>.bValue = bValue;
     <span class="hljs-keyword">return</span> somethingElseAsync(aValue, bValue);
    })
    .then(<span class="hljs-function"><span class="hljs-keyword">function</span> (<span class="hljs-params">cValue</span>) </span>{
        <span class="hljs-keyword">return</span> <span class="hljs-keyword">this</span>.aValue + <span class="hljs-keyword">this</span>.bValue + cValue;
    });
    `</pre>

    或者 你也可以这样

    <pre class="hljs javascript">`<span class="hljs-keyword">var</span> scope = {};
    somethingAsync()
    .spread(<span class="hljs-function"><span class="hljs-keyword">function</span> (<span class="hljs-params">aValue, bValue</span>) </span>{
     scope.aValue = aValue;
     scope.bValue = bValue;
     <span class="hljs-keyword">return</span> somethingElseAsync(aValue, bValue);
    })
    .then(<span class="hljs-function"><span class="hljs-keyword">function</span> (<span class="hljs-params">cValue</span>) </span>{
     <span class="hljs-keyword">return</span> scope.aValue + scope.bValue + cValue;
    });
    `</pre>

    然而，这有非常多的区别，

1.  你必须先声明，有浪费资源和内存泄露的风险
2.  不能用于放在一个表达式的上下文中
3.  效率更低

    #### 7. all。非常用于于处理一个动态大小均匀的 Promise 列表

    #### 8. join。非常适用于处理多个分离的 Promise

    <pre class="hljs php">````
    <span class="hljs-keyword">var</span> join = Promise.join;
    join(getPictures(), getComments(), getTweets(),
    <span class="hljs-function"><span class="hljs-keyword">function</span><span class="hljs-params">(pictures, comments, tweets)</span> </span>{
    console.log(<span class="hljs-string">"in total: "</span> + pictures.length + comments.length + tweets.length);
    });
    ```
    `</pre>

    #### 9. props。处理一个 promise 的 map 集合。只有有一个失败，所有的执行都结束

    <pre class="hljs javascript">`<span class="hljs-string">``</span><span class="hljs-string">`
    Promise.props({
    pictures: getPictures(),
    comments: getComments(),
    tweets: getTweets()
    }).then(function(result) {
    console.log(result.tweets, result.pictures, result.comments);
    });
    `</span><span class="hljs-string">``</span>
    `</pre>

    #### 10. any 、some、race

    <pre class="hljs php">````
    Promise.some([
    ping(<span class="hljs-string">"ns1.example.com"</span>),
    ping(<span class="hljs-string">"ns2.example.com"</span>),
    ping(<span class="hljs-string">"ns3.example.com"</span>),
    ping(<span class="hljs-string">"ns4.example.com"</span>)
    ], <span class="hljs-number">2</span>).spread(<span class="hljs-function"><span class="hljs-keyword">function</span><span class="hljs-params">(first, second)</span> </span>{
    console.log(first, second);
    }).<span class="hljs-keyword">catch</span>(AggregateError, <span class="hljs-function"><span class="hljs-keyword">function</span><span class="hljs-params">(err)</span> </span>{
    `</pre>

    err.forEach(function(e) {

    console.error(e.stack);

    });

    });;

    ```

    有可能，失败的 promise 比较多，导致，Promsie 永远不会 fulfilled

    #### 11. .map(Function mapper [, Object options])

    <pre class="hljs undefined">`用于处理一个数组，或者 promise 数组，
    `</pre>

    Option: concurrency 并发现

    <pre class="hljs css">`    <span class="hljs-selector-tag">map</span>(..., {<span class="hljs-attribute">concurrency</span>: <span class="hljs-number">1</span>});
    `</pre>

    以下为不限制并发数量，读书文件信息

    <pre class="hljs javascript">`<span class="hljs-keyword">var</span> <span class="hljs-built_in">Promise</span> = <span class="hljs-built_in">require</span>(<span class="hljs-string">"bluebird"</span>);
    <span class="hljs-keyword">var</span> join = <span class="hljs-built_in">Promise</span>.join;
    <span class="hljs-keyword">var</span> fs = <span class="hljs-built_in">Promise</span>.promisifyAll(<span class="hljs-built_in">require</span>(<span class="hljs-string">"fs"</span>));
    <span class="hljs-keyword">var</span> concurrency = <span class="hljs-built_in">parseFloat</span>(process.argv[<span class="hljs-number">2</span>] || <span class="hljs-string">"Infinity"</span>);

    <span class="hljs-keyword">var</span> fileNames = [<span class="hljs-string">"file1.json"</span>, <span class="hljs-string">"file2.json"</span>];
    <span class="hljs-built_in">Promise</span>.map(fileNames, <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">fileName</span>) </span>{
     <span class="hljs-keyword">return</span> fs.readFileAsync(fileName)
     .then(<span class="hljs-built_in">JSON</span>.parse)
     .catch(<span class="hljs-built_in">SyntaxError</span>, <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">e</span>) </span>{
     e.fileName = fileName;
     <span class="hljs-keyword">throw</span> e;
     })
    }, {<span class="hljs-attr">concurrency</span>: concurrency}).then(<span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">parsedJSONs</span>) </span>{
     <span class="hljs-built_in">console</span>.log(parsedJSONs);
    }).catch(<span class="hljs-built_in">SyntaxError</span>, <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">e</span>) </span>{
     <span class="hljs-built_in">console</span>.log(<span class="hljs-string">"Invalid JSON in file "</span> + e.fileName + <span class="hljs-string">": "</span> + e.message);
    });
    `</pre>

    结果

    <pre class="hljs ruby">`$ sync &amp;&amp; echo <span class="hljs-number">3</span> &gt; <span class="hljs-regexp">/proc/sys</span><span class="hljs-regexp">/vm/drop</span>_caches
    $ node test.js <span class="hljs-number">1</span>
    reading files <span class="hljs-number">35</span>ms
    $ sync &amp;&amp; echo <span class="hljs-number">3</span> &gt; <span class="hljs-regexp">/proc/sys</span><span class="hljs-regexp">/vm/drop</span>_caches
    $ node test.js Infinity
    reading <span class="hljs-symbol">files:</span> <span class="hljs-number">9</span>ms
    `</pre>

    #### 11. .reduce(Function reducer [, dynamic initialValue]) -&gt; Promise

    <pre class="hljs javascript">`<span class="hljs-built_in">Promise</span>.reduce([<span class="hljs-string">"file1.txt"</span>, <span class="hljs-string">"file2.txt"</span>, <span class="hljs-string">"file3.txt"</span>], <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">total, fileName</span>) </span>{
     <span class="hljs-keyword">return</span> fs.readFileAsync(fileName, <span class="hljs-string">"utf8"</span>).then(<span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">contents</span>) </span>{
     <span class="hljs-keyword">return</span> total + <span class="hljs-built_in">parseInt</span>(contents, <span class="hljs-number">10</span>);
     });
    }, <span class="hljs-number">0</span>).then(<span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">total</span>) </span>{
     <span class="hljs-comment">//Total is 30</span>
    });

### 12. Time

1.  .delay(int ms) -&gt; Promise
2.  .timeout(int ms [, String message]) -&gt; Promise

## Promise 的实现

1.  [q](https://link.jianshu.com?t=https://github.com/kriskowal/q)
2.  [bluebird](https://link.jianshu.com?t=https://github.com/petkaantonov/bluebird)
3.  [co](https://link.jianshu.com?t=https://github.com/tj/co)
4.  [when](https://link.jianshu.com?t=https://github.com/cujojs/when)

## ASYNC

async 函数与 Promise、Generator 函数一样，是用来取代回调函数、解决异步操作的一种方法。它本质上是 Generator 函数的语法糖。async 函数并不属于 ES6，而是被列入了 ES7。

参考文献（说是抄也可以的）：

[阮一峰的ES6 教程](https://link.jianshu.com?t=http://es6.ruanyifeng.com/#docs/promise)

[关于promises，你理解了多少？](https://link.jianshu.com?t=http://t.cn/RLxFIB3)

[Bluebird 的官方文档](https://link.jianshu.com?t=https://github.com/petkaantonov/bluebird/blob/master/API.md#delayint-ms---promise)
