### Tree-shaking

早上起来看到有人在 `segmentfault` 上提问关于 `Tree-shaking` 的问题，因为刚好了解过一点，所以简单得回答了一下，并准备研究和实践一下。

#### 是什么？

目前前端模块化开发，通过 entry 打 bundle 的模式是主流，`Tree-shaking` 作为这种模式的一种优化策略出现。试想一下，在我们使用模块的时候，通常会有没有使用到的代码，尤其是第三方的模块，这部分的代码量有时候是很大的，`Tree-shaking` 就为了消除这一部分无用的代码而出现的。目前两个现代化的模块打包器 rollup 和 webpack2 都已经支持了这种技术。

从实现上来说，`Tree-shaking` 就是先通过静态分析模块间的依赖，确定无用的 `export`，然后通过 Uglifyjs 把没有用的 `export` 的内容去除，去除的方法其实是比较复杂的，因为没法静态分析很多关系无法确定。因此很多情况下为了程序的正确性，只能做最坏的打算，把代码留下来，所以实际上还是有不少无用代码保留了下来。

#### 对比 DCE（dead code elimination）

DCE 无用代码移除。对于静态语言来说，做 DCE 相对简单，通过静态分析，找到执行不到的代码，从 AST（抽象语法树）里清除。说实话传统的 DCE 更符合 `Tree-shaking` 这种叫法，即摇一下把 AST 中的 dead branch 给抖下来。

而 js 是动态语言，没有类型，没办法做静态分析，所以 DCE 很难做。Uglifyjs 做过一些 DCE 的工作，不过也很有限。而 es6 模块静态化的设计，使 js 有了基于模块的静态分析能力，`Tree-shaking` 正是基于此。

根据 rollup 作者的说法来看，`Tree-shaking` 与传统的 DCE 有着很大的区别，它提到一个此 `Live code inclusion`，也就是说 `Tree-shaking` 并不是把无用的代码消除，而是找到需要的代码，灌入最终的结果。

#### 目前的缺陷

1. `Tree-shaking` 目前只工作在 AST 的顶层节点，是粗粒度的，还是有不少无用的代码难以清除。
2. `Tree-shaking` 目前也无法移除对象上无用的方法，并且很多情况下被迫设想最坏的情况来保证程序的正确性。这主要还是因为静态分析在 js 中很难做到导致的。
3. `Tree-shaking` 还不完善，需要结合 Uglifyjs 来使用。

> `Tree-shaking` 出现已经过去快一年了，在这一年里是否有改进的地方，这一点是后面要查一下的。不过从目前静态分析的有限性来看，应该不会有太多的改变。

#### 实践

github 上有个 webpack + babel 进行 `Tree-shaking` 的 demo，不过已经很久没有更新了，pr 也没有人来合并，所以自己照着改写了一个 demo。

clone：
```
git clone 未完成
```

安装：

```
cd about-tree-shaking/
npm install
```

两种运行方式：

* 一次性构建:
    * `npm run build`
    * 在浏览器中打开 `build/index.html`。
* 持续观察，如果文件改变，立即重新构建：
    * `npm run watch`
    * 在浏览器中打开 `build/index.html`，如果改变了文件则手动刷新。

#### 参考资料

1. [知乎相关讨论](https://www.zhihu.com/question/41922432)
2. [rollup 作者的文章](https://medium.com/@Rich_Harris/tree-shaking-versus-dead-code-elimination-d3765df85c80#.jippuamfz)