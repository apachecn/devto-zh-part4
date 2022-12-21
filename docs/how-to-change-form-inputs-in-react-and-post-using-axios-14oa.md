# 如何使用 axios 更改 React 和 post 中的表单输入？

> 原文：<https://dev.to/narzantaria/how-to-change-form-inputs-in-react-and-post-using-axios-14oa>

你好。

我正在学习反应，但还不够强。我使用一个带有表单的组件，它使用 axios 从 express 后端接收数据。获得正确的数据并将其呈现在表单输入中没有问题，但是我不知道如何使用 axios 更改输入值和 post。我看了一些 handleChange()和其他工作人员的资料，但是还是太难了。

JSON 看起来像这样:

```
{
    "data": [
        {
            "_id": "5d28a6fcec97b111c2f5867d",
            "phone": "+1 (111) 111 11 11",
            "email": "shutruk@gmail.com",
            "title": "khkjhkjhkj",
            "longTitle": "lkjlkjlkjlk",
            "introTitle": "Shutruk",
            "introLongTitle": "Shutruk-Nahhunte",
            "videoLink": "khkjhkjhkj",
            "introText": "lkjlkjlkjlk",
            "__v": 0
        }
    ]
} 
```

Enter fullscreen mode Exit fullscreen mode

组件如下:

```
import React, { Component } from 'react';
import axios from 'axios';

class Misc extends Component {
  state = {
    data: [],
    loading: true,
    error: false,
  }
  componentDidMount() {
    axios.get('http://localhost:5555/data')
      .then(res => {
        const data = res.data.data; // get the data array instead of object
        this.setState({ data, loading: false });
        console.log(data);
      })
      .catch(err => { // log request error and prevent access to undefined state
        this.setState({ loading: false, error: true });
        console.error(err); 
      })
  }
  render() {
    if (this.state.loading) {
      return(
        <div>
          <p> Loading... </p>
        </div>
      )
    }
    if (this.state.error || !this.state.data[0]) { // if request failed or data is empty don't try to access it either
      return(
        <div>
          <p> An error occured </p>
        </div>
      )
    }
    return (
      <form action="">
        <h2 className="center" >Change data</h2>
        <div className="center"><img src={require('../img/orn.png')} alt="" className="orn"/></div>
        <h5>Phone:</h5>
        <input type="text" value={ this.state.data[0].phone } />
        <h5>Email:</h5>
        <input type="text" value={ this.state.data[0].email } />
        <h5>Title:</h5>
        <input type="text" value={ this.state.data[0].title }/>
        <h5>Longtitle:</h5>
        <input type="text" value={ this.state.data[0].longTitle }/>
        <h2 className="center" >Intro:</h2>
        <div className="center"><img src={require('../img/orn.png')} alt="" className="orn"/></div>
        <h5>Title:</h5>
        <input type="text" value={ this.state.data[0].introTitle } />
        <h5>Longtitle:</h5>
        <input type="text" value={ this.state.data[0].introLongTitle } />
        <h5>Link to video:</h5>
        <input type="text" value={ this.state.data[0].videoLink } />        
        <h5>Text:</h5>
        <textarea name="" id="" cols="30" rows="10" value={ this.state.data[0].introText }></textarea>
        <button type="submit" className="btn-large waves-effect waves-light xbutton">Save</button>
      </form>
    );
  }
}

export default Misc; 
```

Enter fullscreen mode Exit fullscreen mode

非常感谢任何帮助！))