## webpack学习笔记

```javascript
const gulp = require('gulp');
const webpack = require("webpack");
var config = require('./webpack.config.js');
var WebpackDevServer = require("webpack-dev-server");
const baseDir = __dirname;

var compiler = webpack(config);

gulp.task('dev',function(){
    //这个地方一定要加new
    //要不然会出现 this.sockwrite is not a function
    var server =new WebpackDevServer(compiler, {
        contentBase: __dirname+'/build',
        hot: false,
        inline: true,
        compress: true,
      	historyApiFallback: true,//在开发单页应用时非常有用，它依赖于HTML5 history API，如果设置为true，所有的跳转将指向index.html
        stats: {
            chunks: false,
            children: false,
            colors: true
        }
    });

    server.listen(8080, "localhost", function() {});
});
//webpack.config.js
const config = {
    entry:'./src/app.js',
    output:{
        filename:"bundle.js",
        path:__dirname+"/build"
    },
    module:{
        loaders:[
            {
                test: /\.js$/,
                exclude: /(node_modules|bower_components)/,
                loader: 'babel-loader',
                query: {
                    presets: ['es2015','react']
                }
            }
        ]
    }
};
module.exports = config;
```

##如何编写一个插件
```json
{
    test: /\.*$/,
    exclude: /node_modules/,//屏蔽不需要处理的文件（文件夹）（可选）
    loader: 'test-loader'//loader的名称（必须）
}
```

在node_modules创建test-loader 文件夹，输入index.js

test-loader/index.js
```javascript
module.exports=function(content){
    content = content.replace('aaa','bbb');
    return content;
}
```