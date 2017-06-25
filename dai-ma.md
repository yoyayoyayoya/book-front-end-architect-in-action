# 代码

这章从了解和分析脚手架项目入手，去分析前端架构师如何在代码层面为团队成员解决实际中遇到的开发问题。

[项目脚手架设计](#项目脚手架设计)

[实例分析](#实例分析)

## 项目脚手架设计

现代前端开发中所涉及的框架、工具类库种类繁多，让人眼花缭乱的同时也给项目开发带来一定麻烦。即使限定了某类框架，不同的开发人员就会搭建出不同风格组合。同一种功能特性也能实现出不尽相同的开发架构。前端项目功能差异化、各种项目结构不一致性的特点，造成了开发过程中费时费力，重复搭建的弊端。前端架构师的首要任务就是为团队设计一个功能需求明确、代码规范和构建流程自动化、易于测试／部署的脚手架项目。把团队成员从繁复的项目搭建中解脱出来。

### 何为脚手架

> 提供开发风格一致性的项目环境、目录结构和代码模版，规范统一项目所需的开发工具和流程管理。实现代码检查、测试、部署／发布自动化。

### 设计原则

> 1. 高度可配置
> 2. 代码检查自动化
> 3. 代码测试自动化
> 4. 一站式的产品级代码发布／部署
> 5. 模块化和语义化的项目结构
> 6. 极致统一的开发风格
> 7. 开发架构明确清晰且可替换
> 8. 快速、高效、稳定

## 实例分析

[React Starter Kit \(RST\)](https://github.com/kriasoft/react-starter-kit) 是kriasoft开源的一款基于React的产品级脚手架项目，集成了大部分现代级主流前端开发技术栈。能让你更加快速、高效、稳定的开发前端项目，它有以下特点：

> **组件驱动式UI**
>
> -支持基于组件的开发，提供了一种编写具有一致性API的小型独立UI元素的方式，该API可以轻松地作为大型应用程序的一部分进行编排。
>
> **预配置的开发工具**
>
> -提供了基于babel和webpack优化的构建工具。
>
> -由BrowserSync、HMR、React Hot Transform所支持的热部署web开发服务器。
>
> **SEO友好的同构架构**
>
> -客户端与服务端共享开发代码（UI，路由，数据拉取）
>
> -加快页面初始化时间
>
> -预渲染CSS
>
> **高度定制化**

特别的，RST前后端数据交互使用的是GraphQL技术，能够高效的定制化前端数据组件，摆脱了版本化严重的RestFul API，同时也减少了数据负载和交互次数。在后续的定制设计中，我们将会引入Apollo框架来处理客户端和服务端的GraphQL数据。在整个RST中，各种技术纷繁复杂，我们先抛开他们，由浅入深，慢慢品味这款脚手架给我们带来的乐趣。

### 配置RST环境

本书的所有分析环境都是基于git的，所以在继续探索本书之前，还请各位能够确保git已经在你的系统中安家。

确定git可用后，让我们从github上clone一份RST并把它放到rst目录并进入其中。除非文中特别说明，之后书中都以rst来代称脚手架目录。

```bash
git clone https://github.com/kriasoft/react-starter-kit.git rst && cd rst
```

当然，也为了能够自由进行探索之旅，我们先开一个test的git分支，便于我们一会都能为所欲为而不用担心破坏RST的文件和目录结构。

```bash
git checkout -b test
```

用git branch命令查看当前branch信息，您的git结构应该与下图类似。

![](/assets/rsk_branch_test.png)

### 品尝package.json

对于NodeJs项目，最佳的分析入口非package.json莫属了，不过我们也不能逐条逐句的解读它，所以先抛开具体技术细节，先来看看RST能够让我们做些什么。如果说package.json是一本菜单的话，“scripts”条目就是其中的主菜部分，那么它有些什么美味呢？

> _lint:js_
>
> &gt;&gt;用lint工具对src、tools目录进行js代码规范检查，帮我们找出js代码中不符合规范或者容易踩坑的代码。

运行以下命令去感受一下。

```bash
yarn run lint:js
```

![](/assets/lint_js.png)嗯，目前还没有什么错误，继续。

这里允许我偷个懒啊，接下来的所有命令都是以 yarn run xxxx展开，我就负责贴图，各位客官尽兴！

> _lint:css_
>
> &gt;&gt;同样的，用lint去对css做检查。它的检查范围是所有文件夹中的css。你也许会疑惑，css文件通常不是放在某个静态文件目录中吗？对，通常是的，但RST中组件都是组件化开发的，css文件会放在对应的组件目录中，我们先按此不表，之后文中会带你进一步了解。

![](/assets/lint_css.png)

> lint:staged
>
> &gt;&gt;这个比较好玩，为什么呢？它会查看所有处于unstaged/staged中的改动，然后再一一进行lint检查，是的js、css、sh等主要文件类型一个都不会放过。提醒一下，如果检查不通过，它会按照检查规则修改你的代码。

我们在rsk当前位置新建两个文件：index.js、index.css。随便在文件中填上以下内容，git add到branch变成staged状态，执行lint:staged检查，看看会发生什么。

```js
// index.js
let i= 0;
console.log(i);
```

```css
// index.css
:root {
  background: green;
}

h1 {
  front-size: 1rem
}
```

```bash
git add .
```

![](/assets/lint_staged.png)

由于我们没有创建其他类型的文件，所以第一个task被skiped了。lint检查到了js和css中的错误代码的同时也帮忙修改了它能修改的部分。来看看它都改了啥：css中加上了漏掉的分号，js中把let改成了const。（具体的lint规则设置在此先忽略）

![](/assets/lint_staged_diff.png)

现在我们把css和js按照lint结果做修改，再看看lint的结果如何。修改后的文件内容如下: \(你可以尝试不add改动，测试一下会不会lint unstaged的内容\)

```css
// index.css

:root {
  background: green;
}

h1 {
  font-size: 1rem;
}
```

```js
// index.js

const i = 0;
console.log(i); // eslint-disable-line
```

```bash
git add .
```

![](/assets/lint_staged_pass.png)Yes, 检查通过。继续探索其余美味功能。

> _lint_
>
> &gt;&gt;综合了lint:js lint:css的功能
>
> _test_
>
> &gt;&gt;用mocha框架做js测试
>
> _test:watch_
>
> &gt;&gt;自动监测文件的改动并实时测试
>
> _test:cover_
>
> &gt;&gt;计算js测试覆盖率并输出到控制台
>
> _coverage_
>
> &gt;&gt;计算js测试覆盖率，同时输出到控制台和web页面
>
> _clean_
>
> &gt;&gt;清除build目录的所有文件
>
> _copy_
>
> &gt;&gt;复制项目构建所需的静态文件资源到build目录

注意，主厨推荐来了。

> _bundle_
>
> &gt;&gt;打包编译出客户端\(vendor.js、about.js等\)和服务端\(server.js\)代码文件。由于采用了代码分离设计，所以每个单页面都会打包出各自的js文件以及对应的map。

![](/assets/bundle.png)

> _build_
>
> &gt;&gt;构建整个项目的代码和静态资源。可以配合各种参数来产出不同类型的代码。在后面的项目构建章节中会具体讲到。
>
> _build:stats_
>
> &gt;&gt;构建出产品级代码和资源\(压缩和混淆\)，分析js代码模块的结构和依赖关系并生成分析报告，该报告在[http://127.0.0.1:8888](http://127.0.0.1:8888)

![](/assets/analysis.png)

> _deploy_
>
> &gt;&gt;根据项目配置自动部署产品资源到服务器。
>
> _render_
>
> &gt;&gt;web页面静态直出，也是通常所说的服务端渲染SSR。直出的html页面会放置到build/public目录。可以在相关配置中设置需要直出的页面。该脚本命令的主要功能会在性能优化章节中详解。需要注意的是，在执行render之前需要运行build提前将相关js资源构建好。

![](/assets/render.png)

> _serve_
>
> &gt;&gt;启动server，提供web服务。
>
> _start_
>
> &gt;&gt;这是是跟我们最息息相关也是开发中用的最多的功能，它就是启动development开发模式，该模式会启动web 服务并提供文件监控和热替换等常用功能。使用RST就是从简单的yarn start开始。由于它功能众多，具体细节将会在项目开发章节中详解。

好了，所有功能咱们都浅尝辄止了一遍。趁着接触下一节内容之前，尽情的在test分支中玩耍吧。

