---
title: 使用nodejs简单搭建一个图床
category: js
tags:
  - study
  - js
  - nodejs
keywords: 图床，eggjs，
aimg: /medias/article/2.webp
abbrlink: 63677
date: 2022-06-05 21:59:10
---

# nodejs 简单搭建一个图床

## 01 搭建项目

> 这里使用 egg 来搭建项目

```shell
$ mkdir figure_bed
$ cd figure_bed
$ npm init egg --type=simple
// 这里直接回车选择simple模板
$ cd app
// app为你命名的项目名
$ yarn
$ yarn dev
// 现在打开http://localhost:7001可以看到hello egg!

$ yarn add egg-cors egg-mysql pump nanoid dayjs --save
// 安装依赖
// egg-cors配置跨域
// egg-mysql帮助操作mysql
// pump流式操作文件
```

## 02 修改一下配置

```js
// app/config/config.default.js

"use strict";

module.exports = (appInfo) => {
  const config = (exports = {});
  config.mysql = {
    client: {
      host: "127.0.0.1",
      port: "3306",
      user: "", // mysql用户名
      password: "", // mysql密码
      database: "", // 表名字
    },
    app: true,
    agent: false,
  };

  config.keys = appInfo.name + "_1653801870741_2625";

  config.middleware = [];

  const userConfig = {};

  config.proxy = true;
  config.security = {
    csrf: { enable: false },
    domainWhiteList: ["*"],
  };
  config.cors = {
    origin: "*", // 全部开放
    allowMethods: "GET,HEAD,PUT,POST,DELETE,PATCH,OPTIONS",
  };
  config.multipart = {
    whitelist: [
      ".jpg",
      ".jpeg",
      ".png",
      ".gif",
      ".bmp",
      ".wbmp",
      ".webp",
      ".tif",
      ".psd",
    ], // 文件白名单
    mode: "stream", // 流式读取
  };
  config.uploadDir = "app/public/image";
  config.pathMap = "app/public/data";
  config.static = {
    prefix: "/cdn/",
  };
  // 静态资源前缀修改,不修改可以通过/public/path/to/your/file访问静态资源，修改之后变为/cdn/path/to/your/file访问

  return {
    ...config,
    ...userConfig,
  };
};
```

```js
// app/config/plugin.js
// 开启插件
"use strict";

module.exports = {
  cors: {
    enable: true,
    package: "egg-cors",
  },
  mysql: {
    enable: true,
    package: "egg-mysql",
  },
};
```

## 03 编写接口逻辑

```js
// 配置controller 新建picture.js  /app/controller/picture.js

"use strict";

const Controller = require("egg").Controller;
const fs = require("fs");
const pump = require("pump");
const { nanoid } = require("nanoid");

class UploadController extends Controller {
  async upload() {
    const { ctx, app } = this;
    const isProd = app.config.env === "prod";
    const parts = ctx.multipart({ autoFields: true });
    let part = [],
      stream;
    // 流式写入
    while ((stream = await parts()) != null) {
      let dir = await this.service.tools.getUploadFile(stream.filename);
      let target = dir.uploadDir;
      let writeStream = fs.createWriteStream(target);
      await pump(stream, writeStream);
      let saveDir;
      if (isProd) {
        saveDir = "你部署的位置" + dir.saveDir.replace("/public/", "/cdn/");
      } else {
        saveDir =
          "http://127.0.0.1:7001" + dir.saveDir.replace("/public/", "/cdn/");
      }
      let id = nanoid();
      // 将写入图片的位置写入数据库中
      await this.service.sql.pictureMap(id, saveDir);
      part.push({ path: saveDir, id });
    }

    ctx.body = {
      code: 200,
      data: part,
      status: "success",
    };
  }

  async deletePic() {
    const { ctx } = this;
    const _id = ctx.request.body.id;
    // 根据id标识来找到图片并删除
    const picmap = await this.service.sql.getPictureMap();
    if (picmap.length) {
      for (let i = 0; i < picmap.length; i++) {
        const { id, path } = picmap[i];
        if (id === _id) {
          await this.service.tools.removeImage(path);
          await this.service.sql.deleteMapPath(_id);
        }
      }
    }
    ctx.body = {
      code: 200,
      data: null,
      status: "success",
    };
  }

  // 查看数据库中保存的所有的url
  async getMapData() {
    const { service } = this;
    const res = await service.sql.getPictureMap();
    ctx.body = {
      code: 200,
      data: res,
      status: "success",
    };
  }
}

module.exports = UploadController;
```

