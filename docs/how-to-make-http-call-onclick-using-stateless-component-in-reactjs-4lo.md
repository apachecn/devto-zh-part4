# 如何在 ReactJS 中使用无状态组件进行 http 调用 onclick？

> 原文：<https://dev.to/steerclear90/how-to-make-http-call-onclick-using-stateless-component-in-reactjs-4lo>

我有一个 React 无状态函数组件(SFC)。我想让用户单击一个按钮，然后从 SFC 发出一个 http 调用，当收到响应时，我想打开一个模态。使用 SFC 可以实现吗？或者我需要保留一个有状态的组件吗？

这是我的代码，它在加载时进行 http 调用，然后 onClick 打开模式。但是我希望这两件事在 onClick 事件中按顺序发生。

```
 function httpListCall(url) {
    const [list, setData] = React.useState(null);
    const [error, setError] = React.useState(null);
    React.useEffect(() => {
        axios
            .get(url)
            .then(function (response) {
                setData(response.data.ResultList);
            })
            .catch(function (error) {
                setError(error);
            })
    }, []);
    return { list, error };
}; 
```

Enter fullscreen mode Exit fullscreen mode

```
 const ListContainer = () => {
    const { list, error } = httpListCall("/list.json"); //THIS IS ON LOAD NOW - I WANT TO CALL IT onClick
    const [modalShow, setModalShow] = React.useState(false); 
```

Enter fullscreen mode Exit fullscreen mode

```
return(
    <React.Fragment>
        <div>
           <button onClick={() => setModalShow(true)}/> //WANT TO MAKE API CALL HERE AND THEN OPEN THE MODAL
        </div>
        <ModalWidget show={modalShow} list={advisorList} error={error} onHide={() => setModalShow(false)}/>
    </React.Fragment>
) 
```

Enter fullscreen mode Exit fullscreen mode

}

```
 export default ListContainer;
ReactDOM.render(<FindAdvisorContainer />, document.getElementById("app")); 
```

Enter fullscreen mode Exit fullscreen mode

我试图从一个函数进行 http 调用，但是它给我一个错误:“无效的钩子调用。只能在函数组件的主体内部调用挂钩。