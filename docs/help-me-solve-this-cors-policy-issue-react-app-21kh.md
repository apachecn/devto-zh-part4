# 帮我解决这个 CORS 政策问题- React 应用

> 原文：<https://dev.to/mesubhokarma/help-me-solve-this-cors-policy-issue-react-app-21kh>

我正在尝试从服务器中删除 id 为的用户，在 react 应用程序上使用 axios http 客户端访问 api，并在开发人员/控制台中收到此消息-->

> 从源“localhost:3000”访问位于“host:8082/user/delete/6”的 XMLHttpRequest 已被 CORS 策略阻止:对预检请求的响应未通过访问控制检查:请求的资源上不存在“Access-Control-Allow-Origin”标头。

这是我的代码-

从“React”导入 React
从“axios”导入 axios

```
export default class PersonList extends React.Component {
  state = {
    id: "",
  }

  handleChange = event => {
    this.setState({ id: event.target.value })
  }

  handleSubmit = event => {
    event.preventDefault()

    axios
      .delete(`http://host:8082/user/delete/${this.state.id}`)
      .then(res => {
        console.log(res)
        console.log(res.data)
      })
  }

  render() {
    return (
      <div>
        <form onSubmit={this.handleSubmit} crossOrigin="anonymous">
          <label>
            User ID:
            <input type="text" name="id" onChange={this.handleChange} />
          </label>
          <button type="submit">Delete</button>
        </form>
      </div>
    )
  }
} 
```

请帮我解决这个 cors 问题。