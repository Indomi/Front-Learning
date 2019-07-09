## 如何减少Webpack的打包时间

### 1.优化loader

可以减少打包js的babel-loader的打包文件数来减少

优化Loader的文件搜索范围

```javascript
// eg
module.exports = {
  module: {
    rules: [
      {
        test: /\.js$/,
        loader: 'babel-loader?cacheDirectory=true', // 增加编译过文件的缓存cacheDirectory
        include: [
          resolve('src')
        ],
        exclude: /node_modules/ // 排除node_modules代码
      }
    ]
  }
}
```

优化前：

!(优化前)[img/1.png]

优化后：

!(优化后)[img/2.png]

### 2.HappyPack

受限于node的单线程特点，可以通过HappyPack将Loader的同步执行转换为并行的

```javascript
module: {
  loaders: [
    {
      test: /\.js$/,
      include: [resolve('src')],
      exclude: /node_modules/,
      // id 后面的内容对应下面
      loader: 'happypack/loader?id=happybabel'
    }
  ]
},
plugins: [
  new HappyPack({
    id: 'happybabel',
    loaders: ['babel-loader?cacheDirectory'],
    // 开启 4 个线程
    threads: 4
  })
]
```