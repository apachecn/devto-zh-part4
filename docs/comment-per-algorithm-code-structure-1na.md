# 每个算法代码结构的注释

> 原文：<https://dev.to/kosenshou/comment-per-algorithm-code-structure-1na>

当编写或构建函数时

首先我写下函数的名字和变量。

```
public function email_login($email, $password) {

} 
```

然后我开始逐行描述函数的算法，每一步。

```
public function email_login($email, $password) {
    // check if email is existing
    // if existing verify password
    // if correct password return user information
    // if not show response invalid_password
    // if email is not existing show response email_not_found
} 
```

然后我一个一个的写算法，检查它是否在程序中工作，直到最后一步。就这样，我完成了全部功能的构建。

```
public function email_login($email, $password) {
    // check if email is existing
    $this->db->where('email', $email);
    $query = $this->db->get('users');
    $user = $query->row_array();

    // if existing verify password
    if ($user != null) {
        // if correct password return user information
        if ($user["password"] == $password) {
            return $user;
        // if not show response invalid_password
        } else {
            return "invalid_password";
        }
    // if not show response email_not_found
    } else {
        return "email_not_found";
    }
} 
```