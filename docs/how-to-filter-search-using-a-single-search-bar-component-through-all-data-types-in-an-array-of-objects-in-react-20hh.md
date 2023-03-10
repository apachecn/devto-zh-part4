# 如何在 React 中使用单个搜索栏组件对一组对象中的所有数据类型进行过滤搜索？

> 原文：<https://dev.to/mesubhokarma/how-to-filter-search-using-a-single-search-bar-component-through-all-data-types-in-an-array-of-objects-in-react-20hh>

我调用了一个对象数组，并以表格格式呈现数据。我需要使用单个搜索栏组件实现过滤搜索，该组件可以过滤所有数据类型，如字符串、联系电话、电子邮件地址、地址以及我从 API 获取的更多信息。

```
[
{
id: 4,
accountDetail: {
accountStatus: "ACTIVE",
referralCode: "X1DNO8GW",
signUpRemarks: "",
adminRemarks: "",
accountStatusRemarks: "",
signUpTime: {
date: {
year: 2019,
month: 3,
day: 20
},
time: {
hour: 21,
minute: 32,
second: 2,
nano: 0
}
},
lastUpdationTime: {
date: {
year: 2019,
month: 3,
day: 20
},
time: {
hour: 21,
minute: 32,
second: 2,
nano: 0
}
}
},
name: "Narendra Modi",
phone: {
countryCode: "91",
number: 9876543210,
verified: true
},
emailId: {
address: "modi@gmail.com",
verified: false
},
address: {
street: "abc",
locality: {
id: 2,
name: "ABC",
pincode: 123021,
latLong: {
latitude: 0,
longitude: 0
},
city: {
id: 1683,
district: {
id: 55,
state: {
id: "IND-MH",
code: "MH",
name: "Maharshtra",
tin: 18,
type: "S"
},
code: "",
name: "Hinjewadi"
},
name: "Pune"
}
},
latLong: {
latitude: 0,
longitude: 0
}
},
password: "9876543210",
type: "RETAILER",
businessDetail: {
gstRegistered: false,
gstin: "",
businessName: "BJP Group",
CODLimit: 0,
creditLimit: 0
},
walletBalance: 0
},
{}
] 
```

下面是我用来搜索对象数组中所有名字的代码，

```
let filteredData = this.state.users.filter(user => {
      return (
        user.name
          .toLowerCase()
          .indexOf(this.state.filterUsers.toLowerCase()) !== -1
      )
    }) 
```

我需要过滤数据，如联系电话，姓名，企业名称，电子邮件 id，地址和对象中的每个字段。我该怎么做？