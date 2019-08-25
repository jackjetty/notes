* 部署到服务端后，通过**node app.js**来启动webserver
* 可以自定义虚拟接口地址
    * 根目录的**app.js（入口文件？）**中，可以自定义接口（相当于node.js写的接口，跑在服务端）

```
app.get('/mdsp-osbar', (req, res) => {
  console.log('mdsp_osbar_url:', process.env.mdsp_osbar_url);
  if (process.env.mdsp_osbar_url) {
    res.redirect(process.env.mdsp_osbar_url);
  } else {
    res.redirect("https://static.eu1-b.mindsphere.io/osbar/v4/js/main.min.js");
  }
});
```