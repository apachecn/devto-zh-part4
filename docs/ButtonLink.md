# 如何为应用程序中已存在的组件(视图)编写测试(jest)

> [https://dev . to/vhoyer/how-writing-tests-jest-to-components-view-which-yes-exists-in-your-app-2hhf](https://dev.to/vhoyer/como-escrever-tests-jest-para-componentes-vue-que-ja-existem-dentro-da-sua-aplicacao-2hhf)中存在的测试-jest-to-components-view-which-is-existed]

> 免责声明:
> 除了教学内容外，以下内容是我个人的完全个人选择，如何使您的代码保持完美有序，按帐户和风险进行。写这篇帖子时，我假设你知道单位测试是什么，你知道测试对一个*基本码*的健康非常重要，你知道什么是橙色的>法案>断言

为了让大家在阅读时都在同一个页面上，我想澄清一下我使用的一些术语:

*   componente:componente vue/react/we B- componente/etc；
*   元素:标准 html 元素，无论它是元素内部或外部的元素，还是. html 文档中的元素。

读了这篇文章，我相信你会对我的测试习惯得出一些结论，所以为了避免干扰，尽量帮你集中注意力，我想说的是，我喜欢:

*   必要时转动第一部`describe`中的*安排*`describe`和`beforeAll`；
*   在达·维达之前，罗达·奥*的行为*的行为*;*
*   将所有测试分组到“父级”说明中：
*   将用户交互分组到说明中。

好的，假设你有下列成分:

```
<template>
  <div
    ref="origin"
    class="button-link-selector"
  >
    <ButtonLink
      @click="openPopover"
      class="js-label"
    >
      {{ value.label }}
    </ButtonLink>

    <div
      v-if="isOpen"
      class="button-link-selector__background js-outside"
      @click="closePopover"
    />

    <div
      v-show="isOpen"
      class="button-link-selector__popover js-popover"
      :style="{ left: `${popoverX}px`, top: `${popoverY}px` }"
    >
      <Multiselect
        :allow-empty="false"
        :internal-search="false"
        :options="options"
        :value="value"
        deselect-label=""
        label="label"
        open-direction="bottom"
        placeholder="Selecione o campus"
        select-label=""
        selected-label=""
        track-by="id"
        @close="searchChange('')"
        @select="select"
      >
        <span slot="noResult">
          <span class="button-link-selector__no-result">
            Sem resultados para a pesquisa.
          </span>
        </span>
      </Multiselect>
    </div>
  </div>
</template>

<!--
  tô omitindo o style pq jest não testa css então
  não é importante pra esse contexto
-->

<script>
// this is a utility present on npm:
// https://www.npmjs.com/package/vue-prop-validation-helper
import {
  everyItemOfArrayShouldHave,
  objectShouldHave,
} from 'vue-prop-validation-helper';

// esse é um outro componente seu qualquer
import ButtonLink from '~/components/button-link';

// esse é um componente, também presente no npm:
// https://www.npmjs.com/package/vue-multiselect
import Multiselect from 'vue-multiselect';

export default {
  name: 'ButtonLinkSelector',
  components: {
    ButtonLink,
    Multiselect,
  },
  props: {
    options: {
      required: true,
      type: Array,
      validator: everyItemOfArrayShouldHave([
        'id',
        'label',
      ]),
    },
    value: {
      required: true,
      type: Object,
      validator: objectShouldHave([
        'id',
        'label',
      ]),
    },
  },
  data: () => ({
    isOpen: false,
    popoverX: 0,
    popoverY: 0,
  }),
  beforeDestroy() {
    document.removeEventListener('scroll', this.onWindowScroll);
  },
  methods: {
    closePopover() {
      this.isOpen = false;
    },
    openPopover() {
      this.isOpen = true;

      const boundings = this.$refs.origin.getBoundingClientRect();

      this.popoverX = boundings.x - 23;
      this.popoverY = boundings.y + 20;

      document.addEventListener('scroll', this.onWindowScroll);

      this.$nextTick(() => {
        this.$el.querySelector('.multiselect__input').focus();
      });
    },
    onWindowScroll() {
      this.closePopover();

      document.removeEventListener('scroll', this.onWindowScroll);
    },
    select(value) {
      this.closePopover();

      this.$emit('select', value);
    },
  },
};
</script> 
```

完全自以为是，我总是从组件已要求的快照开始:

```
import ButtonLinkSelector from '~/components/button-link-selector';
import { shallowMount } from '@vue/test-utils';

//para uso mais a frente no
const multiselectFocus = jest.fn();

describe('Components > ButtonLinkSelector', () => {
  let wrapper;

  beforeEach(() => {
    wrapper = shallowMount(ButtonLinkSelector, {
      // só to fornecendo aqui o que o componente acima pede:
      propsData: {
        options: [
          { id: 0, label: 'minha mãe correu do boi' },
          { id: 1, label: 'minha vó correu do vô' },
          { id: 2, label: 'meu pai corre da mãe' },
          { id: 3, label: 'meu cachorro corre de mim' },
          { id: 4, label: 'eu corro de todo mundo' },
        ],
        value: {
          id: 0,
          label: 'minha mãe correu do boi',
        },
      },
    });
  });

  it('matches snapshot', () => {
    expect(wrapper.element).toMatchSnapshot();
  });
}); 
```

接下来我说，他装配时的默认行为，考虑到传递给他的*，就是显示所选项目(a.k.a.: `value`。以防万一，我把那件物品交给他:* 

```
{
  id: 0,
  label: 'minha mãe correu do boi',
} 
```

所以他应该展示的价值是:

```
 it('displays selected item', () => {
    expect(wrapper.find('.js-label').text()).toBe('minha mãe correu do boi');
  }); 
```

所以我开始思考使用者可以和这个元件互动。例如，用户可以单击组件的*标签*:

```
 describe('when user click in its label', () => {
    beforeEach(() => {
      wrapper.find('.js-label').vm.$emit('click');
    });
  }); 
```

好吧，好吧，但当用户单击组件时，会发生什么？

```
 it('shows popover', () => {
      expect(wrapper.find('.js-popover').element).toBeVisible();
    }); 
```

好吧，那我们开始测试吧。哎呀，他在那边打了个岔，因为他找不到那班`.multiselect__input`，因为她在 multiselect 里面。

我知道有两种方法可以解决这个问题。第一种是，我们可以用`mount`代替组件中的`mount`。这使得`vue-test-utils`(即协助您测试*vista components*的 lib)呈现您要求安装的组件；以及他们所有的后代，他们也是组成部分，他们所有的后代，等等。这使得你的测试更重，更耗时。所以这不是我最喜欢的方法。我更喜欢有名的*【mock】*:

```
 describe('when user click in its label', () => {
    beforeEach(() => {
      //mock multiselect
      wrapper.vm.$el.querySelector = jest.fn(() => ({ focus: multiselectFocus }));
      // p.s.: eu disse que o multiselectFocus ia voltar a aparecer, né? ta ele aí.

      wrapper.find('.js-label').vm.$emit('click');
    });
  }); 
```

如果您看一下组件的实现，就会看到它试图在组件的“`this.$el`”内查找类元素“`.multiselect__input`”(指组件的“*根”*，即文件“`*.vue`”中标记“`<template>`”的唯一子节点)。

只是由于`vue-test-utils`没有呈现出`querySelector`，所以希望`querySelector`自己找不到这个元素。所以我们就把查询选取器弄坏了。

现在我们可以自由测试了。测试通过，控制台中没有显示“*warn*”。接下来，当用户单击组件的“*标签”*时应如何处理？

```
 it("focus on multiselect's input", () => {
      expect(multiselectFocus).toHaveBeenCalled();
    }); 
```

来吧，另一种可能的互动，既然‘t0’pope 已经打开，那就是用户可以点击组件外:

```
 describe('when user clicks outside the popover', () => {
      beforeEach(() => {
        wrapper.find('.js-outside').element.click();
      });

      it('closes popover', () => {
        expect(wrapper.find('.js-popover').element).not.toBeVisible();
      });
    }); 
```

用户也可以通过`vue-multiselect`选择项目，而不是关闭，而且由于`<Multiselect>`预期已经在测试文件中得到很好的测试，理论上我们可以信任他正在做的事情。因此，当用户在 *multiselect* 内选择项目时，该组件会发送事件`select`，这也可以模拟。

而当*多项选择*发出这一事件时，我们的`ButtonLinkSelector`应该做的是(*鼓式劳斯莱斯*...我...发送另一个事件！！

```
 describe('when user selects another item through Multiselect', () => {
      beforeEach(() => {
        wrapper.find(Multiselect).vm.$emit('select', {
          id: 1,
          label: 'minha vó correu do vô',
        });
      });

      it('emits the select event once with the received payload', () => {
        // só emitiu uma vez
        expect(wrapper.emitted('select').length).toBe(1);

        // o primeiro argumento da primeira, e nesse caso, única
        // vez que esse evento foi disparado, é:
        expect(wrapper.emitted('select')[0][0]).toEqual({
          id: 1,
          label: 'minha vó correu do vô',
        });
      });
    });
  }); 
```

另一个可能发生的交互是:用户可以滚动页面，如果在 Popo 打开时滚动页面，则必须将其关闭。但我如何模拟 jest 内部的滚动？您可以将组件传递给此函数的*回调*公开给它，从而使您可以调用它。

但作为*或`addEventListener`在此`describe`内的点击事件，调用`addEventListener`，在达到此描述(没有‘当用户点击其标签’时】之前转动？简单来说，你在`beforeAll`中取笑`addEventListener`，它会在任何`beforeEach`之前滚动(甚至是第一个，它会组装组件)。*

 *另一个需要执行的操作是:如果设置了事件，则必须删除 scroll listener，因为不再需要 scroll listener。所以这次互动的最终结果如下:

```
 describe('when user scrolls page', () => {
      let userScrolledPage;

      beforeAll(() => {
        document.removeEventListener = jest.fn();

        document.addEventListener = jest.fn((_event, callback) => {
          userScrolledPage = callback;
        });
      });

      beforeEach(() => {
        userScrolledPage();
      });

      it('hides popover', () => {
        expect(wrapper.find('.js-popover').element).not.toBeVisible();
      });

      it('removes scroll listener', () => {
        expect(document.removeEventListener)
          .toBeCalledWith('scroll', expect.any(Function));
      });
    }); 
```

最后，而罩盖变得可爱，如果组件打开*而组件被破坏，则 *scroll* 的*监听器*必须拆除，因为它不会被使用* 

```
 describe('when component is destroyed', () => {
    beforeAll(() => {
      document.removeEventListener = jest.fn();
    });

    beforeEach(() => {
      wrapper.destroy();
    });

    it('removes scroll listener', () => {
      expect(document.removeEventListener)
        .toBeCalledWith('scroll', expect.any(Function));
    });
  }); 
```

那好吧。对该组件的测试一目了然。遵循最终结果，jest 文件:

```
import ButtonLinkSelector from '~/components/button-link-selector';
import Multiselect from 'vue-multiselect';
import { shallowMount } from '@vue/test-utils';

const multiselectFocus = jest.fn();

describe('Components > ButtonLinkSelector', () => {
  let wrapper;

  beforeEach(() => {
    wrapper = shallowMount(ButtonLinkSelector, {
      propsData: {
        options: [
          { id: 0, label: 'minha mãe correu do boi' },
          { id: 1, label: 'minha vó correu do vô' },
          { id: 2, label: 'meu pai corre da mãe' },
          { id: 3, label: 'meu cachorro corre de mim' },
          { id: 4, label: 'eu corro de todo mundo' },
        ],
        value: {
          id: 0,
          label: 'minha mãe correu do boi',
        },
      },
    });
  });

  it('matches snapshot', () => {
    expect(wrapper.element).toMatchSnapshot();
  });

  it('displays selected item', () => {
    expect(wrapper.find('.js-label').text()).toBe('minha mãe correu do boi');
  });

  it('does not show popover', () => {
    expect(wrapper.find('.js-popover').element).not.toBeVisible();
  });

  describe('when user click in its label', () => {
    beforeEach(() => {
      //mock multiselect
      wrapper.vm.$el.querySelector = jest.fn(() => ({ focus: multiselectFocus }));

      wrapper.find('.js-label').vm.$emit('click');
    });

    it('shows popover', () => {
      expect(wrapper.find('.js-popover').element).toBeVisible();
    });

    it('focus on multiselect\'s input', () => {
      expect(multiselectFocus).toHaveBeenCalled();
    });

    describe('when user clicks outside the popover', () => {
      beforeEach(() => {
        wrapper.find('.js-outside').element.click();
      });

      it('closes popover', () => {
        expect(wrapper.find('.js-popover').element).not.toBeVisible();
      });
    });

    describe('when user selects another item through Multiselect', () => {
      beforeEach(() => {
        wrapper.find(Multiselect).vm.$emit('select', {
          id: 1,
          label: 'minha vó correu do vô',
        });
      });

      it('emits the select event once with the received payload', () => {
        expect(wrapper.emitted('select').length).toBe(1);

        expect(wrapper.emitted('select')[0][0]).toEqual({
          id: 1,
          label: 'minha vó correu do vô',
        });
      });
    });

    describe('when user scrolls page', () => {
      let userScrolledPage;

      beforeAll(() => {
        document.removeEventListener = jest.fn();

        document.addEventListener = jest.fn((_event, callback) => {
          userScrolledPage = callback;
        });
      });

      beforeEach(() => {
        userScrolledPage();
      });

      it('hides popover', () => {
        expect(wrapper.find('.js-popover').element).not.toBeVisible();
      });

      it('removes scroll listener', () => {
        expect(document.removeEventListener)
          .toBeCalledWith('scroll', expect.any(Function));
      });
    });
  });

  describe('when component is destroyed', () => {
    beforeAll(() => {
      document.removeEventListener = jest.fn();
    });

    beforeEach(() => {
      wrapper.destroy();
    });

    it('removes scroll listener', () => {
      expect(document.removeEventListener)
        .toBeCalledWith('scroll', expect.any(Function));
    });
  });
}); 
```

这都是针对个人的，希望他帮了你得到了你那个收取考试费的无聊同事(我)的批准。任何事，请在下面评论，以创造一个合法的讨论***