## 04 编写接口逻辑需要用到的方法和操作数据库的逻辑

```js
// 在service里写操作数据库的方法(官方推荐的) /app/service/sql.js
"use strict";
const Service = require("egg").Service;
const dayjs = require("dayjs");

class ToolService extends Service {
  async deleteMapPath(id) {
    const { app } = this;
    const { mysql } = app;
    // picturemap为你储存的表名
    const result = await mysql.delete("picturemap", {
      id,
    });
    return result;
  }

  async pictureMap(id, url) {
    const { app } = this;
    const { mysql } = app;
    const result = await mysql.insert("picturemap", {
      id,
      url,
      createAt: dayjs().unix(),
      deleteAt: null,
    });
    return result;
  }
  async getPictureMap() {
    const { mysql } = this.app;
    const res = await mysql.get("picturemap");
    return res;
  }
}

module.exports = ToolService;
```

```js
// /app/service/tool.js
"use strict";
const path = require("path");
const Service = require("egg").Service;
const Sd = require("silly-datetime");
const Mkdirp = require("mz-modules/mkdirp");
const fs = require("fs");
const { promisify } = require("util");

const removeFile = promisify(fs.unlink);
const readDir = promisify(fs.readdir);

class ToolService extends Service {
  async getTime() {
    let now = new Date();
    return now.getTime();
  }

  // 删除public文件夹下保存的图片
  async removeImage($path) {
    let _path = path.resolve(__dirname, "../public" + $path.split("/cdn")[1]);
    let dateDir = path.resolve(
      __dirname,
      "../public/image/" + $path.split("/image/")[1].split("/")[0]
    );
    await removeFile(_path);
    const file = await readDir(dateDir);
    !file.length && fs.rmdir(dateDir, function (e) {});
  }

  async getUploadFile(filename) {
    let now = Sd.format(new Date(), "YYYYMMDD");
    let dir = path.join(this.config.uploadDir, now);
    await Mkdirp(dir);
    let timestamp = await this.getTime();
    let uploadDir = path.join(dir, timestamp + path.extname(filename));
    return {
      uploadDir: uploadDir,
      saveDir: uploadDir.slice(3).replace(/\\/g, "/"),
    };
  }
}

module.exports = ToolService;
```

## 05 配置接口映射

> 配置一下 router 路由映射,router 就是配置接口的请求方式以及调用哪个接口的地方

```js
// /app/router.js
"use strict";

module.exports = (app) => {
  const { router, controller } = app;
  router.post("/upload", controller.picture.upload);
  router.get("/imgDataSouce", controller.picture.getMapData);
  router.post("/removeImg", controller.picture.deletePic);
};
```

## 06 简单测试一下

```js
const baseUrl = "http://localhost:7001";
fetch(baseUrl + "/imgDataSource")
  .then((res) => res.json())
  .then((res) => console.log(res));

// 这里的files为blob数组
let form = new FormData();
files.forEach((file) => form.append("file", file));
fetch(baseUrl + "/upload", {
  method: "POST",
  body: file,
})
  .then((res) => res.json())
  .then((res) => console.log(res));

// 这里id为前端调上传图片时接口返回的id
fetch(baseUrl + "/removeImg", {
  method: "POST",
  body: { id },
})
  .then((res) => res.json())
  .then((res) => console.log(res));
```
