# 动态进口的 RN -问题

> 原文：<https://dev.to/bithman/rn-problem-with-dynamic-imports-29na>

嗨，我需要你的帮助，因为我遇到了一个问题。
我有一个用 react 原生 0.51 打造的 app，想更新到 0.60.4 版本。
我有一个与这段代码相关的错误:

//eslint-disable-next-line import/no-resolved，import/extensions
import * as translations from '../* */i18n . js '；

当我运行应用程序时，我有这样的错误日志:

捆绑【android，dev】。/index . js░░░░░░░░░░░░░░░░0.0%(0/1)::ffff:127 . 0 . 0 . 1--[05/aug/2019:15:19:02+0000]" get/index . bundle？platform = Android & dev = true & minify = false HTTP/1.1 " 500-"-" " ok HTTP/3 . 12 . 1 "
错误:捆绑失败:错误:无法从`/reactProject/CameraExample/src/config/i18n.js`解析模块`../**/i18n.js`:从`/reactProject/CameraExample/src/config/i18n.js`找不到模块`../**/i18n.js`。事实上，这些文件都不存在:

*   `/reactProject/CameraExample/src/**/i18n.js(.native||.android.js|.native.js|.js|.android.json|.native.json|.json|.android.ts|.native.ts|.ts|.android.tsx|.native.tsx|.tsx)`
*   `/reactProject/CameraExample/src/**/i18n.js/index(.native||.android.js|.native.js|.js|.android.json|.native.json|.json|.android.ts|.native.ts|.ts|.android.tsx|.native.tsx|.tsx)`at module resolver . resolve dependency(/react project/camera example/node _ modules/metro/src/node-haste/dependency graph/module resolution . js:163:15)at resolution request . resolve dependency(/react project/camera example/node _ modules/metro/src/node-haste/dependency graph/resolution request . js:52:18)at dependency graph . resolve dependency(/project/index . js░░░░░░░░░░░░░░░░0.0%(0/1)，失败。

在这个版本的 RN 中，动态导入发生了变化？

预先感谢你的帮助

问候弗拉维奥