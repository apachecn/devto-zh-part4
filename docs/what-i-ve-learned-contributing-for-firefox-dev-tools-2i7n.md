# 我从火狐开发工具中学到了什么

> 原文：<https://dev.to/fannyvieira/what-i-ve-learned-contributing-for-firefox-dev-tools-2i7n>

当我们加入一个社区时，我们会觉得自己没有能力，或者没有足够的知识来解决问题，这是很常见的。主要是，当它是 Firefox 时，那是一个巨大的社区，我也经历了这一点，我会告诉你我是如何度过的。

# 不要害怕

[![](img/93c6e093087877491ee7ca1a1a05ec6d.png)](https://i.giphy.com/media/AFTWK5Qo22V2g/giphy.gif)

开始阅读并试图理解这个项目，自然，有许多片段我不明白，但还有其他的，这是非常类似于我的大学项目，见[这个组件](https://github.com/mozilla/gecko-dev/blob/master/devtools/client/webconsole/components/FilterBar.js)的开发工具项目:

```
/* This Source Code Form is subject to the terms of the Mozilla Public
 * License, v. 2.0\. If a copy of the MPL was not distributed with this
 * file, You can obtain one at http://mozilla.org/MPL/2.0/. */
"use strict";

const { Component, createFactory } = require("devtools/client/shared/vendor/react");
const dom = require("devtools/client/shared/vendor/react-dom-factories");
const { connect } = require("devtools/client/shared/vendor/react-redux");
const { getAllFilters } = require("devtools/client/webconsole/selectors/filters");
const { getFilteredMessagesCount } = require("devtools/client/webconsole/selectors/messages");
const { getAllUi } = require("devtools/client/webconsole/selectors/ui");
const actions = require("devtools/client/webconsole/actions/index");
const { l10n } = require("devtools/client/webconsole/utils/messages");
const { PluralForm } = require("devtools/shared/plural-form");
const {
  FILTERS,
  FILTERBAR_DISPLAY_MODES,
} = require("../constants");

const FilterButton = require("devtools/client/webconsole/components/FilterButton");
const FilterCheckbox = require("devtools/client/webconsole/components/FilterCheckbox");
const SearchBox = createFactory(require("devtools/client/shared/components/SearchBox"));

loader.lazyRequireGetter(this, "PropTypes", "devtools/client/shared/vendor/react-prop-types");

class FilterBar extends Component {
  static get propTypes() {
    return {
      dispatch: PropTypes.func.isRequired,
      filter: PropTypes.object.isRequired,
      persistLogs: PropTypes.bool.isRequired,
      hidePersistLogsCheckbox: PropTypes.bool.isRequired,
      showContentMessages: PropTypes.bool.isRequired,
      hideShowContentMessagesCheckbox: PropTypes.bool.isRequired,
      filteredMessagesCount: PropTypes.object.isRequired,
      closeButtonVisible: PropTypes.bool,
      closeSplitConsole: PropTypes.func,
      displayMode:
        PropTypes.oneOf([...Object.values(FILTERBAR_DISPLAY_MODES)]).isRequired,
    };
  }

  static get defaultProps() {
    return {
      hidePersistLogsCheckbox: false,
      hideShowContentMessagesCheckbox: true,
    };
  }

  constructor(props) {
    super(props);

    this.onClickMessagesClear = this.onClickMessagesClear.bind(this);
    this.onSearchBoxChange = this.onSearchBoxChange.bind(this);
    this.onChangePersistToggle = this.onChangePersistToggle.bind(this);
    this.onChangeShowContent = this.onChangeShowContent.bind(this);
    this.renderFiltersConfigBar = this.renderFiltersConfigBar.bind(this);

    this.maybeUpdateLayout = this.maybeUpdateLayout.bind(this);
    this.resizeObserver = new ResizeObserver(this.maybeUpdateLayout);
  }

  componentDidMount() {
    this.filterInputMinWidth = 150;
    try {
      const filterInput = this.wrapperNode.querySelector(".devtools-searchbox");
      this.filterInputMinWidth =
        Number(window.getComputedStyle(filterInput)["min-width"].replace("px", ""));
    } catch (e) {
      // If the min-width of the filter input isn't set, or is set in a different unit
      // than px.
      console.error("min-width of the filter input couldn't be retrieved.", e);
    }

    this.maybeUpdateLayout();
    this.resizeObserver.observe(this.wrapperNode);
  }

  shouldComponentUpdate(nextProps, nextState) {
    const {
      filter,
      persistLogs,
      showContentMessages,
      filteredMessagesCount,
      closeButtonVisible,
      displayMode,
    } = this.props;

    if (nextProps.filter !== filter) {
      return true;
    }

    if (nextProps.persistLogs !== persistLogs) {
      return true;
    }

    if (nextProps.showContentMessages !== showContentMessages) {
      return true;
    }

    if (
      JSON.stringify(nextProps.filteredMessagesCount) !==
        JSON.stringify(filteredMessagesCount)
    ) {
      return true;
    }

    if (nextProps.closeButtonVisible != closeButtonVisible) {
      return true;
    }

    if (nextProps.displayMode != displayMode) {
      return true;
    }

    return false;
  }

  componentWillUnmount() {
    this.resizeObserver.disconnect();
  }

  /**
   * Update the boolean state that informs where the filter buttons should be rendered.
   * If the filter buttons are rendered inline with the filter input and the filter
   * input width is reduced below a threshold, the filter buttons are rendered on a new
   * row. When the filter buttons are on a separate row and the filter input grows
   * wide enough to display the filter buttons without dropping below the threshold,
   * the filter buttons are rendered inline.
   */
  maybeUpdateLayout() {
    const {
      dispatch,
      displayMode,
    } = this.props;

    const filterInput = this.wrapperNode.querySelector(".devtools-searchbox");
    const {width: filterInputWidth} = filterInput.getBoundingClientRect();

    if (displayMode === FILTERBAR_DISPLAY_MODES.WIDE) {
      if (filterInputWidth <= this.filterInputMinWidth) {
        dispatch(actions.filterBarDisplayModeSet(FILTERBAR_DISPLAY_MODES.NARROW));
      }

      return;
    }

    if (displayMode === FILTERBAR_DISPLAY_MODES.NARROW) {
      const filterButtonsToolbar =
        this.wrapperNode.querySelector(".webconsole-filterbar-secondary");

      const buttonMargin = 5;
      const filterButtonsToolbarWidth = Array.from(filterButtonsToolbar.children).reduce(
        (width, el) => width + el.getBoundingClientRect().width + buttonMargin, 0);

      if (filterInputWidth - this.filterInputMinWidth > filterButtonsToolbarWidth) {
        dispatch(actions.filterBarDisplayModeSet(FILTERBAR_DISPLAY_MODES.WIDE));
      }
    }
  } 
```

所以，我想，“好吧，当我和我的大学同事一起开发的项目中有什么不明白的地方，我会试着去理解，去寻找或者问他们，为什么我不在这里做同样的东西？”

最初，我很羞于向某人提问(认识我的人知道我很惭愧，幸运的是我在最近的日子里一直在做这个哈哈)，但是当我意识到开发人员与社区的互动时，我看到每个人总是非常愿意教你如何做每件事情，并帮助你处理一些问题。

所以我开始参与进来，我去下载项目并安装，我遇到了许多问题，大多数问题都是通过查看社区报告和解决的问题解决的，但是有一个问题我不记得了，我不能解决。我不得不在 firefox 聊天中发送一条消息，几分钟后，一名开发人员回复了我，我解决了这个问题(感谢尼古拉斯·切沃贝)

就这样，我终于在我的机器上建立了这个项目，然后，不要害怕寻求帮助，当你对一件事情感到非常失落时，肯定会有人愿意帮助你。

我忘记提到的一个重要的部分是，为了在我的机器上进行设置，我首先阅读了[这个文档](https://docs.firefox-dev.tools/)。我按照说明，它非常完整地解释了配置和他们的工作流程，代码模式等

# 先做第一件事

[![](img/a24fe6e0c641eb7a7c47948871ef552a.png)](https://i.giphy.com/media/7zYMmNDKi8SYDAbxLB/giphy.gif)

一旦我建立了项目，我非常兴奋地开始开发，毕竟，我几乎实现了为火狐做贡献的梦想！！

又一次，我有点困惑，然后我该怎么办？我问帮助我的人，因为我可以开始做贡献了。他对开源有一点经验，知道大多数社区都有一些被称为“好的第一个问题”的问题，所以他向我推荐了两个链接:

1.  [共进好第一期](https://codetribute.mozilla.org/projects/devtools?tag%3Dgood-first-bug)
2.  [火狐开发工具的 bug](https://bugs.firefox-dev.tools/?easy&tool=all)

然后，我看了一个问题，[这个](https://bugzilla.mozilla.org/show_bug.cgi?id=1421342)，我想，“这是我的第一个问题！”，我，以及导师，我们低估了这个问题，当重新开始讨论时，维护人员意识到这不是那么简单，我不得不与网络团队交谈，改变一些对初学者来说不那么直观的文件，这使我寻找其他问题

我发现的第二个是这个。简而言之，当我们有一个链接标签时，我们需要显示一个从该标签复制链接的选项。以前，我们需要复制整个标签并手动提取链接。如果你读了这个主题，我最初以不同的方式理解这个问题，我想使用正则表达式，因为我不知道它包括一个 html 标签。但是后来一位导师去了那里，并解释了真正应该做的事情，他甚至建议使用一种工具来调试和更好地可视化我正在做的事情，这就开始了下一个主题。

(正如你所注意到的，我失败了很多次，但幸运的是总有人帮助我，所以不要害怕尝试！！)

# 使用对你有利的工具&调试

[![](img/c1209219df02ddb454dddada599248b0.png)](https://i.giphy.com/media/102h4wsmCG2s12/giphy.gif)

正如我之前所说，文档很好地解释了每个模块，例如，当我需要修改控制台中的一些东西时，我直接进入 [webconsole 目录](https://github.com/mozilla/gecko-dev/tree/master/devtools/client/webconsole)并试图找到与问题描述相关的代码。

为了帮助我调试浏览器的代码，我使用了前面引用的[工具](https://developer.mozilla.org/en-US/docs/Tools/Browser_Toolbox)，使用它非常重要，因为我们习惯于在浏览器控制台上分析 web 开发项目，但是如果我们正在开发浏览器控制台，会发生什么呢？

我们无法看透控制台，浏览器控制台本身的代码，所以我们使用它，它在我们的开发中是如此不可或缺。

此外，为了补充文档研究，我发现[这个链接](https://developer.mozilla.org/en-US/docs/Tools)非常有趣，因为它给出了这些工具的一些内部内容的解释。

基本上，我使用这个浏览器工具箱工具分析代码，并查看文档和链接，有时当我没有真正找到我需要的东西的链接时，我会问别人，然后再次不要感到害怕！！！

一旦你做到了这一点，你就已经知道如何在文档中提交你的第一个 pr(或者他们所说的，你的补丁),正确地解释每一件事，如何将它分配给一个审核者，如何提交。值得一提的是，他们使用了一个不同的 git 系统，它是 mercurial 系统，但它与完成事情的方式非常相似，所以如果您了解 git，您将能够展开。

当我提交我的第一份 pr 并被接受时，最酷的事情之一是在聊天中得到认可，我非常高兴，高兴得甚至拍下了那一刻的照片:

[![Captura de tela_2019-03-23_11-04-14](img/0de7911a85eee97f88e4d7f3bd660997.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8s1a7H4V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/14113480/57582306-536d4880-7499-11e9-9c24-14d69c7d9477.png)

此外，几乎每个星期，他们都在他们的博客上发布一周的更新，包括贡献者的工作，我的名字出现在页面上，两次，人们，我很高兴！！！！！！！！！！！！！！！！

[第一周](https://blog.nightly.mozilla.org/2019/03/15/these-weeks-in-firefox-issue-55/)
[第二周](https://blog.nightly.mozilla.org/2019/04/05/these-weeks-in-firefox-issue-56/)

所以再一次，不要害怕为 firefox 社区做贡献，我克服了我的恐惧和羞愧，我觉得很有成就，不仅如此，你的工作肯定会得到很好的认可，社区非常接受和欢迎，所以我在这里留下我作为一个优秀社区的经验和建议。