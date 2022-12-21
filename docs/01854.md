# 我们如何向同一个变量声明多个值？

> 原文：<https://dev.to/mesubhokarma/how-do-we-declare-multiple-values-to-the-same-variable-306e>

下面是代码-

```
import axios from "axios"

import { put, takeLatest } from "redux-saga/effects"

import { actions, t } from "./actions"

const baseUrl = "http://host:8082/user/phone"

function* loadUserData(action) {

const response = yield axios.get(`${baseUrl}/${action.name}`)

yield put(actions.loadUserDataSuccess(response.data))

}

export function* watchLoadUserData() {

yield takeLatest(t.LOAD_USER_DATA, loadUserData)

} 
```

在响应变量的 inline 8 中，我想同时传递电子邮件和电话 URL，这样我就可以一次通过一个搜索框搜索结果。

请帮帮忙！！