# 可重用组件逻辑

> 原文：<https://dev.to/ztorstri/reuseable-component-logic-2208>

我正在实现一个自定义表，它将具有分页、过滤、排序和其他常见功能。我不想使用现有的解决方案，因为这是熟悉 React 的一个很好的练习，也因为我希望该表适合我的需要。

我遇到的问题是过滤。我要的是把“对象是否通过过滤器”逻辑放到过滤器里；我已经在其他语言中成功地使用了这种模式，它非常简洁。

然而，使用 React，所有的逻辑都必须放在父类中，因为父类不能调用子类的方法。所以我被卡住了。

大致来说，这是我希望 T1 做的事情。

```
class FilterContainer extends Component {
  constructor(props) {
    super(props);

    this.toggle = this.toggle.bind(this);

    this.state = { isOpen: false };
  }

  toggle() {
    this.setState({ isOpen: !this.state.isOpen });
  }

  render() {
    return (
      <Fragment>
        <FaFilter id="filter-icon" />
        <Popover placement="right" isOpen={this.state.isOpen} target="filter-icon" toggle={this.toggle}>
          <PopoverHeader>Filter table</PopoverHeader>
          <PopoverBody>
            {this.props.children}
          </PopoverBody>
        </Popover>
      </Fragment>
    );
  }
};

class Filter extends Component {
  constructor(props) {
    super(props);

    this.setValue = this.setValue.bind(this);
  }

  setValue(event) {
    this.props.setValue(this.props.name, event.target.value);
  }

  // I want this as a method on the filter because I will have different types of
  // filters, and I don't want to duplicate this everywhere I use a filter
  passesFilter(obj){
    if (obj.hasownproperty(this.props.name)){
      if (obj[this.props.name].contains(this.props.value)){
        return true;
      }
    }
  }

  render() {
    return (
      <Fragment>
        <Label>
          {this.props.name}

          <Input
            id={this.props.name + "-value"}
            type="text"
            value={this.props.value}
            onChange={this.setValue} />
        </Label>
      </Fragment>
    );
  }
}; 
```

但是现在想象一下，代替过滤器，我有一个 StringFilter，它可以处理大小写和正则表达式，一个 BoolFilter，它只是 true/false，可能是 DateFilter，等等。其中的每一个都有“通过过滤器”的概念，在数据表以及任何我想使用它们的地方复制它们很糟糕。

希望这有意义，如果没有，我可以提供更多的细节。