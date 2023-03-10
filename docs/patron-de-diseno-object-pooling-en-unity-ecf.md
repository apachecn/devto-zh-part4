# Unity 中的对象池设计模式

> 原文：<https://dev.to/developervix/patron-de-diseno-object-pooling-en-unity-ecf>

## 是对象池吗？

想象任何肖特式的电子游戏，无论是第一人称还是第三人称；玩家和敌人都有一个共同的对象，那就是他们的武器，具体来说就是用**子弹**换各种武器。在这种体裁上有几种放置射击的方法，在**团结**上，可以用*射线投射*或扫掠物体，采用 *prefab* 的形式，并向其添加力和方向。

现在，如果我们选择使用创建具有子弹行为的物体，最正常的情况是，当我们按下按钮时，我们有一种方法来创建一颗子弹，这是通过提供给我们统一性的快照方法来实现的，我们必须为其指定要创建的物体，并为其指定一个位置

```
public GameObject bulletPrefab; //El objeto bala que se va a crear

public Transform spawnPoint;    //El lugar donde saldrá la bala, cañón de arma

//Este método se manda llamar en cada frame
public void Update()
{
     if(Input.GetMouseButtonDown(0))
     {
        Instantiate(bulletPrefab, spawnPoint.position, spawnPoint.rotation);
     }
} 
```

必须明确的是，这种方法*将会在我们每次发送呼叫时，在这种情况下，当玩家按下鼠标左键时，为我们创建一个**新对象**；如果作出必要的调整，敌人 IA 会有类似的东西，当他们向玩家射击时，这将会使我们“**”产生许多场景中的物体**，这些物体可能是类似子弹的物体，它们会消耗记忆，如果我们不确定在什么时候该子弹在，或撞击墙壁、汽车或楼层装饰的任何部分，通常会这样做:* 

```
//script que se encuentra en el objeto bala

void OnCollisionEnter(Collision col)
{
    if(col.gameObject.CompareTag("Enemy"))
    {
       Destroy(gameObject, 0f); //Método que destruye a la bala
    } 
} 
```

如上面的代码所示，当子弹检测到碰撞并满足条件就会销毁时， *Destroy()* 方法会从内存中删除它作为参数收到的对象，这样做几次不会造成影响；在射手的例子中，假设我们遇到 10 个敌人，每个人都会有自己的子弹，每个人都可以参考相同的弹头型式，当每一个射击的敌人都命令调用方法*【即时】)*，而玩家也会这样做来应对攻击， 所以我们说的是 11 个物体(10 个敌人和 1 个玩家)将根据每一个人的射击速度每 x 秒创建一个新的子弹型物体，从而增加场景中显示的物体数量，每一个物体都会有自己的行为和碰撞检测，以便在时机到来时销毁。 我们已经可以开始想象将要进行的大量过程，因为‘T4’快照()和‘T6’销毁〖T7〗，成本相对较高，快速完成 x 次可能会造成破坏。

这是对象池可以帮助我们提高游戏性能的地方，其想法是拥有一个特定类型的对象的“树”，其数量有限，自游戏开始以来，其基础是，我们将重复使用这些对象，而不是“T0”来销毁和创建“T1”对象

## 一种很容易实现的设计模式

我们首先定义一个将控制对象树的类:

```
public class PoolManagerObjects : MonoBehaviour
{
   public GameObject objPrefab;  //El objeto que vamos a estar reutilizando

   public int poolSize;          //Cuantos objetos se necesitaran

   private Queue<GameObject> objPool; //La "alberca" donde estarán los objetos

} 
```

我们使用尾巴，**尾巴**，因为它首先进入，首先退出的功能非常适合这种设计模式，这里将是我们一有需要就可以重用的对象的参考。

我们初始化树:

```
public class PoolManagerObjects : MonoBehaviour
{
   public GameObject objPrefab;  //El objeto que vamos a estar reutilizando

   public int poolSize;          //Cuantos objetos se necesitaran

   private Queue<GameObject> objPool; //La "alberca" donde estarán los objetos

   void Start()
   {
     objPool = new Queue<GameObject>();  //Inicializamos la cola

     for (int i = 0; i < poolSize; i++) //Vamos a llenar la alberca en base al tamaño
     {
         //Instanciamos el objeto y lo guardamos en una varible temporal 
         GameObject newObj = Instantiate(objPrefab);  
         objPool.Enqueue(newObj);   //Lo añadimos a la cola con Enqueue
         newObj .SetActive(false);    //Lo desactivamos ya que en ese momento no se requiere
     }
   }
} 
```

