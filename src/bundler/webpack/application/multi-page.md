# 多页应用通用模板

## 动态处理 entry

- 统一入口文件目录结构（如 `src/page1/index.js, src/page2/index.js`）
- glob 动态加载文件

```js
const path = require('path');
const glob = require('glob');
const getEntry = () => {
    const entry = {};
    const htmlWebpackPlugins = [];
    
    const entryFiles = glob.sync('./src/*/index.js');
    
    entryFiles.map((entryFile) => {
        const pageName = entryFile.match(/src\/(.*)\/index\.js$/)[1];
        
        entry[pageName] = entryFile;

        htmlWebpackPlugins.push({
          template: path.join(__dirname, `./src/${pageName}/index.html`),
          filename: `${pageName}.html`,
          hash: true,
          chunks: [pageName],
          minify: {
            html5: true,
            collapseWhitespace: true,
            removeComments: true,
            preserveLineBreaks: false,
            minifyCSS: true,
            minifyJS: true
          }
        })
    })
}

getEntry();
```

