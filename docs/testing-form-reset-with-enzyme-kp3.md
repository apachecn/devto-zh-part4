# 用酶重置测试表单

> 原文：<https://dev.to/jeffpereira/testing-form-reset-with-enzyme-kp3>

## 简介

作为一名相对较新的 Javascript 开发人员，我把我所做的一切都视为学习的机会。在我目前工作的应用程序中，测试覆盖率需要一些重大改进，不仅要对新功能进行接近 100%的测试，还要对你接触的任何代码进行测试，以增加应用程序的测试覆盖率。这一举措确实让我对编写测试产生了兴趣，所以我接下来的两篇文章将会讨论这个主题。

## 问题

最近我的任务是创建一个新的表单。这是一个带有一个字段的简单 Redux 表单。需要发送的所有其他数据都被屏蔽，只作为 API 请求的一部分发送。所以这个表单只有一个“Name”字段，一个“Cancel”按钮，最后还有一个“Submit”按钮。

对于这个条目，让我们只关注测试“取消”按钮。我想确定的主要事情是，处于模态中的窗体关闭，并且窗体重置。例如，假设您在“FOO”中输入了“Name”字段，然后决定取消并稍后返回，我们不应该保存该输入。在我们的例子中，为用户清除表单是一个最佳实践。

在为这个表单编写测试时，我遇到了一个有趣的问题。我用酶来测试渲染，似乎我找不到一种方法来有效地测试前面提到的“取消”按钮。我能够测试出模态关闭了，但是验证表单中的字段不再被填充变得有点困难。

## 按钮

我们应用程序中的按钮创造了一个新事物。姑且称之为“创建 Foo”。一旦点击，它将使一个模态可见，其中有一个形式。这里是按钮的一些示例代码。

```
class CreateFooButton extends Component {
    constructor(props) {
        super(props);
        this.state = { dialogOpen: false };
    }

    static propTypes = {
        someName: string
    }

    static defaultProps = {
        someName: ''
    }

    setDialogOpen(dialogOpen) {
        return this.setState({ dialogOpen });
    }

    render() {
        const { dialogOpen } = this.state;
        const { someName } = this.props;

        if (!someName) return null;
        return (
            <div>
                <Button
                    onClick={ () => this.setDialogOpen(true) }
                    name='button'
                    label={ I18n.t('create_foo_button') }
                /> 
                <Modal
                    visible={ dialogOpen }
                >
                    <CreateFooModalForm
                        setDialogOpen={ this.setDialogOpen.bind(this) }
                        someName={ someName }
                    />
                </Modal>
            </div>
        );
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 表单

在下面的表格中，我们只有一个简单的名称字段。然后，用户可以点击“提交”,这将调用函数`handleSubmit`,这是发送带有所有数据的神奇 API 请求的部分。如果用户点击“取消”按钮，我们重置表单，并关闭模态。

```
 <StandaloneForm>
                <div styleName='container'>
                    <Confirm
                        disabled={ disabled }
                        onTouchTapPrimary={ handleSubmit(this.handleFormSubmit) }
                        onTouchTapSecondary={ () => {
                            reset();
                            setDialogOpen(false);
                        } }
                        label='save'
                    >
                        <Field
                            name={ NAME }
                        />
                    </Confirm>
                </div>
            </StandaloneForm> 
```

Enter fullscreen mode Exit fullscreen mode

## 测试

### 确保模态关闭

首先，我们要测试模态是否关闭。使用酶给我们的一些工具，这是相对微不足道的事情。下面我们将设置一些数据来呈现组件，并模拟出`setDialogOpen`函数。这里我们不测试该函数的实现，因为这是不必要的，我们只是想确保该函数被调用。我们希望确保填写表单不会影响“取消”按钮。所以我们将它放入，找到“取消”按钮，然后点击它。

```
 describe('when Cancel button is clicked', () => {
            test('should close the modal form', () => {
                const mockedSetDialogOpen = jest.fn();
                const wrapper = mount(
                    <Root>
                        <CreateFooModalForm
                            setDialogOpen={ mockedSetDialogOpen }
                            someName={ 'foo' }
                        />
                    </Root>
                );
                const input = wrapper.findWhere(
                    n => n.name() === 'Field' && n.props().name === NAME
                ).find('input');
                input.simulate('change', { target: { value: 'bar' }});
                const cancel = wrapper.findWhere(
                    n => n.name() === 'button'
                ).first();

                cancel.simulate('click');

                expect(mockedSetDialogOpen).toHaveBeenCalledWith(false);
            });

        }); 