嗯，使用 for 循环，我们将根据变量 *poolSize* 将一定数量的对象填充到树中。诚然，我们使用的是*快照()，但只进行有限的次数和游戏开始时，这是在 *Start()* 方法中发生的原因。*

 *现在，我们将创建两种方法来替代方法【瞬时】、*【销毁】、*。

```
public class PoolManagerObjects : MonoBehaviour
{
   public GameObject objPrefab;  //El objeto que vamos a estar reutilizando

   public int poolSize;          //Cuantos objetos se necesitaran

   private Queue<GameObject> objPool; //La "alberca" donde estarán los objetos

   void Start()
   {
     objPool = new Queue<GameObject>();  //Inicializamos la cola

     for (int i = 0; i < poolSize; i++) //Vamos a llenar la alberca en base al tamaño
     {
         //Instanciamos el objeto y lo guardamos en una varible temporal 
         GameObject newObj = Instantiate(objPrefab);  
         objPool.Enqueue(newObj);   //Lo añadimos a la cola con Enqueue
         newObj .SetActive(false);    //Lo desactivamos ya que en ese momento no se requiere
     }
   }

   public GameObject GetObjFromPool(Vector3 newPosition, Quaternion newRotation)
   {
        //Se obtiene el 1er objeto disponible en la cola
        GameObject newObj = objPool.Dequeue();
        //Activamos el objeto, se activa su comportamiento
        newObj.SetActive(true);        
        //Le damos la posición y rotación, en donde se necesita que este
        newObj.transform.SetPositionAndRotation(newPosition, newRotation);    

        return newObj;
   }

   public void ReturnObjToPool(GameObject go)
   {
        go.SetActive(false);    //Lo desactivamos
        objPool.Enqueue(go); //Lo volvemos a añadir a la cola para reutilizarlo
   }

} 
```

最后，方法**getbjfrompool()**取代*【snapshot()*，但在这种情况下，我们只想指定我们需要树对象的位置和旋转，如果我们需要访问 **PoolManagerObjects 类的话看这里，方法会把物体还给我们。**

这同样适用于取代 *Destroy()* 的方法，该方法从参数上接收要返回到树中的对象，然后将其关闭并重新折叠。

再回到按下鼠标按钮时的例子，它会变成这样:

```
public PoolManagerObjects poolManager; //Hay que asignar desde el inspector el objeto que tenga el script PoolManagerObjects 

public Transform spawnPoint;    //El lugar donde saldrá la bala, cañón de arma

//Este método se manda llamar en cada frame
public void Update()
{
     if(Input.GetMouseButtonDown(0))
     {
        //Tenemos una referencia del objeto que tomamos de la alberca, 
       // esto puede ayudarnos para modificar valores de la bala, acceder a sus componentes, etc.
        GameObject theBullet = poolManager.GetObjFromPool(spawnPoint.position, spawnPoint.Rotation);
     }
} 
```

并且以防我们不再需要物品时:

```
//script que se encuentra en el objeto bala

public PoolManagerObjects poolManager; //Hay que asignar desde el inspector el objeto que tenga el script PoolManagerObjects 

void OnCollisionEnter(Collision col)
{
    if(col.gameObject.CompareTag("Enemy"))
    {
       //En cuanto exista una colisión y se cumpla la condición del if
       // mandaremos llamar el método para regresar la bala a la alberca y la podamos reutilizar
       poolManager.ReturnObjToPool(this.gameObject);
    } 
} 
```

因此，我们大大提高了我们的游戏性能，从 11 个物体(敌人和玩家)正在制造和销毁 x 数量的子弹，到每个人都可以进入一个有有限数量物体的树，而且每个人都在使用同样的物体。

可以通过添加一对元素来大大改进此图案，从而使其更有用，并允许对不同的对象使用不同的轴。

我希望这种模式是可以理解的，任何有助于改进的评论都是受欢迎的。

**重要注意事项**:
—对于 poolSize 变量，我们必须给它一个相当大的值，这样它才能避免通过所有对象都在使用以及树这一空白的错误，应该设置一个不是很高但也不是很低的值。**