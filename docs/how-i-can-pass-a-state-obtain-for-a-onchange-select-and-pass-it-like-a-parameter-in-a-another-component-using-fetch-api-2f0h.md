# 如何使用 fetch api 传递 onchange select 的状态获取，并像在另一个组件中传递参数一样传递它

> 原文：<https://dev.to/bryanro81647827/how-i-can-pass-a-state-obtain-for-a-onchange-select-and-pass-it-like-a-parameter-in-a-another-component-using-fetch-api-2f0h>

//这是我的组件 Formulario，其中我使用 onchange 事件获取 idDepartamento

从“react”导入 React，{ Component }；
从'导入省份。/provincia . js '
class Formulario 扩展组件{
构造器(道具){
超级(道具)；
this.state = {departamento:[]，idDepartamento:null }；

```
 this.handleChangeDepartment = this.handleChangeDepartment.bind(this);
   // this.handleSubmit = this.handleSubmit.bind(this);
}

handleChangeDepartment=(event)=> {

    this.setState({idDepartamento: event.target.value});
    alert(this.state.idDepartamento)

  }

componentDidMount() {
    fetch("http://10.85.110.6:81/api/Departamento")
    .then(res => res.json())
    .then((data) => {
      this.setState({ departamento : data })
      console.log(data)
    })
}

render () {
    const {departamento,idDepartamento,provincia}=this.state;
    let optionItems = departamento.map((departamento) =>
            <option key={departamento.idDepartamento} value={departamento.idDepartamento}>{departamento.descripcion}</option>
        );

    return (
        <div>
        <form onSubmit={this.handleSubmit}>
         <select onChange={this.handleChangeDepartment}>
            {optionItems}

         </select>

           <Provincia />

         </form >

         </div>
    )
} 
```

}
导出默认公式 Rio；

///而这里是我要使用 fetch api
import React，从‘React’中{ Component }传递 idDepartamento like 参数的地方；
从'导入配方。/formulario . js '
const tournyAPI = "[http://10 . 85 . 110 . 6:81/API/Provincia？idDepartamento = 4](http://10.85.110.6:81/api/Provincia?idDepartamento=4)；
class Provincia 扩展组件{
构造器(props) {
超级(props)；
this.state = {provincia:[]，idDepartamento:props.id，idProvincia:' '，departamento:[]}；

```
 this.handleChangeDepartment = this.handleChangeDepartment.bind(this);
   // this.handleSubmit = this.handleSubmit.bind(this);
}

handleChange(event){
    this.setState({idProvincia: event.target.idProvincia});
    console.log(event.target.idProvincia);
}

componentDidMount() {
    const abc=this.handleChangeFormulario();
    fetch(tournyAPI+abc)
    .then(res => res.json())
    .then((data) => {
      this.setState({ departamento : data })
      console.log(data)
    })
}
render(){
    const {departamento,idDepartamento,provincia}=this.state;
let provinciaItems = provincia.map((provincia) =>
<option key={provincia.idDepartamento} value={provincia.idDepartamento}>{provincia.descripcion}</option> 
```

);
回车(

{provinciaItems}

)
}

}

```
export default Provincia; 
```