```

Enter fullscreen mode Exit fullscreen mode

### 测试模态的内容

当测试模型内部的内容时，用 Enzyme 的 find 找到它就足够简单了，然后写一个简单的断言，内容就在那里。然而，这并不可行，因为模态呈现在不同的门户元素中，而不是页面上的其他元素。一些像 react-modal 这样的模态库提供了一些测试工具，但是不幸的是我们没有使用这些库。因此，为了测试 form modal 中的任何内容，我们使用这一点存根逻辑来用一个 div 替换我们的 modal，该 div 可以插入所有内容。有点不太好，但是我们无法用现有的工具想出更好的方法。

```
jest.mock('components/Modal', () => ({ children, ...rest }) => <div id='modal' data={{ ...rest }}>{ children }</div>); 
```

Enter fullscreen mode Exit fullscreen mode

### 确保表单复位

最初我对自己说，找到按钮，点击它，然后确保一切正常。不过，我想确定的一件事是，我需要填写这个字段，然后确保更改存在。

```
describe('when Cancel button is clicked', () => {
            test('should clear out text from fields', () => {
                const mockedSetDialogOpen = jest.fn();
                const sidebarParams = {
                    ...a bunch of unimportant data
                };
                const wrapper = mount(
                    <Root>
                        <CreateFooModalForm
                            setDialogOpen={ mockedSetDialogOpen }
                            someName={ 'foo' }
                        />
                    </Root>
                );
                // find field and update input with value
                const field = wrapper.findWhere(
                    n => n.name() === 'Field' && n.props().name === NAME
                ).find('input').simulate('change', { target: { value: 'bar' }});

                expect(field).toHaveProp('value', 'bar');
                // find cancel button
                const cancel = wrapper.findWhere(
                    n => n.name() === 'button'
                ).first();

                cancel.simulate('click');

                expect(inputAfterReset).toHaveProp('value', '');
                expect(mockedSetDialogOpen).toHaveBeenCalledWith(false);
            });
        }); 
```

Enter fullscreen mode Exit fullscreen mode

我们在这里遇到的第一个问题是当我们失败时。在模拟更改后记录包装器时，数据不存在。我花了几秒钟才意识到我需要重新找到那个领域。

```
describe('when Cancel button is clicked', () => {
            test('should clear out text from fields', () => {
                const mockedSetDialogOpen = jest.fn();
                const sidebarParams = {
                    ...a bunch of unimportant data
                };
                const wrapper = mount(
                    <Root>
                        <CreateFooModalForm
                            setDialogOpen={ mockedSetDialogOpen }
                            someName={ 'foo' }
                        />
                    </Root>
                );
                // find field and update input with value
                wrapper.findWhere(
                    n => n.name() === 'Field' && n.props().name === NAME
                ).find('input').simulate('change', { target: { value: 'bar' }});
                // we need to do find the field again to get the updated value
                const field = wrapper.findWhere(
                    n => n.name() === 'Field' && n.props().name === NAME
                ).find('input');
                expect(field).toHaveProp('value', 'bar');

                // find cancel button
                const cancel = wrapper.findWhere(
                    n => n.name() === 'button'
                ).first();

                cancel.simulate('click');

                expect(field).toHaveProp('value', '');
                expect(mockedSetDialogOpen).toHaveBeenCalledWith(false);
            });
        }); 
```

Enter fullscreen mode Exit fullscreen mode

从这里，我们得到一个类似的失败。即使我们模拟单击 cancel 按钮，数据也不会改变。我很快就把这个联系起来了。我们需要再次找到该字段，以便从 cancel 按钮获取更新后的值。

```
describe('when Cancel button is clicked', () => {
            test('should clear out text from fields', () => {
                const mockedSetDialogOpen = jest.fn();
                const sidebarParams = {
                    ...a bunch of unimportant data
                };
                const wrapper = mount(
                    <Root>
                        <CreateFooModalForm
                            setDialogOpen={ mockedSetDialogOpen }
                            someName={ 'foo' }
                        />
                    </Root>
                );
                // find field and update input with value
                wrapper.findWhere(
                    n => n.name() === 'Field' && n.props().name === NAME
                ).find('input').simulate('change', { target: { value: 'bar' }});
                // we need to do find the field again to get the updated value
                const inputBeforeReset = wrapper.findWhere(
                    n => n.name() === 'Field' && n.props().name === NAME
                ).find('input');
                expect(inputBeforeReset).toHaveProp('value', 'bar');
                // find cancel button
                const cancel = wrapper.findWhere(
                    n => n.name() === 'button'
                ).first();

                cancel.simulate('click');

                // find field again to that the values are updated after the reset
                const inputAfterReset = wrapper.findWhere(
                    n => n.name() === 'Field' && n.props().name === NAME
                ).find('input');

                expect(inputAfterReset).toHaveProp('value', '');
                expect(mockedSetDialogOpen).toHaveBeenCalledWith(false);
            });
        }); 
```

Enter fullscreen mode Exit fullscreen mode