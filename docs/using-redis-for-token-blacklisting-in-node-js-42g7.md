# 保持无状态——使用 Redis 将令牌列入节点 JS 的黑名单

> 原文：<https://dev.to/mr_cea/using-redis-for-token-blacklisting-in-node-js-42g7>

JSON web 令牌是无状态的。这意味着服务器不维护用户的状态。数据库或会话中没有保存关于谁发送特定请求的信息。JWT 提供了一个令牌来处理这种身份验证

不维护状态的一个主要缺点是令牌在设置的到期日期之前都是活动的。因此，即使用户已在前端注销并且本地存储被清除，任何有权访问令牌的人都可以访问该用户的已验证路由，直到令牌过期。

针对这一缺点，已经提供了许多解决方案:

*   将 JWT 过期持续时间缩短至非常短的时间，并使用过期日期为 2 周或更长时间的刷新令牌。当 JWT 过期时，刷新令牌用于在用户仍然登录的情况下为用户生成新的 JWT。
    这样做的一个主要缺点是，管理员可以随时撤销刷新令牌，如果在 JWT 过期时发生这种情况，用户必须再次登录，因为您既有已撤销的令牌，又有过期的 JWT。

*   第二种方法是在注销时将列入黑名单的令牌保存在用户表的一列中，并使用它进行验证，在之前的令牌过期时销毁它。

    *   这是不可伸缩的。
    *   这违背了 JWT 作为一个国家存在的目的。
*   第三种方法是通过改变 JWT 密钥来使所有用户的令牌无效。这可能会激怒所有用户，因为他们都必须重新登录。

*   第四种方法是在 Redis 中注销时将令牌列入黑名单

### 什么是惯用语

Redis 是内存中的数据结构存储，用作数据库、缓存或消息代理。你可以使用数据结构，比如字符串、散列、列表、集合、排序集合等等

#### 为什么用内存代替数据库

内存依赖于主内存来存储计算机数据，并且比使用磁盘管理系统的数据库管理系统更快，因为内部优化算法更简单，执行的 CPU 指令更少。在内存中获取数据消除了查询数据时的寻道时间，这提供了比磁盘更快、更可预测的性能。与使用 DBMS 相比，这更具可扩展性。

### 使用 Redis 验证节点中的令牌

*   安装 npm redis 程序包〔t0〕

    ```
    npm install redis 
    ```

*   导入 Redis 并使用 rediscclient

    ```
    import redis from redis  // ES6 +
    import { exec} from 'child_process';// to start the redis database in development 
    /*// for windows user import {execFile} from 'child_process';        
    // for ES5 users
    const redis = require('redis')*/
    // if in development mode use Redis file attached
    // start redis as a child process
    if (process.env.NODE_ENV === 'development') {
    const puts = (error, stdout) =>{
    console.log(error)
    console.log(stdout)
    }
    exec('redis/src/redis-server redis/redis.conf', puts);  
    }
    /* for window implementation 
    execFile('redis/redis-server.exe',(error,stdout)=>{
    if(error){
    throw error
    }
    console.log(stdout)
    })
    */
    export const redisClient = redis.createClient(process.env.REDIS_URL);
    // process.env.REDIS_URL is the redis url config variable name on heroku. 
    // if local use redis.createClient()
    redisClient.on('connect',()=>{
    console.log('Redis client connected')
    });
    redisClient.on('error', (error)=>{
    console.log('Redis not connected', error)
    }); 
    ```

*   使用 Redis 验证令牌。

    将您的 Redis 操作作为身份验证的一部分进行传递

    ```
    import jwt from 'jsonwebtoken';
    import pool from '../path/to/file';
    import { redisClient } from '../path/to/file';
    import 'dotenv';
    const auth = {
    // eslint-disable-next-line consistent-return
    async checkToken(req, res, next) {
    const token = req.headers.authorization.split('  ')[1]|| req.params.token;
    // check if token exists
    if (!token) {
      return res.status(401).send({
        status: 401,
        error: 'You need to Login',
      });
    }
    /* .......redis validation .........*/
    try {

        const result = await redisClient.lrange('token',0,99999999)
        if(result.indexOf(token) > -1){
          return res.status(400).json({
            status: 400,
            error: 'Invalid Token'
        })
      }
       /*
         const invalid = (callback) => {
         redisClient.lrange('token', 0, 999999999, (err, result) => 
         callback(result));
      };
        invalid((result) => {
      // check if token has been blacklisted
         if (result.indexOf(token) > -1){
           return res.status(400).json({
            status: 400,
            error: 'Invalid Token',
         });
       }
    })
    */
    /* ...... ............................*/
     const decrypt = await jwt.verify(token, process.env.SECRET);
      const getUser = 'SELECT * FROM users WHERE id= $1';
      const { rows } = await pool.query(getUser, [decrypt.id]);
      // check if token has expired
      if (!rows[0]) {
        return res.status(403).json({
          status: 403,
          error: ' Token Not accessible',
        });
      }

      next();
    } catch (error) {
      return res.status(501).json({
        status: 501,
        error: error.toString(),
      });
    }
    },
    };
    export default auth; 
    ```

#### 解释:

Redis lrange 函数返回数组中的标记列表。这些令牌是已经列入黑名单令牌。如果使用的令牌已经被列入黑名单，indexOf 方法将返回大于等于 0 的值和 400 响应。

### 使用 Redis 将节点列入黑名单

使用 Redis 将令牌列入黑名单

*   在后端创建注销路由:

通常，所需要的只是在注销时从本地存储器
中清除令牌，但是将令牌列入黑名单需要在注销时调用一个端点
。

```
static async logout(req, res) {
    // logout user
    // save token in redis
    const token = req.headers.authorization.split('  ')[1];
    try {
        await redisClient.LPUSH('token', token);
        return res.status(200).json({
          'status': 200,
          'data': 'You are logged out',
        });
    } catch (error) {
      return res.status(400).json({
        'status': 500,
        'error': error.toString(),
      });
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

#### 解释:

Redis LPUSH 方法类似于数组 PUSH 方法。所以基本上你把令牌添加到一个名为‘token’的数组中。单击 logout 按钮时，注销的端点被调用，令牌被列入黑名单，然后本地存储可以被清除。

#### 结论:

Redis 是一个有价值的工具。关于 Redis 的更多用法，请阅读它的
[文档](https://redis.io/documentation)，尤其是[缓存](https://redis.io/topics/lru-cache)。

在这个 [Redis](https://github.com/basilcea/askLite) 文件夹中有一个用于 Windows 的 Redis 实现。
请随时通过 [ob_cea](https://twitter.com/OB_CEA) 或下面的帖子联系我。