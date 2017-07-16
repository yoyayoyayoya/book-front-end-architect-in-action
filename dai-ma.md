# 代码

这章从了解和分析脚手架项目入手，去分析前端架构师如何在代码层面为团队成员解决实际中遇到的开发问题。
除非特殊说明，本书的默认编辑器为Atom。其所用的开发插件详见附录。建议各位读者在开始阅读本书前，参考附录将Atom配置完毕。

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

好了，所有脚本功能咱们都浅尝辄止了一遍。趁着接触下一节内容之前，尽情的在test分支中玩耍吧。



### 项目开发配置
我们从配置文件开始，带出其所属技术，逐个的去了解RSK所用到的项目开发技术。若无特殊说明，所有配置文件均放置在项目根目录下。

#### .editorconfig
[EditorConfig](http://editorconfig.org) 帮助开发者们在不同的编辑器与IDE之间定义与维护一致性的代码开发风格。主要作用说白了就是统一代码开发风格。比如说行结束符、缩进大小、字符集的设置等等。在实际的项目开发过程中，即使所有开发者都使用同一种开发工具，也不能确保所有开发者的代码开发风格能保持一致，大部分情况下会因为个人开发习惯而导致缩进大小不一致、字符集不同、行结束符不同的情况，造成项目开发中发生代码冲突，往往这些冲突会增加大量的代码重置和风格一致化调整带来的沟通成本。在以往大部分的项目管理中，会通过文档或强制规则来确保代码风格的统一性。但我们自己知道，通过人为的管理代码风格根本不靠谱。自动化的技术支持才是王道。EditorConfig为我们提供了一条康庄大道。具体的配置选项可以参考官网文档。这里我们只针对RSK中的常用配置做讲解。

> **root = true|false**
>
> &gt;&gt; 配置搜索停止标记(特殊配置项)。
>
>打开文件时，EditorConfig插件会在该文件所在目录以及每个父目录中查找名为.editorconfig的配置文件。查找操作会在到达根目录或包含该配置了该标记的配置文件时停止。之后插件自上而下的合并和应用所有配置文件中的配置项。包含该标记的最后一个配置文件会在最后读取。因此该文件的配置项会得到优先权。
>
>我们来假设两种配置类型来做具体说明，某个项目有3层目录，文件将在最后一层打开，此时每个目录各有一个.editorconfig配置文件。配置项分别为：
>#level1/.editorconfig
>
> indent_size = 2
>
> trim_trailing_whitespace = true
>
>#level2/.editorconfig
>
> end_of_line = lf
>
> charset = utf-8
>
>#level3/.editorconfig
>
> indent_size = 4
>
> end_of_line = crlf
>
> insert_final_newline = true
>
>第一种类型，不设置root = true 到任何一个配置文件，合并后所应用的配置项为：
>
> indent_size = 4 # 合并自level3
>
> charset = utf-8 # 合并自level2
>
> end_of_line = crlf # 合并自level3
>
> insert_final_newline = true # 合并自level3
>
> trim_trailing_whitespace = true # 合并自level1
>
>第二种类型，在level2的配置文件中加入标记root = true，合并后所应用的配置项为：
>
> indent_size = 4 # 合并自level3
>
> end_of_line = lf # 合并自level2，level2设置了停止标记，最后一个被读取，享用合并优先权.此时覆盖了level的配置
>
> charset = utf-8 # 合并自level2
>
> insert_final_newline = true # 合并自level3

---

> **indent_style = space|tab**
>
> &gt;&gt; 配置缩进符。
>
>一般情况下推荐使用space，毕竟各操作系统中对于tab的缩进大小不是统一的，对于使用缩进来做语法分析的语言，例如Python。会产生意料之外的代码错误。而使用space可规避此风险，并且在编辑器或IDE中你依然可以使用tab键来缩进。EditorConfig插件会自动帮你置换成对应的缩进大小。

---

> **indent_size = [NUMBER]**
>
> &gt;&gt; 配置缩进符。值为数值类型。
配置代码的缩进大小，常用配置为2或者4。当然你想要来个3也没人拦着。

---

> **end_of_line = crlf|lf|cr**
>
> &gt;&gt; 配置代码行结束符。crlf为Windows所用，lf为Linux所用，cr为Mac之类系统所用。
>
>此选项个人认为是必选项，重中之重。特别推荐设置为lf。一旦在项目开发中出现行结束符冲突，对于使用git为代码管理的项目，解决结束符冲突相当之麻烦，以至于会出现通过git打出的patch不能应用回git项目和diff不正确的极端情况。且现在的服务器大部分为Linux，即使是Windows服务器，应用lf也不会有太大影响。

---

> **charset = utf-8|utf-16be|...**
>
> &gt;&gt; 配置文件字符集。

---

> **trim_trailing_whitespace = true|false**
>
> &gt;&gt; 配置是否去除行尾的空白符。
>
> 对于使用git的项目来说，此配置也是必选。行尾出现的空白符会导致git的patch应用出错以及一些其他意想不到的异常错误。

---

> **insert_final_newline = true|false**
>
> &gt;&gt; 配置是否在最后一行插入空白行。
>
> 虽然在RSK中，此配置项不建议修改。但其实在项目开发过程中也不会有太大的影响。个人认为可根据需要配置。另外，在稍后要介绍的Lint工具中，也会出现跟此配置项的配置。

---

> **max_line_length = [NUMBER]|off**
>
> &gt;&gt; 配置是否根据指定的行字符长度强制截断代码行。off为关闭此功能。

---
> [更多配置项请参考此wiki](https://github.com/editorconfig/editorconfig/wiki/EditorConfig-Properties)
>
> [需要／不需要安装插件的编辑器和IDE列表参考此主页](http://editorconfig.org/)

#### .eslintrc.js
[ESLint](http://eslint.org/) 大名鼎鼎的代码校验工具Lint的ES版，配合Atom的插件可做到保存即自动检查、格式化代码、智能提示等诸多实用功能。RSK跟随流行趋势，选用了Airbnb开源的ES代码规范。跟着大公司走，没错的。要愉快的使用ESLint，需要对项目进行整套的配置与依赖包安装。它的配置方式也多种多样，可以通过文件内的注释、\*.eslintrc.\*、package.json、command line方式添加和应用校验规则。ESLint的配置主要分为4个部分：解析器、应用环境、全局变量、规则。
我们先跟随RSK来学习它的配置方式，等你熟练之后，再根据需要选择自己的配置方式。所谓贪多嚼不烂。   
RSK采用的是.eslintrc.js为主，package.json为辅助的方式。我们先来看看.eslintrc.js中都配置了哪些内容。   

> **parser: 'babel-eslint'**  
>
> &gt;&gt; 配置ES文件解析器，ESLint默认设置为Esprima。官方提到的解析器有以下三个：
> [Esprima](https://npmjs.com/package/esprima)
> [Babel-ESLint](https://npmjs.com/package/babel-eslint)
> [typescript-eslint-parser(Experimental) ](https://npmjs.com/package/typescript-eslint-parser)
> RSK选用babel的eslint包来解析ES文件。
> 对应的npm包参考： [babel-eslint](https://github.com/babel/babel-eslint)

---

> **extends: [   
      'airbnb',   
      'plugin:css-modules/recommended',   
    ]**
>
> &gt;&gt; 引入已经配置好的规则集。
> 此处应用的是Airbnb开源的规则集。
> 对应的npm包和style guide参考： [eslint-config-airbnb](https://github.com/airbnb/javascript)，建议多花些时间了解Airbnb都给我们规范了哪些代码规则。便于今后为项目做规则定制。
> 应用css-modules插件中的规则集。


---

> **plugins: [
      'css-modules',
    ]**
>
> &gt;&gt; 引入第三方插件，增强和扩展ESLint功能。以eslint-plugin-为前缀的插件，可以在配置时省略该前缀。例如eslint-plugin-custom可以配置成custom。
> **提示：** 全局方式安装的ESLint只能应用基于全局安装的插件模块，而本地方式安装的ESLint，可以应用全局或本地插件模块。按照此提示，我建议各位按本地方式安装ESLint。
> RSK引入css-modules插件，检查代码引入的css／scss中的未被使用或未定义的类。对应的npm包和使用介绍参考：
[eslint-plugin-css-modules](https://github.com/atfzl/eslint-plugin-css-modules)

---

> **globals: {
      \__DEV__: true,
    }**
>
> &gt;&gt; 配置允许使用的全局变量。所有不在此配置范围的全局变量会被检测出并标为错误项。就当前配置而言，RSK仅允许使用\__DEV__这个全局变量。你可以在此配置其他你需要使用的全局变量。

---

> **env: {
      browser: true,
    }**
>
> &gt;&gt; 配置当前ESLint的应用环境。ESLint提供了多达25种应用环境。每个应用环境都带有预定义的全局变量。根据你自身的需要来添加或修改。RSK的默认配置为仅作用于浏览器环境。或者你也可以自定义一个应用环境分享给别人使用。

---

敲黑板划重点了，我们来看看最主要的校验规则是怎么配置的。

> **"rules":{"rule":value|[value, optionalValue]}**

> &gt;&gt;规则可以配置在注释和配置文件中，每条规则可设置3种值，每个值可以为string或number。   
> **"off"|0** - 关闭规则   
> **"warn"|1** - 开启警告模式，不影响代码。   
> **"error"|2** - 开启错误模式，当其触发时，exit code为1。   
> 除了上述值外，还可为规则配置可选值（可选值的值配置对应规则的不同也会有不同）。   
> 举个例子   
> /\* eslint quotes: ["error", "double"], curly: 2 \*/   
> "rules": {   
> &nbsp;&nbsp; "quotes": ["error", "double"],   
> &nbsp;&nbsp; "curly": 2   
> }   
> 上面两个配置是等价的。第一条规则限制了字符串的引号为双引号。第二条规则是禁止函数使用柯里化。两条规则都开启的是错误模式。   

>  了解了如何配置规则后，我们来看看RSK都为我们预设了哪些规则。   

> **'import/extensions': [   
&nbsp;&nbsp;&nbsp;&nbsp;'error',   
&nbsp;&nbsp;&nbsp;&nbsp;'always',   
&nbsp;&nbsp;&nbsp;&nbsp;{   
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;js: 'never',    
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;jsx: 'never',   
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;mjs: 'never',   
&nbsp;&nbsp;&nbsp;&nbsp;},   
      ]**
>
> &gt;&gt;extensions规则出自[import](https://github.com/benmosher/eslint-plugin-import)插件。之前说过插件是需要配置在plugins里的。而import并没有配置就直接在rules中引用其规则。不知道是不是隐藏属性。这条规则是定义如何校验导入文件的扩展名。extensions规则的值可以是string、object或者两者的组合。当string值为“never”（默认值）时，会限制在导入文件时使用任何扩展名。 “always”则是限定导入文件时必须指定其扩展名。如果你想分别指定不同的导入规则，可以使用object方式，例如{"js": "never", "json": "always"}是限定json文件必须指定扩展名，js文件不可以指定扩展名。   
> 根据extensions规则的使用描述，RSK中定义的extensions规则为js|jsx|mjs文件不可以指定扩展名，除此以外的其他文件类型必须指定扩展名，否则一律报错。

---

> **'import/no-extraneous-dependencies': 'off'**   
> &gt;&gt;同样来自import插件。它是禁止导入任何不在package.json的dependencies, devDependencies, optionalDependencies 或 peerDependencies中配置的模块。它会寻找离它最近的父文件夹中的package.json文件，如果没有找到，则不会lint任何代码。你可以配置packageDir来指定package.json。具体用法比较复杂，推荐查看其[文档](https://github.com/benmosher/eslint-plugin-import/blob/master/docs/rules/no-extraneous-dependencies.md)
> 由于[某些原因](https://github.com/benmosher/eslint-plugin-import/issues/458)，RSK关闭了这条校验规则。

---

> **'no-console': [
&nbsp;&nbsp;'error',
&nbsp;&nbsp;{
&nbsp;&nbsp;&nbsp;&nbsp;allow: ['warn', 'error', 'info'],
&nbsp;&nbsp;},
]**   
> &gt;&gt; ESLint 内置规则，它会限制console的使用范围。   
> RSK推荐的是不使用log来打印信息，而是用对应的warn|error|info。

---

> **'react/jsx-filename-extension': 'off'**   
> **'react/prefer-stateless-function': 'off'**   
> &gt;&gt;这两条规则出自[react](https://github.com/yannickcr/eslint-plugin-react)插件。它包含了大部分React校验规则。这里就留给你去慢慢了解了。
> RSK在这里关闭jsx文件扩展名和无状态函数的校验规则。

#### .flowconfig
[Flow](https://flow.org/)([中文版](https://zhenyong.github.io/flowtype/))。js静态类型检查器。它可以提供运行时的类型检查，在js运行前帮我们找出常见bug，立刻检测代码变化，在开发js时快速不断的提供反馈。与它类似的应该是TypeScript了。在做技术选型的时候，我们根据组员的接受能力与学习曲线来决定是否Flow还是TypeScript。无论选择谁，类型检查对于咱架构师而言都是出门在外，居家旅行的必备工具。当面对团队成员经验和能力参差不齐、业务复杂、开发时间紧的情况，光靠ESLint肯定是不够的，漂亮的代码并不能保证功能的正确性。   

Flow的启用很简单，但具体的设置稍后再讲，我们先从[配置](https://flow.org/en/docs/config/)([中文版](https://zhenyong.github.io/flowtype/docs/advanced-configuration.html#_))开始入手。默认情况下，Flow会以.flowconfig所在的目录为根目录开始检测（所有文件和子目录）。   

配置共有5个部分：(这里偷点懒，直接引用了Flow中文版的相关描述)   

**[include]**   
用来告诉 flow 还要检测哪些文件或者目录（所有子文件）。 这一栏配置的每一行表示一个待检测的路径，可以使用相对于根目录的路径，或者绝对路径， 支持一个或多个星号通配符。   
举例说明，如果 /path/to/root/.flowconfig 包含了下面配置。   
> [include]   
> ../externalFile.js   
> ../externalDir/   
> ../otherProject/\*.js   
> ../otherProject/\*\*/coolStuff/   

当 Flow 检测项目 /path/to/root 的时候，检测文件包括：   
/path/to/root/ (自动包含)   
/path/to/externalFile.js   
/path/to/externalDir/   
/path/to/otherProject/ 下所有以 .js 结尾的文件   
/path/to/otherProject 下任意名为 coolStuff/ 的目录   

**[ignore]**   
用来告诉 flow 哪些文件不需要检测， 路径匹配使用正则表达式。默认为空。   
以下几点要注意:   
1. 这是[OCaml 正则表达式](http://caml.inria.fr/pub/docs/manual-ocaml/libref/Str.html#TYPEregexp)。   
2. 这些正则表达式匹配的绝对路径. 通常应该以 .* 开头。     
3. [ignore] 在 [include] 之后， 如果同时 include 和 ignore 同个路径，那么就会 ignore。
举例说明：   

>[ignore]   
>.\*/__tests__/.\*   
>.\*/src/\(foo\|bar\)/.\*   
>.\*\\.ignore\\.js   

该 [ignore] 配置表示忽略：  

所有名为 __tests__ 目录下的目录和文件，
所有.\*/src/foo 或 .\*/src/bar下
所有以 .ignore.js 结尾命名的文件。
从 Flow v0.23.0 开始，你可以在正则中用&lt;PROJECT_ROOT&gt;占位符表示根目录的绝对路径。 对于相对路径的场景就很有用了，例如：

>[ignore]    
>&lt;PROJECT_ROOT&gt;/__tests__/.*   

这就忽略了项目根目录下 __tests__ 的目录和文件。   
不像之前的 .\*/__tests__/.\* 那样忽略了整个系统名为 __tests__ 的目录。   

**[libs]**   
当检测代码的时候， flow 就会包含指定的 声明 (接口文件)。   
[libs] 下每行配置表示要包含的一个目录或文件，相对与根目录的路径，或者绝对路径。   

**[options]**   
Flow配置最复杂的一部分，它可以指定emoji输出，log，额外检测的文件类型等诸多功能。各位客官请查看其文档（[英文版](https://flow.org/en/docs/config/options/)[中文版](https://zhenyong.github.io/flowtype/docs/advanced-configuration.html#options)）按需配置。   

**[version]**   
指定 Flow 的版本。   
如果没有配置或者留白, 则允许任意版本. 如果配置了，但是运行的版本不匹配，就报错退出。  
例如你配置 .flowconfig 为

>[version]   
>0.22.0   

你运行 Flow 版本未 v0.21.0, 则报错:   

"Wrong version of Flow. The config specifies version 0.22.0 but this is version 0.21.0"   

目前为止，Flow提供下列方式为指定支持的版本:   

1. 明确的版本, (e.g. 0.22.0, which only matches 0.22.0)。   
2. 范围, (例如 &gt;=0.13.0 &lt;0.14.0 将 匹配 0.13.0 和 0.13.5，单步包括 0.14.0)。   
3. Caret 范围，下界：左起第一个非0数字升1，后面改为0。     
^0.13.0 表示 &gt;=0.13.0 &lt;0.14.0    
^0.13.1 表示 &gt;=0.13.1 &lt;0.14.0   
^1.2.3" 表示 &gt;=1.2.3 &lt;2.0.0   

所有Flow的配置选项讲解完毕，现在你去看RSK配置的.flowconfig，是不是一目了然。   
下面的工具配置和使用不是很复杂。所以我就蜻蜓点水的介绍一下它们所对应的代码工具。
#### .nycrc   
[Istanbul](https://istanbul.js.org/) 是针对ES5和ES2015+的代码测试覆盖率检测工具。它支持多种测试框架包括[tap](https://github.com/tapjs/node-tap)、[mocha](https://github.com/mochajs/mocha)、[AVA](https://github.com/avajs/ava)等。覆盖率是代码流程中的一项重要指标。但到底它需要制定多少的覆盖率，我们架构师必须要根据实际情况来实时调整。100%那是一种理想，而现实就是很多时候为了达到100%的指标。测试就是很猥琐的为了覆盖而测试，并没有什么实际意义。大部分情况下70%～80%是个不错的区间。可根据项目紧急程度来下调覆盖率。不过时间再紧下调也得有个底线，底线覆盖率是代码质量的保障。   

#### .stylelintrc.js
[StyleLint](https://stylelint.io/)，css的lint工具，重要程度和ESLint一样。
后期会介绍一些CSS方法论比如BEM等来配合StyleLint。   

#### .travis.yml
[Travis CI](https://travis-ci.org/)。一款在线的GitHub项目CI工具。这个在开发GitHub项目且不需要整套CI和CD流程的情况下比较实用，不要自己安装CI工具即可使用，非常方便。在实际开发中这种在线的CI就不能满足使用了。一般来说是使用[Jenkins](https://jenkins.io/)、[TeamCity](https://www.jetbrains.com/teamcity/)、[BamBoo](https://www.atlassian.com/software/bamboo)等专业CI套件。
