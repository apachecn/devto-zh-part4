# 了解 react 中的脆性和非脆性测试

> 原文：<https://dev.to/sant0shg/understanding-brittle-and-non-brittle-tests-in-react-hjd>

脆性测试测试逻辑的实现，而非脆性测试测试功能。在 React 的上下文中，脆性测试通常测试状态的变化，或者窥探某个函数以检查它是否被调用。

让我们看看代码来理解 React 应用程序中的脆性和非脆性测试。

```
class App extends Component{
    state = {
        no: 0
    }

    incrementNo = () => {
        const { no } = this.state;
        this.setState({
            no: no+1
        })
    }

    render(){
        const { no } = this.state;
        return(
            <div>
                <div data-testid="no">
                    {no}
                </div>
                <button data-testid="increment-button" onClick={this.incrementNo}>
                    Increment Number
                </button>
            </div>
        )
    }
} 
```

上述反应成分的脆性试验

```
it('should increment no - implementation test', () => {
    const wrapper = shallow(<App />);
    wrapper.instance().incrementNo();
    expect(wrapper.state('no')).toEqual(1);
}) 
```

上述反应成分的非脆性试验

```
it('should increment number - functionality test', () => {
    const wrapper = shallow(<Brittle />);
    expect(wrapper.find('[data-testid="increment-no"]').text()).toEqual('0')
    wrapper.find('[data-testid="increment-button"]').simulate('click');
    expect(wrapper.find('[data-testid="increment-no"]').text()).toEqual('1')
}) 
```

## 支持非脆性测试的库

有许多库允许您在 React 中编写测试。比较流行的有[酶](https://airbnb.io/enzyme/)和[反应测试库](https://github.com/testing-library/react-testing-library)。
[Enzyme](https://airbnb.io/enzyme/)库提供了许多 API，允许你以你想要的方式编写测试。但是 [React 测试库](https://github.com/testing-library/react-testing-library)迫使你编写非脆弱的测试。想了解更多？我写了另一篇关于[酶和反应测试库](https://techdoma.in/difference-between-react-test-utilities-and/)的区别的博客