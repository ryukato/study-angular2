# [Webpack](https://webpack.js.org)
Application 개발 시, Javascript module을 build하는 기능을 제공하는 Tool이다. 각 Javascript파일의 import구문과 각 파일간 dependency를 분석하여 하나의 module 파일로 생성하는 기능을 제공한다. 그리고 아래와 같은 기능을 제공한다. 
* constructing a dependency graph
* customising optimizations to application code
* Running a development server (* hot re-loading)

또한 Webpack 세세한 설정을 할 수 있도록 지원하지만 아래의 기본 개념은 알고 있어야 한다.

## Concepts
### [Entry](https://webpack.js.org/concepts/#entry)
Webpack은 application code를 분석하여 의존관계를 분석한다. 이때 의존 관계를 분석하기 위한 시작점이 entry point이며, 이를 지정하기 위해 **entry**를 사용 및 설정한다.

#### Syntax
```entry: {[entryChunkName string]: string|Arrat<string>}```


###### webpack.config.js (single entry)
```
const config = {
	entry: './path/to/my/entry/file.js'
}
module.exports = config;
```

###### webpack.config.js (multi entry)
```
const config = {
	entry: ['./path1/to/my/entry/file.js', './path2/to/my/entry/file.js']
}
module.exports = config;
```

###### webpack.config.js (multi entry)
```
const config = {
	entry: {
		app: './src/app.js',
		vendors: './src/vendors.js' // --> starting from vendors.js, creates chunk file includes all dependent files
	}
}
module.exports = config;
```

#### Multi Page application
CommonsChunkPlugin을 사용하면 각 페이지별가 공통으로 사용하는 코드를 뽑아내서 별도의 bundle로 만들 수 있다. 
###### webpack.config.js

```
const config = {
	entry: {
		pageOne: './src/pageOne/index.js',
		pageTwo: './src/pageTwo/index.js',
		pageThree: './src/pageThree/index.js',
	}
}
```

### [Output](https://webpack.js.org/concepts/#output)
```output```option은 compile된 파일들을 어디에, 어떤 파일명으로 저장해야 하는지를 설정하도록 한다. 단 여러개의 entry가 있을 수 있지만 output은 하나만 존재한다. 

```OccurenceOrderPlugin ```혹은 ```recordsPath```를 사용하면 모듈간 일정한 순서를 보장할 수 있다. 

#### Syntax

```
output: {
	filename: 'bundle.js',
	path: '/home/proj/public/assets'
}
```

###### webpack.config.js (with single entry)
```
const path = require('path'); //nodeJs의 path모듈 import

module.exports = {
	entry: '.path/to/my/entry/file.js',
	output: {
		path: path.resolve(__dirname, 'dist'), // --> [project path]/dist
		filename: 'my-first-webpack.bundle.js'
	}
};
```

###### webpack.config.js (multi entries)

```
const config = {
  entry: {
    app: './src/app.js',
    search: './src/search.js'
  },
  output: {
    filename: '[name].js',  // name will be 'app' and 'search'
    path: __dirname + '/build'
  }
}

module.exports = config;
```

