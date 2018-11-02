## 自动化构建工具对比



### [grunt](https://www.gruntjs.net/)

**`配置文件GruntFile.js`**

```javascript
module.exports = function(grunt) {

  // Project configuration.
  grunt.initConfig({
    pkg: grunt.file.readJSON('package.json'),
    uglify: {
      options: {
        banner: '/*! <%= pkg.name %> <%= grunt.template.today("yyyy-mm-dd") %> */\n'
      },
      build: {
        src: 'src/<%= pkg.name %>.js',
        dest: 'build/<%= pkg.name %>.min.js'
      }
    }
  });

  // 加载包含 "uglify" 任务的插件。
  grunt.loadNpmTasks('grunt-contrib-uglify');

  // 默认被执行的任务列表。
  grunt.registerTask('default', ['uglify']);

};
```



**Grunt的优点**：

* 灵活，它只负责执我们定义的任务

* 大量可复用插件封装好了我们常见的构建任务

**缺点:**

* 集成度不高，要写很多配置后才可使用，无法做到开箱即用



### [Gulp](https://www.gulpjs.com.cn/)

**`配置文件Gulpfile.js`**

```javascript
var gulp = require('gulp'),
    sass = require('gulp-sass'),
    concat = require('gulp-concat'),
    uglify = require('gulp-uglify');

// 编译less文件
gulp.task('sass', function() {
  // 读取文件，通过管道喂给插件
   gulp.src('./scss/*.scss')
  // scss插件将scss文件编译成css文件
  .pipe(sass())
   // 输出文件
   .pipe(gulp.dest('./css')) 
});
// 合并压缩javascript文件
gulp.task('scripts', function(){
    gulp.src('./js/*.js')
    .pipe(concat('all.js'))
    .pipe(uglify())
    .pipe(gulp.dest('./dist'))
});
// 监听文件的变化
gulp.task('watch', function(){
    // 当检测到文件变化时
    gulp.watch('./scss/*.scss', ['sass']);
    gulp.watch('./js/*.js', ['scripts']);
})
```

**Gulp的使用**：

- 通过gulp.task注册一个任务

- 通过gulp.run执行任务

- 通过gulp.watch监听文件的变化

- 通过gulp.src读取文件

- 通过gulp.dest写文件


**Gulp优点:**

* 灵活好用，可以单独完成构建，也可以和其他工具搭配使用

**Gulp优点:**

- 集成度不高，要写很多配置后才可使用，无法做到开箱即用



### [Fis3](http://fis.baidu.com/fis3/index.html)

**`配置文件：fis-conf.js`**

```
// 清除其他配置，只保留如下配置
fis.match('*.js', {
  // fis-optimizer-uglify-js 插件进行压缩，已内置
  optimizer: fis.plugin('uglify-js')
});

fis.match('test.js', {
  useHash: true,
  release: '/dist/js/$0'
});

fis.match('*.css', {
  // fis-optimizer-clean-css 插件进行压缩，已内置
  optimizer: fis.plugin('clean-css')
});

fis.match('*.png', {
  // fis-optimizer-png-compressor 插件进行压缩，已内置
  optimizer: fis.plugin('png-compressor')
});
```



**常用功能：**

* 读写文件: 
* 资源定位
* 文件指纹
* 文件编译
* 压缩资源
* 图片合并



**优点:**

* 功能集成
* 配置简单，开箱即用

* 专注于web开发的完整解决方案，功能完善

**缺点:**

* 官方不在更新维护，不支持最新版本的Node.js



### [webpack](https://webpack.docschina.org/)

**`配置文件：webpack.config.js`**



**优点:**

* 专注于处理模块化的项目，能做到开箱即用，一步到位
* 可通过Plugin扩展，完整好用又不失灵活
* 使用场景不限于web开发
* 社区活跃，紧跟潮流
* 良好的开发体验

**优点:**

* 只能用于采用模块化开发的项目



### [Rollup](https://www.rollupjs.com/guide/zh)

**`配置文件：rollup.config.js`**

```javascript
// rollup.config.js
export default {
  input: 'src/main.js',
  output: {
    file: 'bundle.js',
    format: 'cjs'
  }
};
```

定配置文件

```
rollup --config rollup.config.dev.js
rollup --config rollup.config.prod.js
```



**rollup与webpack比较：**

* Rollup专注于ES6模块打包

* Rollup在webapck流行后出现的替代品

* Rollup的生态链不完善，体验不如webpack

* Rollup的功能不如webpack完善，但其配置和使用更加简单

* Rollup不支持code Spliting, 但好处是打包出来的代码没有webapck那段模块加载、执行和缓存的代码








