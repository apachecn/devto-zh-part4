# 用 React 创建动态<select>菜单</select>

> 原文：<https://dev.to/daviddoes/creating-a-dynamic-select-menu-with-react-52fm>

*[最初发布到媒体](https://medium.com/l2code/creating-a-dynamic-select-menu-with-react-701950fbedd6)*

* * *

我必须说——对我来说，这是一个很难实现的特性。我查看了像 [react-select](https://react-select.com/home) 这样的库以及其他类似特性的实现，但就是找不到我要找的东西。走出每个人都习惯使用别人辛苦工作的舒适区，我开始工作。

React 对我来说仍然是陌生的，就像软件开发一样。因此，需要更多的心算来准确理解我想要完成的任务。

在这种情况下，我需要理解如何将以前存储的值放入表单的元素中。这个想法是，用户利用这个表格来记录冥想时间——或者我称之为*正念时刻*——在这个表格中，用户记录他们正念时刻的位置。他们很可能会在同一个位置冥想不止一次，所以我们需要让用户可以使用已经存在的位置，或者创建一个新的位置。

### 代码演练

让我们看看如何做到这一点……

```
// LocationSelect component

import React from 'react';
import { connect } from 'react-redux';
import { getMoments } from '../actions/moments'; 
```

当然，我们需要导入 React 和 connect，以及获取我们的时刻列表的动作——对我们的 API 的调用——它包含我们稍后需要的位置。

让我们初始化组件的状态:

```
class LocationSelect extends React.Component {
  constructor(props){
    super(props);
    this.state = {
      location: ''
    };
  } 
```

所以我们告诉我们的组件在它的状态中存储一个位置键，但是还没有给它一个值。我们只是给那个值一个地方，让它以后去。

让我们从 API 中获取我们的 moments 对象:

```
 componentDidMount(){
    if (this.props.authToken){
     getMoments(this.props.authToken);
    }
  } 
```

*如果*我们的 props 包含 authToken，那么使用该 authToken 运行 getMoments 操作。我们希望这个组件安装后就能实现。

在我们的 render()中，我们希望对选择菜单的选项进行排序，使其更加用户友好。为此，我们需要首先获取所有先前输入的数据，将其存储在一个新的数组中，然后对该数组进行排序。

```
render() {
  let momentsList = [];
  this.props.moments.forEach(({ id, location }) => momentsList.push({ id, location }));

  let uniqueSet = [...new Set(momentsList.map(moment => moment.location))]; 
```

所以我们创建了新的数组 momentsList。因为它们是作为道具传递的，所以我们需要从那里获取它们，并运行 forEach，从每个迭代中获取 id 和位置(在本例中，是每个时刻)。然后，我们从迭代的每一刻开始，将 id 和位置放入新数组。我们只需要 id 和位置，而不需要可能存储在该对象中的任何其他信息。

然后我们需要创建一个新的集合，这样我们就可以存储任何类型的数据。我们说，*创建一个名为 uniqueSet 的新数组，这将是一个从 map()创建的新集合，覆盖我们之前的数组，并获取位置*。

*我知道这很麻烦——如果可能的话，我很想知道一种更简洁的方法来做到这一点！

接下来，让我们按字母顺序对新数组进行排序:

```
let sortedList = uniqueSet.sort()
  .map((location, index) => <option key={index}>{location}</option>); 
```

`sort()`的默认行为是按字母顺序排序。

我们的 map 函数获取每个已经排序的条目的位置和索引，并将它们放入一个数组中，供我们以后使用。注意，我们使用索引作为 React 的键，使用位置作为要显示的文本。

在我们的 return 语句中，我们将看到所有这一切在用户端实现。

```
return (
      <div className="dropdown">
        <label htmlFor="location">Location</label>
        <input
          required
          className="form-input"
          type="text"
          name="location"
          placeholder="create or choose"
          value={this.props.location}
          onChange={event => this.handleTextFieldChange(event, 'location')}
          maxLength="20"
          autoComplete="off"
        />
        <select onChange={event => this.handleTextFieldChange(event, 'location')}>
          {sortedList}
        </select>
      </div>
    ); 
```

在这里，您可以看到我们正在将一个和我们的。我们的输入是用于创建新位置的文本字段，而我们的选择是呈现所有先前输入的位置项目。

我们的 select 正在接收我们的 sortedList 数组以用作 s——还记得我们在上面写的吗？

如果我们在想象中的文档中向上滚动，我们需要编写 onChange 处理程序 handleTextFieldChange。

```
handleTextFieldChange(event) {
    let location = event.target.value;
    let text = location // capitalize first letter
      .toLowerCase()
      .split(' ')
      .map(s => s.charAt(0).toUpperCase() + s.substr(1))
      .join(' ');
    this.props.setLocation(text, 'location');
  } 
```

event.target.value 要么是我们的输入，要么是我们的选择。如果我们在输入框中输入，或者从菜单中选择一个选项。我们还处理输入到输入字段中的所有文本；我们将第一个字符大写。这有助于保持物品的整洁。用户可能喜欢今天大写，明天全部小写。这样，我们存储的数据是统一的。你可以在我的上一篇文章中了解更多。

然后我们完成我们的组件:

```
const mapStateToProps = state => ({
  moments: state.moments.moments,
  authToken: state.auth.authToken
}); 
```

```
export default connect(mapStateToProps)(LocationSelect); 
```

并在导入后将其呈现在父组件中。

我知道这是一个粗略的方法。作为一个对 React 和 JavaScript 没有太多经验的人，也没有人亲自讨论想法，我只能阅读文档，看看其他人做了什么。我从来没有找到做同样事情的东西，所以我必须利用我能拼凑起来的东西。例如，Set 对我来说很新，老实说，我认为我没有以正确的方式使用它。也就是说，这是我所需要的。

我真的希望这能帮助到一些人，我非常欢迎任何和所有的投入。在下面，您可以找到完整的组件:

```
import React from 'react';
import { connect } from 'react-redux';
import { getMoments } from '../actions/moments';

class LocationSelect extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      location: ''
    };
  }

componentDidMount() {
    if (this.props.authToken) {
      getMoments(this.props.authToken);
    }
  }

handleTextFieldChange(event) {
    let location = event.target.value;
    let text = location // capitalize first letter
      .toLowerCase()
      .split(' ')
      .map(s => s.charAt(0).toUpperCase() + s.substr(1))
      .join(' ');
    this.props.setLocation(text, 'location');
  }

render() {
    let momentsList = [];
    this.props.moments.forEach(({ id, location }) => momentsList.push({ id, location }));
    let uniqueSet = [...new Set(momentsList.map(moment => moment.location))];

// sort list alpha, map to render
    let sortedList = uniqueSet
      .sort((a, b) => {
        if (a < b) return -1;
        else if (a > b) return 1;
        return 0;
      })
      .map((location, index) => <option key={index}>{location}</option>);

// store locations to state
    return (
      <div className="dropdown">
        <label htmlFor="location">Location</label>
        <input
          required
          className="form-input"
          type="text"
          name="location"
          placeholder="create or choose"
          value={this.props.location}
          onChange={event => this.handleTextFieldChange(event, 'location')}
          maxLength="20"
          autoComplete="off"
        />
        <select onChange={event => this.handleTextFieldChange(event, 'location')}>
          {sortedList}
        </select>
      </div>
    );
  }
}

const mapStateToProps = state => ({
  moments: state.moments.moments,
  authToken: state.auth.authToken
});

export default connect(mapStateToProps)(LocationSelect); 
```

**Changelog**
2019 年 7 月 25 日

*   修复媒体遗留的格式错误
*   更新`.sort()`代码块