#### More options
[output more options](https://webpack.js.org/concepts/output/#options)

### [Loader](https://webpack.js.org/concepts/#loaders)
Webapck은 Javascript로 작성되어야 하지만, 대부분의 웹 자원(.css, .html, .scss, .jpg...)들을 다룰 수 있다. 이때 **Loader**는 이런 파일들을 그들의 의존관계에 따라 모듈로 구성할 수 있다. 그러기 위해선 각 loader별로 다룰 수 있는 파일인지 여부(test)와 어떻게 bundle로 구성할지(use)를 지정해 주어야 한다.

###### webpack.config.js

```
const path = require('path');

const config = {
	entry: './path/to/my/entry/file.js',
	output: {
		path: path.resove(__dirname, 'dist'),
		filename: 'my-first-webpack.bundle.js'
	},
	module: { 
		rules: [
			{
				test: /\.(js|jsx)$/, // 정규식을 사용하여 js, jsx 파일 확장자를 가진 파일을 모두 골라낸다.
				use: 'babel-loader' //위의 파일들을 loading하기 위해 babel-loader를 사용한다.
			}
		]
	}
};

module.exports = config;
```
*NOTE* ```Loader를 선언할때 loaders 혹은 rules로 바로 선언하는 것이 아닌, ```**modules**```하위에 선언하는 것에 유의해야 한다.```

#### Loaders
* css: css-loader
* typescript: ts-loader
* jade: jade-loader
* json: json-loader

###### webpack.config.js

```
const config = {
	module: {
		rules: [
			{
				test: /\.css$/, loader: 'css-loader'
			},
			{
				test: /\.css$/, use: 'css-loader'
			},
			{
				test: /\.css$/,
				use: [
					{ loader: 'style-loader' },
					{
						loader: 'css-loader',
						options: {
							modules: true
						}
					}
				]
			},
		]
	}
};
module.exports = config;
```
###### require statement

```
require('style-loader!css-loader?modules!./styles/css');
```
###### cli commands

```
webpack --module-bind jade-loader --module-bind 'css=style-loader!css-loader'
```
.css file -> loaded by style-loader and css-loader, jade file -> loaded by jade-loader

#### [Loader Features](https://webpack.js.org/concepts/loaders/#loader-features)
#### [Resolving Loaders](https://webpack.js.org/concepts/loaders/#resolving-loaders)

### [Plugins](https://webpack.js.org/concepts/#plugins)


## 설치
webpack을 설치하기 위해선 먼저 node 및 npm(node package manager)가 설치되어 있어야 한다.

### 예제 프로젝트 생성

```
mkdir webpack-demo && cd webpack-demo
npm init -y
npm install --save-dev webpack
```

### Sample codes

아래의 코드내의 component함수는 lodash에 대한 의존관계를 가진다. 

#### version-1
##### index.js

```
function component(){
  console.log(_.join(['Hello', 'webpack'],' '));
}

```
##### index.html
이전 방식으로 lodash를 먼저 참고하고  lodash를 참조하는 index.js를 나중에 참조하도록 선언하였다.
이 방식은 예전부터 많이 사용하던 방식이지만, 순서 혹은 참조하는 것을 빠드리게 되면 application이 동작하지 않을 수 있다. 
```
<html>
  <head>
    <title>webpack 2 demo</title>
    <script src="https://unpkg.com/lodash@4.16.6"></script>
  </head>
  <body>
    <script src="app/index.js"></script>
  </body>
</html>

```

#### version-2
lodash를 npm으로 먼저 설치한다. 다음의 명령어로 설치된 package는 package.json에 자동으로 추가된다. 

```
npm install --save-dev lodash
```

##### index.js
아래와 같이 lodash를 import한다. 

```
import _ from 'lodash';

function component(){
  console.log(_.join(['Hello', 'webpack'],' '));
}

```

##### webpack build
######  build할 파일을 모두 열거하여 build
```
node_modules/.bin/webpack app/index.js dist/bundle.js
```
######  webpack.config.js를 통해 build

```
node_modules/.bin/webpack
```

##### index.html
아래의 코드에서 **dist/bundle.js**는 webpack을 통해 build한 결과로 나오는 js 파일이다. 
해당 js 파일은 lodash와 index.js가 합쳐진 형태로 나오게 된다. 

```
<html>
  <head>
    <title>webpack 2 demo</title>
  </head>
  <body>
    <script src="dist/bundle.js"></script>
  </body>
</html>

```

### npm에 build script 추가하기
package.json 파일을 열어 다음의 코드를 추가한다. 

```

"scripts": {
	"test": "echo \"Error: no test specified\" && exit 1",
	"build": "webpack"
},

```
위의 코드를 추가한 후, 다음의 명령을 실행한다. 

```
npm run build
```

### webpack dev server
#### 설치

```
npm install --save-dev webpack-dev-server
```


### webpack command 살펴보기

```
node_modules/.bin/webpack --help
```

```
Config options:
  --config  Path to the config file
                         [string] [default: webpack.config.js or webpackfile.js]
  --env     Enviroment passed to the config, when it is a function

Basic options:
  --context    The root directory for resolving entry point and stats
                                       [string] [default: The current directory]
  --entry      The entry point                                          [string]
  --watch, -w  Watch the filesystem for changes                        [boolean]
  --debug      Switch loaders to debug mode                            [boolean]
  --devtool    Enable devtool for better debugging experience (Example:
               --devtool eval-cheap-module-source-map)                  [string]
  -d           shortcut for --debug --devtool eval-cheap-module-source-map
               --output-pathinfo                                       [boolean]
  -p           shortcut for --optimize-minimize --define
               process.env.NODE_ENV="production"                       [boolean]
  --progress   Print compilation progress in percentage                [boolean]

Module options:
  --module-bind       Bind an extension to a loader                     [string]
  --module-bind-post                                                    [string]
  --module-bind-pre                                                     [string]

Output options:
  --output-path                 The output path for compilation assets
                                       [string] [default: The current directory]
  --output-filename             The output filename of the bundle
                                                   [string] [default: [name].js]
  --output-chunk-filename       The output filename for additional chunks
       [string] [default: filename with [id] instead of [name] or [id] prefixed]
  --output-source-map-filename  The output filename for the SourceMap   [string]
  --output-public-path          The public path for the assets          [string]
  --output-jsonp-function       The name of the jsonp function used for chunk
                                loading                                 [string]
  --output-pathinfo             Include a comment with the request for every
                                dependency (require, import, etc.)     [boolean]
  --output-library              Expose the exports of the entry point as library
                                                                        [string]
  --output-library-target       The type for exposing the exports of the entry
                                point as library                        [string]

Advanced options:
  --records-input-path       Path to the records file (reading)         [string]
  --records-output-path      Path to the records file (writing)         [string]
  --records-path             Path to the records file                   [string]
  --define                   Define any free var in the bundle          [string]
  --target                   The targeted execution enviroment          [string]
  --cache                    Enable in memory caching
                      [boolean] [default: It's enabled by default when watching]
  --watch-stdin, --stdin     Exit the process when stdin is closed     [boolean]
  --watch-aggregate-timeout  Timeout for gathering changes while watching
  --watch-poll               The polling interval for watching (also enable
                             polling)                                  [boolean]
  --hot                      Enables Hot Module Replacement            [boolean]
  --prefetch                 Prefetch this request (Example: --prefetch
                             ./file.js)                                 [string]
  --provide                  Provide these modules as free vars in all modules
                             (Example: --provide jQuery=jquery)         [string]
  --labeled-modules          Enables labeled modules                   [boolean]
  --plugin                   Load this plugin                           [string]
  --bail                     Abort the compilation on first error      [boolean]
  --profile                  Profile the compilation and include information in
                             stats                                     [boolean]

Resolving options:
  --resolve-alias         Setup a module alias for resolving (Example:
                          jquery-plugin=jquery.plugin)                  [string]
  --resolve-extensions    Setup extensions that should be used to resolve
                          modules (Example: --resolve-extensions .es6 .js)
                                                                         [array]
  --resolve-loader-alias  Setup a loader alias for resolving            [string]

Optimizing options:
  --optimize-max-chunks      Try to keep the chunk count below a limit
  --optimize-min-chunk-size  Try to keep the chunk size above a limit
  --optimize-minimize        Minimize javascript and switches loaders to
                             minimizing                                [boolean]

Stats options:
  --color, --colors           Enables/Disables colors on the console
                                           [boolean] [default: (supports-color)]
  --sort-modules-by           Sorts the modules list by property in module
                                                                        [string]
  --sort-chunks-by            Sorts the chunks list by property in chunk[string]
  --sort-assets-by            Sorts the assets list by property in asset[string]
  --hide-modules              Hides info about modules                 [boolean]
  --display-exclude           Exclude modules in the output             [string]
  --display-modules           Display even excluded modules in the output
                                                                       [boolean]
  --display-max-modules       Sets the maximum number of visible modules in
                              output                                    [number]
  --display-chunks            Display chunks in the output             [boolean]
  --display-entrypoints       Display entry points in the output       [boolean]
  --display-origins           Display origins of chunks in the output  [boolean]
  --display-cached            Display also cached modules in the output[boolean]
  --display-cached-assets     Display also cached assets in the output [boolean]
  --display-reasons           Display reasons about module inclusion in the
                              output                                   [boolean]
  --display-depth             Display distance from entry point for each module
                                                                       [boolean]
  --display-used-exports      Display information about used exports in modules
                              (Tree Shaking)                           [boolean]
  --display-provided-exports  Display information about exports provided from
                              modules                                  [boolean]
  --display-error-details     Display details about errors             [boolean]
  --verbose                   Show more details                        [boolean]

Options:
  --help, -h     Show help                                             [boolean]
  --version, -v  Show version number                                   [boolean]
  --json, -j     Prints the result as JSON.                            [boolean]
```