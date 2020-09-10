# How to use the demo for more than one template (multipage app / MPA)?

**Author: [@ihelmer07](https://github.com/ihelmer07)**.

Everything is extremely easy to set up for more than one template to be exported with Vue embedded! Only few small tweaks are needed:

1. Set up the Vue project to be an MPA.

2. Create a `pages` folder and with several subfolders named after the pages you want, e.g. `About` and `Index`.

3. Copy `App.vue` and `main.js` into those folders. Match the following:

```
client
|--src
   |--pages
      |--about
      |   |--App.vue
      |   |--main.js
      |--index
          |--App.vue
          |--main.js
```

4. Change `vue.config.js` with the following changes: add `pages` object, remove `indexPath` and update `writeToDisk` function.

```javascript
module.exports = {
  publicPath:
    process.env.NODE_ENV === "production"
      ? "/static/dist/"
      : "http://127.0.0.1:8080",
      // Added pages per Vue Docs
  pages: {
    index: {
      entry: "./src/pages/home/main.js", // matching the new paths created above
      template: "public/index.html",
      filename: "../../templates/base-vue.html", // relative to outputDir!
      chunks: ["chunk-vendors", "index"],
    },
    about: {
      entry: "./src/pages/about/main.js", // matching the new paths created above
      template: "public/index.html",
      filename: "../../templates/base-vue-about.html", // relative to outputDir!
      chunks: ["chunk-vendors", "about"],
    },
  },

  outputDir: "../server/static/dist",
  chainWebpack: (config) => {
    /*
        The arrow function in writeToDisk(...) tells the dev server to write
        only index.html to the disk.
        The indexPath option (see above) instructs Webpack to also rename
        index.html to base-vue.html and save it to Django templates folder.
        We don't need other assets on the disk (CSS, JS...) - the dev server
        can serve them from memory.
        See also:
        https://cli.vuejs.org/config/#indexpath
        https://webpack.js.org/configuration/dev-server/#devserverwritetodisk-
        */
    config.devServer
      .public("http://127.0.0.1:8080")
      .hotOnly(true)
      .headers({ "Access-Control-Allow-Origin": "*" })
      .writeToDisk((filePath) => filePath.endsWith(".html")); //NOTE THIS CHANGE HERE
  },
};
```
