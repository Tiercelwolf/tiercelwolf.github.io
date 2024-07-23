# redux example build实录
从https://github.com/reduxjs/redux.git下载redux，其中包含example，这里以buildexample中的todos项目为例，记录解决问题过程。

1. 更新npm的registry地址。从taobao更新到npmjs。原因是taobao中某个包的checksum不对。命令行为：<br> npm config set registry https://registry.npmjs.org
2. 为加速npm下载速度，设置proxy信息：<br> npm config set proxy=http://127.0.0.1:1089
3. 运行npm start时，报错：ERR_OSSL_EVP_UNSUPPORTED，设置环境变量解决：<br>export NODE\_OPTIONS=--openssl-legacy-provider
4. 运行npm start时，报错：Failed to load config "react-app" to extend from .eslintrc.cjs"，通过修改如下解决问题：<br> npm install --save-dev eslint-config-react-app <br> 修改.eslintrc.cjs文件 <br>
   ```
   module.exports = {
  extends: ['react-app'],
  // other configurations...
  };
  ```<br>
  在package.json增加：<br>
  ```
  {
  "eslintConfig": {
    "extends": [
      "react-app"
    ]
  }
  }
  ```<br>
  删除package.json中原有的包依赖的eslint信息。
  
  