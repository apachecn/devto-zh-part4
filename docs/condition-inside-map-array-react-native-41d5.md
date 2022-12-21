# 映射数组内的条件-反应本机

> 原文：<https://dev.to/fedebais/condition-inside-map-array-react-native-41d5>

你好，我想使用本地数据阵列登录，但是当用户名和密码出错时，我得不到错误提示。请你能指导我吗？

```
constructor (props) {
    super(props);
    this.state = {

        username: '',
        password: '',
        login:2,

    };
} 
```

onButtonPress = () => {

```
 data.map((dataItem) =>{
            if(dataItem.user == this.state.username && dataItem.pass == this.state.password  ){ 
```

this.setState({ login: 1 }，()=> {

```
 Alert.alert("Welcome");
    this.props.navigation.navigate("Home");

}); 
```

}

```
 }  )

         if (this.state.login = 2) {
             Alert.alert("Error");

         }

          } 
```