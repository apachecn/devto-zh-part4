# 在测试中使用“数据测试”

> 原文：<https://dev.to/rstankov/using-data-test-in-tests-5fo8>

在测试 HTML 组件的时候，我经常看到有人用类名做选择器。例如:

```
element.find('.description button.expand-button').simulate('click'); 
```

虽然这乍看起来很方便，但也有一些缺点。HTML 结构和 css 类往往会因为设计的改变而改变。这将导致您经常重写测试。同样，如果你正在使用 [css-modules](https://github.com/css-modules/css-modules) ，你不能依赖类名。

正因为如此，在相当长的一段时间里，我开始用`data-test`属性标记元素。

反应实例*(使用[酶](https://github.com/airbnb/enzyme/)和[柴酶](https://github.com/producthunt/chai-enzyme) )* :

```
describe(Description.name, () => {
  it('cut off text based on `cutoffLength`', () => {
    const el = shallow(<Description text="test" cutoffLength={1} />);

    expect(el).to.have.text('t...');
    expect(el).not.to.have.text('test');
  });

  it('hides expand button when text is short', () => {
    const el = shallow(<Description text="test" cutoffLength={10} />);
    expect(el).not.to.have.descendants('[data-text="expand-button"]');
  });

  it('shows expand button when text is long', () => {
    const el = shallow(<Description text="test" cutoffLength={1} />);
    expect(el).to.have.descendants('[data-test="expand-button"]');
  });

  it('clicking expand button reveals the whole text', () => {
    const el = shallow(<Description text="test" cutoffLength={1} />);

    el.find('[data-test="expand-button"]').simulate('click');

    expect(el).not.to.have.descendants('[data-test="expand-button"]');
    expect(el).to.have.text('test');
  });
}); 
```

组件代码:

```
import React from 'react';
import styles from "./style.css";

export default Description extends React.Component {
  state = { expanded: false };

  render() {
    const { text, cutoffLength } = this.props;

    if (this.state.expanded || text.length < cutoffLength) {
      return (
        <div className={styles.description}>
          {this.props.text}
        </div>
      );
    }

    return (
      <div className={styles.description}>
        {`${ text.substr(0, cutoffLength) }...`}
        <button 
          data-test="expand-button" 
          className={styles.expand} 
          onClick={this.expand}>show more</button>
      </div>
    );
  }

  expand = () => {
    this.setState({ expanded: true });
  };
} 
```

我还使用了`data-test`属性来测试红宝石世界里的[水豚](https://github.com/jnicklas/capybara)。

```
describe 'Product page' do
  it 'has product description rev' do
    product = create :post, :with_long_description

    visit product_path(product)

    expect(page).not_to have_text product.description

    # This can be extracted into `find_test_attr` or `click_test_attr`
    find(:css, '[data-test="expand"]').click

    expect(page).to have_text product.description

    # This can be extracted into `have_test_arr`
    expect(page).not_to have_css('[data-test="expand"]')
  end
end 
```