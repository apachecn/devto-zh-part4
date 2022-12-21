# 反应漂亮和扑克游戏需要帮助...

> 原文：<https://dev.to/amishmaqbool/react-beautifull-dnd-poker-game-need-help-16c2>

嘿，我试图做一个像扑克一样的游戏，但它是拖放式的，当我在拖放区放下一张牌时，另一张相同的牌出现在拖放区，我想防止它，我使用了 react-beautiful-dnd，牌应该按顺序排列，国王，王后，杰克，1，2，3，4，5，6，7，8，9，10，检查一个条件已经实现，只是想不出如何阻止相同的牌被放在拖放区

下面是 index.js 组件

从' React '导入 React
从' react-dom '导入 ReactDOM】导入' @atlaskit/css-reset'
从' react-beautiful-dnd '导入{ DragDropContext }
从' styled-components '导入 styled

从'导入 initialData。/initial-data'
从'导入列'。/列'

const Container = style . div `display:flex;` 

类应用程序扩展 React。组件{
状态=初始数据

onDragEnd = result = > {
const { destination，source，draggableId } = result

```
if (!destination) {
  return
}

if (
  destination.droppableId === source.droppableId &&
  destination.index === source.index
) {
  return
} 
```

Enter fullscreen mode Exit fullscreen mode

console.log("src "，source)；

```
const start = this.state.columns[source.droppableId]
 console.log(start)
const finish = this.state.columns[destination.droppableId]
// console.log(finish)
//   console.log(start===finish)
if (start === finish) {
  const newTaskIds = Array.from(start.taskIds)
  newTaskIds.splice(source.index, 1)
  newTaskIds.splice(destination.index, 0, draggableId)

  const newColumn = {
    ...start,
    taskIds: newTaskIds
  }

  const newState = {
    ...this.state,
    columns: {
      ...this.state.columns,
      [newColumn.id]: newColumn
    }
  }

  this.setState(newState)
  return
}
const pattren = ['task-1', 'task-2', 'task-3', 'task-4','task-5','task-6','task-7','task-8','task-9','task-10','task-11','task-12','task-13'];
// Moving from one list to another
const startTaskIds = Array.from(start.taskIds)
console.log(Array.from(start.taskIds))
console.log("start",source.index)
startTaskIds.splice(source.index, 1)
// const pickeditem=startTaskIds[1];

const newStart = {
  ...start,
  taskIds: startTaskIds
} 
```

Enter fullscreen mode Exit fullscreen mode

console . log(start taskids)
const finishTaskIds = array . from(finish . taskids)
console . log(array . from(finish . taskids))
finishTaskIds . splice(destination . index，0，draggableId)
const new finish = {
...finish，
taskIds:finishTaskIds
}
console . log(finishTaskIds)
console . log(" lastind "，finishTaskIds[length-2])
console . log(" drag able "，draggableId)
//if(finishTaskIds[finishTaskIds . length-1<draggableId])
if(finishTaskIds . indexof(draggableId)= = pattren . indexof。
const newState = {
...
列:{
...this.state.columns，
[newStart.id]: newStart，
[new finish . id]:new finish
}
}

```
this.setState(newState)
// console.log("did",draggableId)
// console.log("state",this.state.tasks)
// const items =this.state.tasks;
// const valueToRemove = 'task-1';
// const filteredItems = items.filter(function(item) {
//     return item !== valueToRemove
//   })
// this.state.tasks=filteredItems;
// var array=this.state.tasks;
// var index= 
```

Enter fullscreen mode Exit fullscreen mode

} else {
return；
}
}

render(){
return(

{ this . state . column order . map(columnId =>{
const column = this . state . columns【columnId】
const tasks = column . taskids . map(
taskId =>this . state . taskId
)

```
 return (
          <Column key={column.id} column={column} tasks={tasks} />
        )
      })}
    </Container>
  </DragDropContext>
) 
```

Enter fullscreen mode Exit fullscreen mode

}
}

const rootElement = document . getelementbyid(' root ')
react DOM . render(，root element)

下面是 column.js 组件

从' React '导入 React
从' styled-components '导入 styled
从' import Task from '。/task'
从' react-beautiful-dnd '导入{ Droppable }

const Container = styled . div `
margin:8px；
边框:1px 纯色浅灰色；
border-radius:2px；
宽度:220px

显示器:flex
flex-direction:列；
 `const Title = styled.h3`
填充:8px
 `const TaskList = styled.div`
填充:8px
过渡:背景色 0.2s 缓和；
background-color:$ { props =>
props . is dragging over？天蓝色':'白色' }
flex-grow:1；
最小高度:100px
`
//const getShuffledArr = arr =>{
//if(arr . length = = = 1){ return arr }；
//const rand = math . floor(math . random()* arr . length)；
// return [arr[rand]，...getShuffledArr(arr.filter((_，i) = > i！=兰特))]；
//}；

//函数 shuffle array(array){
//let I = array . length-1；
//for(；I>0；I-){
//const j = math . floor(math . random()*(I))；
//const temp = array[I]；
//array[I]= array[j]；
//array[j]= temp；
// }
//返回数组；
// }

const shuffle =(arra 1)= > {
var CTR = arra 1 . length，temp，index

//数组中有元素的同时
while (ctr > 0) {
//选取一个随机索引
index = math . floor(math . random()* CTR)；
//减少 CTR 1
CTR-；
//并与之交换最后一个元素
temp = arra 1[CTR]；
arra 1[CTR]= arra 1[index]；
arra 1[index]= temp；
}
返回 arra1
}
导出默认类列扩展 React。组件{

```
componentWillMount() { 
```

Enter fullscreen mode Exit fullscreen mode

}
render(){
//const shuffle darr = getshuffle darr(this . props . tasks)；

```
const shuffledArr = shuffle(this.props.tasks);
return (

  <Container>
    {this.props.column.title}
    <Droppable droppableId={this.props.column.id} type="TASK">
      {(provided, snapshot) => (
        <TaskList
          ref={provided.innerRef}
          {...provided.droppableProps}
          isDraggingOver={snapshot.isDraggingOver}
        >
          {shuffledArr.map((task, index) => (
            <Task key={task.id} task={task} index={index} />
          ))}
          {provided.placeholder}
        </TaskList>
      )}
    </Droppable>
  </Container>
) 
```

Enter fullscreen mode Exit fullscreen mode

}

}

下面是任务。js 组件

从“React”导入 React
从“styled-components”导入 styled
从“react-beautiful-dnd”导入{ Draggable }

const Container = style . div `border: 1px solid lightgrey;
border-radius: 2px;
padding: 8px;
margin-bottom: 8px;
transition: background-color 0.2s ease;
background-color: ${props =>
props.isDragDisabled
? 'lightgrey'
: props.isDragging
? 'lightgreen'
: 'white'};` 

导出默认类任务扩展 React。component {
render(){
//const isDragDisabled = this . props . task . id = = = ' task-1 '
return(
draggabled = { this . props . task . id }
index = { this . props . index }
//isDragDisabled = { isDragDisabled }
>
{(提供，快照)= > (
{...provided.draggableProps}
{...provided . dragchandleprops }
ref = { provided . innerref }
is dragging = { snapshot . is dragging }
//is dragdragdisabled = { is dragdragdisabled }
>
【this . props . task . content }

```
 </Container>
    )}
  </Draggable>
) 
```

Enter fullscreen mode Exit fullscreen mode

}
}

如果有人能帮忙的话，那将是非常有帮助的，因为我还处于学习的初级阶段