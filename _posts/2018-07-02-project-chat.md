---
layout:     post
title:      DEMO
summary: demo 使用 vue / vuex / mysql / node
categories: Vue
technique: true
---



### 启动mysql

1. 以管理员身份运行cmd

2. 启动服务命令：net start mysql 

> [zip压缩包方式安装mysql（Windows）](https://selenamona.github.io/2018/07/02/project-chat/)


### server 

在项目根文件夹下创建一个server文件夹。然后里面创建以下文件：

- **db.js---用来添加mysql配置**   

    ```javascript
    // 数据库连接配置
    module.exports = {
        mysql: {
            host: 'localhost',
            user: 'root',
            password: '',
            database: 'users',
            port: '3306'
        }
    }
    ```

- **index.js---Express服务器入口文件**  

    ```javascript
    // node   后端服务器

    const userApi = require('./api/useApi');
    const fs = require('fs');
    const path = require('path');
    const bodyParser = require('body-parser');
    const express = require('express');
    const app = express();

    app.use(bodyParser.json());
    app.use(bodyParser.urlencoded({extended: false}));

    // 后端api路由
    app.use('/api/user', userApi);

    // 监听端口
    app.listen(3000);
    console.log('success listen at port:3000......');
    ```

- **sqlMap.js---SQL语句映射文件，以供api逻辑调用**  

    ```javascript
    // sql语句
    var sqlMap = {
        // 用户
        user: {
            add: 'insert into user(id, username, password) values (0, ?, ?)',
            search: 'select password from user where username=?'
        }
    }

    module.exports = sqlMap;
    ```
- **api/userApi.js ---  api示例** 

    ```javascript
    var models = require('../db');
    var express = require('express');
    var router = express.Router();
    var mysql = require('mysql');
    var $sql = require('../sqlMap');

    // 连接数据库
    var conn = mysql.createConnection(models.mysql);

    conn.connect();
    var jsonWrite = function(res, ret) {
        if(typeof ret === 'undefined') {
            res.json({
                code: '1',
                msg: '操作失败'
            });
        } else {
            res.json(ret);
        }
    };

    // 增加用户接口
    router.post('/addUser', (req, res) => {
        var sql = $sql.user.add;
        var params = req.body;
        console.log(params);
        conn.query(sql, [params.username, params.password], function(err, result) {
            if (err) {
                console.log(err);
            }
            if (result) {
                jsonWrite(res, result);
            }
        })
    });

    // 查询用户接口
    router.post('/searchUser', (req, res) => {
        var sql = $sql.user.search;
        var params = req.body;
        console.log(params);
        conn.query(sql, [params.username], function(err, result) {
            if (err) {
                console.log(err);
            }
            if (result) {
                jsonWrite(res, result);
            }
        })
    });

    module.exports = router;
    ```
- **server.js ---   server**

    ```javascript
    var http = require('http');

    var mysql = require('mysql');
    var TEST_DATABASE = 'test';
    var TEST_TABLE = 'test';

    var connection = mysql.createConnection({
        host : 'localhost',
        port : 3306,
        user : 'root',
        password : '',
    });

    
    // 创建数据库
 
    // connection.query('CREATE DATABASE '+TEST_DATABASE, function(err) {
    //     if (err && err.number != mysql.ERROR_DB_CREATE_EXISTS) {
    //         throw err;
    //     }
    // });
 
 
    // 不指定回调函数，如果出错，则体现为客户端错误
    // connection.query('USE '+TEST_DATABASE);
 
    // 创建表格,插入数据
    // connection.query(
    // 'CREATE TABLE '+TEST_TABLE+
    // '(id INT(11) AUTO_INCREMENT, '+
    // 'name VARCHAR(255), '+
    // 'PRIMARY KEY (id))'
    // );

    // connection.query(
    // 'INSERT INTO '+TEST_TABLE+' '+
    // 'SET name = ?',
    // ['nodejs1']
    // );

    // var query = connection.query(
    // 'INSERT INTO '+TEST_TABLE+' '+
    // 'SET name = ?',
    // ['nodejs2']
    // );
    
    // 查询，并设置回调函数
    // connection.query(
    // 'SELECT * FROM '+TEST_TABLE,
    // function selectCb(err, results, fields) {
    //     if (err) {
    //     throw err;
    //     }
    //     console.log(results);
    //     console.log(fields);
    //     connection.end();
    // }
    // );

    http.createServer(function (request, response) {
        response.writeHead(200, {'Content-Type': 'text/html'});
        response.end('<b>Hello World</b>');
    }).listen(8888);

    console.log('Server running at http://127.0.0.1:8888/');
    ```


编写完成之后，就可以在项目根目录下安装依赖`npm install express mysql body-parser`;  

此时在server文件夹下执行`node index`（这里也可以加载package.json中，然后使用npm执行）看到success listen at port:3000......即服务端启动成功。  

### 设置代理

```JAVASCRIPT
// onfig文件proxyTable参数
dev: {
    // ...
    proxyTable: {
        '/api': {
            target: 'http://127.0.0.1:3000/api/',
            changeOrigin: true,
            pathRewrite: {
                '^/api': ''
            }
        }
    },
    // ...
}
```


参考：

[Vue+MySQL+Express小试牛刀](https://segmentfault.com/a/1190000008176208)