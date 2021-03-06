# Webstorm 使用Js+cucumber做ATDD
## 新建工程
  新建一个Nodejs工程
  ![](images/create_project.png)
## 必用类库
- cucumber 提供Given/When/Then的测试DSL和解析
- chai 提供断言功能

## 常用类库
- axios 网络访问
- fs 本地文件读取
- form-data 用来构造上传文件的表单
- cpress 提供一个无界面的浏览器引擎用来测试Web
- cypress-cucumber-preprocessor cypress支持cucumber的库
## 增加test指令
在package.json中，修改script中的test为："node ./node_modules/.bin/cucumber-js"
## 编写测试
- 根目录新建 features目录
- 在features目录下新建一个.feature文件，如test.feature
- feature文件内容
```
  Feature: 测试特征描述
  Scenario: 场景描述
    Given 当XXXX
    When 进行XXXX
    Then XXXX
```
  光标停留在Given/When/Then那一行上，点击提示的warning，可以选择'create step defintion'，选中后可以生成一个js文件，如下
  ![](images/create_step.png)
```
  Then(/^使用管理员登录成功$/, async function () {
    let resp = await axios.post(getUrl('/auth/login'), {
        loginName: "admin",
        password: "123456"
    });
    expect(resp.data.code).to.equal(0);
 });
```
 之后就可以运行 npm run test运行所有测试
## 示例
```
Feature: 初始化管理员

  Scenario: 系统初期初始化管理员
    Given 导入数据 "empty_user.xls"
    When 初始化管理员
    Then 使用管理员登录成功

  Scenario: 系统初期初始化管理员后不能再初始化一个
    Given 导入数据 "user_with_admin.xls"
    When 初始化管理员
    Then 使用管理员登录失败1
```
## 上传文件用法
```
const axios = require('axios');
const FormData = require('form-data');
const fs = require('fs');
.....
//将位于test_data目录下的xls文件通过接口导入到系统
async function importFile(file) {
    const formData = new FormData();
    formData.append("file", fs.createReadStream('./test_data/' + file));
    await axios.post(getUrl('/dbtool/import'), formData, {
        headers: formData.getHeaders()
    })
}
```
# 作业
我有一个服务，接口地址是：http://192.168.104.104:8080/ ，有如下接口需要测试：初始化管理员
- 导入文件接口
提供一个空的excel文件，用来将数据库清空，只要导入这个文件，数据库将恢复到清空状态，见[here](../files/empty_user.xls)
```
  post /dbtool/import
      file 文件内容
```  
- 初始化管理员接口
```
  post /auth/init_admin
       {
         loginName:'',
         password:''
       }
```
- 登录接口
```
  post /auth/login
   {
     loginName:'',
     password:''
   }
```
- 测试场景1
  清空数据，初始化管理员，能登录成功
- 测试场景2
  清空数据，初始化管理员后再初始化另外管理员，第二次的管理员初始化失败，登录失败

