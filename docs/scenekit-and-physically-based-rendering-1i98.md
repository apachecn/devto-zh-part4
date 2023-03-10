# 基于场景和物理的渲染

> 原文：<https://dev.to/chicio/scenekit-and-physically-based-rendering-1i98>

*让我们尝试使用 SceneKit 及其基于物理的
渲染特性来构建一个场景。*

SceneKit 是我最喜欢的 iOS SDK 内部框架之一。什么是 SceneKit？让我们看看来自
开发者苹果网站的定义:

> SceneKit 将高性能渲染引擎与描述性 API 相结合，用于导入、操纵和渲染 3D 资源。与 Metal 和 OpenGL 等要求您精确实现显示场景的渲染算法的低级 API 不同，SceneKit 只需要描述场景的内容以及您希望它执行的动作或动画。

正如你从定义中看到的，里面有很多东西。基本上，通过使用 SceneKit，你将能够创建游戏和其他 3D 应用程序，而不需要了解任何计算机图形算法，物理模拟等等。你基本上是用物体和特征来描述一个场景，苹果会为你做好一切😎。

关于 SceneKit 在计算机图形学方面最有趣的事情之一已经在 2016 年推出:基于物理的渲染(PBR)。
😉).这意味着 SceneKit 可以使用自己全新的基于物理的渲染引擎来渲染基于物理的场景。值得吗？当然可以！！😊所以，我们来试试吧！在本帖中，我们将使用 SceneKit 中的主要 PBR 功能从头开始创建一个场景。在这篇文章的最后，你将能够渲染包含在下图中的场景。所以
是时候开始编码了！！

[![Physically based scene right](img/7b785813278351ee17a0646db122ac5d.png "Physically based scene right")](https://res.cloudinary.com/practicaldev/image/fetch/s--CGDXeBVf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.fabrizioduroni.it/asseimg/posts/physically-based-rendering-scene-right.jpg)

在场景构建中使用的一般方法如下:对于每个主要的场景类别组件，我们将创建一个类来封装相应的`SCNNode`的创建、基本的 SceneKit 单元元素及其设置，以获得我们想要的特征。

我们要创建的第一个类是`Light`类，它封装了我们设置灯光所需的基本特性:位置、旋转和通用颜色。场景中的光线使用`SCNLight`类来表示。

```
class Light {
    let node: SCNNode

    init(lightNode: SCNNode) {
        node = lightNode
    }

    init(lightFeatures: LightFeatures) {
        node = SCNNode()
        createLight()
        set(lightFeatures: lightFeatures)
    }

    func createLight() {
        node.light = SCNLight()
    }

    private func set(lightFeatures: LightFeatures) {
        node.light?.color = lightFeatures.color
        node.position = lightFeatures.position
        node.eulerAngles = lightFeatures.orientation;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

光线的基本特征必须在构建时使用一个`LightFeatures`对象来传递。

```
class LightFeatures {
    let position: SCNVector3
    let orientation: SCNVector3
    let color: UIColor

    init(position: SCNVector3, orientation: SCNVector3, color: UIColor) {
        self.position = position
        self.orientation = orientation
        self.color = color
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们现在准备创建我们的`PhysicallyBasedLight`作为`Light`类的子类。我们基于物理的灯将是`.directional`类型，我们将定制它的`intensity`和`temperature`。强度是光的通量(同样，如果你不知道它是什么，去看看我的第一篇关于物理渲染的文章😛)，第二个是用开尔文表示的色温(记住:6500 K 对应的是纯白太阳光)。我们激活了其他有趣的功能:通过将`castsShadow`设置为`true`我们激活了使用阴影贴图技术的阴影渲染，通过将`orthographicScale`设置为`10`我们从光线中扩展了一点场景的可见区域，因此我们正在改进阴影贴图的构造。

```
class PhysicallyBasedLight: Light {

    init(lightFeatures: LightFeatures, physicallyBasedLightFeatures: PhysicallyBasedLightFeatures) {
        super.init(lightFeatures: lightFeatures)
        set(physicallyBasedLightFeatures: physicallyBasedLightFeatures)
        activateShadow()
    }

    private func set(physicallyBasedLightFeatures: PhysicallyBasedLightFeatures) {
        node.light?.type = .directional
        node.light?.intensity = physicallyBasedLightFeatures.lumen
        node.light?.temperature = physicallyBasedLightFeatures.temperature
    }

    private func activateShadow() {
        node.light?.castsShadow = true
        node.light?.orthographicScale = 10        
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

至于基本灯光，我们也为基于物理的特征创建了一个类，它将存储配置，并且必须在构建时注入(正如你可以从前面的 init 类中看到的)，我们称之为`PhysicallyBasedLightFeatures`。

```
class PhysicallyBasedLightFeatures {
    let lumen: CGFloat
    let temperature: CGFloat

    init(lumen: CGFloat, temperature: CGFloat) {
        self.lumen = lumen
        self.temperature = temperature
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

对于基于物理的渲染，我们还需要另一种照明设置来达到最佳效果。我们需要设置`SCNScene`，包含场景中所有`SCNNode`元素的对象，以及`lightingEnviroment`和`background`属性。这些让 SceneKit 更精确地近似间接照明计算。为了设置这个特性，我们创建了一个新的类，`PhysicallyBasedLightingEnviroment`，它将接收要设置的场景。将在`lightingEnviroment.contents`属性上设置一个立方体贴图，并在`lightingEnviroment.intensity`属性上设置它的强度。为了匹配这个灯光设置的结果，它将使用与`lightingEnviroment.contents`属性相同的立方体贴图来设置`background.contents`。

```
class PhysicallyBasedLightingEnviroment {
    let cubeMap: [String]
    let intensity: CGFloat

    init(cubeMap: [String], intensity: CGFloat) {
        self.cubeMap = cubeMap
        self.intensity = intensity
    }

    func setLightingEnviromentFor(scene: SCNScene) {
        scene.lightingEnvironment.contents = cubeMap
        scene.lightingEnvironment.intensity = intensity
        scene.background.contents = cubeMap
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

下一步:相机。我们创建一个`Camera`类，它将再次包含对定义了`SCNCamera`的`SCNNode`的引用。对于摄像机，我们首先需要设置一些几何属性，如位置、旋转和枢轴点，我们将使用它们作为摄像机动画的参考。最后但同样重要的是，我们设置标志`wantHDR`以应用[高动态范围](https://en.wikipedia.org/wiki/High_dynamic_range)后处理来调整场景相对于显示器的总体亮度。

```
class Camera {
    let node: SCNNode

    init(cameraNode: SCNNode, wantsHDR: Bool = false) {
        node = cameraNode
        setAdvancedFeatures(wantsHDR: wantsHDR)
    }

    init(position: SCNVector3, rotation: SCNVector4, wantsHDR: Bool = false, pivot: SCNMatrix4? = nil) {
        node = SCNNode()
        createCameraOnNode()
        setAdvancedFeatures(wantsHDR: wantsHDR)
        set(position: position, rotation: rotation, pivot: pivot)
    }

    private func createCameraOnNode() {
        node.camera = SCNCamera()
    }

    private func setAdvancedFeatures(wantsHDR: Bool) {
        node.camera?.automaticallyAdjustsZRange = true
        node.camera?.wantsHDR = wantsHDR
    }

    private func set(position aPosition: SCNVector3, rotation aRotation: SCNVector4, pivot aPivot: SCNMatrix4?) {
        node.position = aPosition
        node.rotation = aRotation
        node.pivot = aPivot ?? node.pivot
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在是时候考虑我们想要在场景中显示的对象了。出于这个原因，我们创建了一个`Object`类来表示我们想要在场景中显示的每一种对象。显然，对于前面的类来说，`Object`类也将暴露一个类型为`SCNNode`的`node`属性，它代表我们在场景中的对象。我们用多个初始化器来定义这个类，这些初始化器使用各种配置来创建对象实例:init 作为一个空对象，init 使用一个`SCNGeometry`实例，使用一个网格加载作为一个`MDLObject`使用[模型 I\O](https://developer.apple.com/documentation/modelio) 苹果框架。这个框架允许我们以各种常见的可用格式导入/导出 3D 模型。

```
class Object {
    let node: SCNNode

    init(position: SCNVector3, rotation: SCNVector4) {
        node = SCNNode()
        node.castsShadow = true
        set(position: position, rotation: rotation)
    }

    init(geometry: SCNGeometry, position: SCNVector3, rotation: SCNVector4) {
        node = SCNNode(geometry: geometry)
        node.castsShadow = true
        set(position: position, rotation: rotation)
    }

    init(mesh: MDLObject, position: SCNVector3, rotation: SCNVector4) {
        node = SCNNode(mdlObject: mesh)
        node.castsShadow = true
        set(position: position, rotation: rotation)
    }

    private func set(position: SCNVector3, rotation: SCNVector4) {
        node.position = position
        node.rotation = rotation
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们准备定义一个`PhysicallyBasedObject`类，它将继承`Object`类的所有功能，并将设置使用基于物理的渲染来渲染对象所需的所有特性。即使所有的初始化器对这个子类都是可用的，我们在构造时也需要一个网格作为`MDLObject`，因为我们将显示一些特定的网格对象(我们将在后面讨论它们)。在建造时，我们还需要位置和旋转以及`PhysicallyBasedMaterial`材料。通过将它分配给我们节点的`geometry`的`firstMaterial`属性，我们的对象将使用基于物理的 SceneKit 渲染引擎被渲染为基于物理的对象。注意:我们将使用的网格不包含任何材料，因此通过分配`firstMaterial`属性，网格将对整个表面使用它。

```
class PhysicallyBasedObject: Object {

    init(mesh: MDLObject, material: PhysicallyBasedMaterial, position: SCNVector3, rotation: SCNVector4) {
        super.init(mesh: mesh, position: position, rotation: rotation)
        node.geometry?.firstMaterial = material.material
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

所以，下一个问题是:我们如何定义`PhysicallyBasedMaterial`类？我们用类型为`SCNMaterial`的单个属性`material`创建`PhysicallyBasedMaterial`。在此材料属性上，我们设置:

*   `lightingModel`到`.physicallyBased`，为 SceneKit 标记为基于物理的材质
*   `diffuse.contents`具有适当漫射值的属性。
*   `roughness.contents`具有适当粗糙度值的属性。
*   `metalness.contents`具有适当金属度值的属性。
*   `normal.contents`具有适当正常值的属性。
*   `ambientOcclusion.contents`具有适当环境遮挡值的属性

正如你所看到的，我们拥有了我在[基于物理渲染的介绍](https://www.fabrizioduroni.it/2017/12/07/physically-base-rendering-introduction.html)中讨论过的所有属性。我们也有其他的属性来帮助我们提高真实感，特别是间接照明对于[环境遮挡](https://en.wikipedia.org/wiki/Ambient_occlusion)(这个属性/技术与 PBR 无关，但是有助于提高最终的渲染)。哪种价值观接受这种属性？如 Apple 文档中所述，您可以为`contents`属性赋值:

*   一种颜色(`NSColor` / `UIColor` / `CGColor`)
*   一个数字(`NSNumber`)
*   一个图像(`NSImage` / `UIImage` / `CGImage`)
*   一根绳子
*   答`CALayer`
*   一个纹理(`SKTexture` / `MDLTexture` / `MTLTexture` / `GLKTextureInfo`)
*   答`SKScene`
*   一个由六个图像组成的数组，代表一个立方体贴图(就像我们对`lightingEnviroment.contents property`所做的那样)。

```
class PhysicallyBasedMaterial {
    let material: SCNMaterial

    init(diffuse: Any, roughness: Any, metalness: Any, normal: Any, ambientOcclusion: Any? = nil) {
        material = SCNMaterial()
        material.lightingModel = .physicallyBased
        material.diffuse.contents = diffuse
        material.roughness.contents = roughness
        material.metalness.contents = metalness
        material.normal.contents = normal
        material.ambientOcclusion.contents = ambientOcclusion
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在是时候构建我们的场景了😌！！我们首先创建一个新的类`PhysicallyBasedScene`，它是`SCNScene`的子类。通过这种方式，我们可以定制默认的初始化程序，添加我们场景的所有元素，也因为通过这种方式我们可以直接访问`SCNScene`的所有属性。我们还定义了一个协议`Scene`，我们将使用它来管理一些手势和动画场景。所以在初始化器中，我们将调用三个方法:`createCamera()`，我们将创建相机，`createLight()`，我们将创建灯光，`createObjects()`，我们将创建对象。注意:我们还需要用编码器定义初始化器，因为我们正在子类化一个采用`NSSecureCoding`的类，它是`NSCoding`协议的扩展，有这个需要的初始化器。

```
@objc class PhysicallyBasedScene: SCNScene, Scene {
    var camera: Camera!

    override init() {
        super.init()
        createCamera()
        createLight()
        createObjects()
    }

    required init?(coder aDecoder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }

    ...
    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

所以我们从制造相机开始。我们把它放在场景的前面，枢轴移动了一点，HDR 后期处理被激活。

```
private func createCamera() {
    camera = Camera(
        position: SCNVector3Make(0, 2, 0),
        rotation: SCNVector4Make(1, 0, 0, GLKMathDegreesToRadians(-5)),
        wantsHDR: true,
        pivot: SCNMatrix4MakeTranslation(0, 0, -8)
    )
    rootNode.addChildNode(camera.node)
} 
```

Enter fullscreen mode Exit fullscreen mode

然后我们创造我们的光。我们创建了一个基于物理的光源，功率为 100 流明，色温为 4000K。通过这种方式，我们可以匹配场景中设置的照明环境所使用的立方体贴图的暖橙色。

```
private func createLight() {
    rootNode.addChildNode(createPhysicallyBasedLight().node)
    createPhysicallyLightingEnviroment()
}

private func createPhysicallyBasedLight() -> PhysicallyBasedLight {
    let lightFeatures = LightFeatures(
        position: SCNVector3Make(-2, 5, 4),
        orientation: SCNVector3Make(GLKMathDegreesToRadians(-45), GLKMathDegreesToRadians(-25), 0),
        color: UIColor.white
    )
    let physicallyBasedLightFeatures = PhysicallyBasedLightFeatures(lumen: 100, temperature: 4000)
    let physicallyBasedLight = PhysicallyBasedLight(
        lightFeatures: lightFeatures,
        physicallyBasedLightFeatures: physicallyBasedLightFeatures
    )
    return physicallyBasedLight
}

private func createPhysicallyLightingEnviroment() {
    let enviroment = PhysicallyBasedLightingEnviroment(
        cubeMap: ["rightPBR.png", "leftPBR.png", "upPBR.png", "downPBR.png", "backPBR.png", "frontPBR.png"],
        intensity: 1.0
    )
    enviroment.setLightingEnviromentFor(scene: self)
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们可以放置我们的 4 个物体:一个基本的平面网格和 3 个来自斯坦福扫描库的网格。
这些网格是:龙，快乐的佛和露西。所有这些网格将使用`PhysicallyBasedObject`渲染。
我们从 [freepbr](https://freepbr.com) 网站上获取用于制作各种材质模型的纹理。

```
private func createObjects() {
    addFloor()
    addDragon()
    addBuddha()
    addLucy()
}

private func addFloor() {
    let floor = PhysicallyBasedObject(
        mesh: MeshLoader.loadMeshWith(name: "Floor", ofType: "obj"),
        material: PhysicallyBasedMaterial(
            diffuse: "floor-diffuse.png",
            roughness: NSNumber(value: 0.8),
            metalness: "floor-metalness.png",
            normal: "floor-normal.png",
            ambientOcclusion: "floor-ambient-occlusion.png"
        ),
        position: SCNVector3Make(0, 0, 0),
        rotation: SCNVector4Make(0, 0, 0, 0)
    )
    rootNode.addChildNode(floor.node)
}

private func addDragon() {
    let dragon = PhysicallyBasedObject(
        mesh: MeshLoader.loadMeshWith(name: "Dragon", ofType: "obj"),
        material: PhysicallyBasedMaterial(
            diffuse: "rustediron-diffuse.png",
            roughness: NSNumber(value: 0.3),
            metalness: "rustediron-metalness.png",
            normal: "rustediron-normal.png"
        ),
        position: SCNVector3Make(-2, 0, 3),
        rotation: SCNVector4Make(0, 1, 0, GLKMathDegreesToRadians(20))
    )
    rootNode.addChildNode(dragon.node)
}

private func addBuddha() {
    let buddha = PhysicallyBasedObject(
        mesh: MeshLoader.loadMeshWith(name: "HappyBuddha", ofType: "obj"),
        material: PhysicallyBasedMaterial(
            diffuse: "cement-diffuse.png",
            roughness: NSNumber(value: 0.8),
            metalness: "cement-metalness.png",
            normal: "cement-normal.png",
            ambientOcclusion: "cement-ambient-occlusion.png"
        ),
        position: SCNVector3Make(-0.5, 0, 0),
        rotation: SCNVector4Make(0, 0, 0, 0)
    )
    rootNode.addChildNode(buddha.node)
}

private func addLucy() {
    let lucy = PhysicallyBasedObject(
        mesh: MeshLoader.loadMeshWith(name: "Lucy", ofType: "obj"),
        material: PhysicallyBasedMaterial(
            diffuse: "copper-diffuse.png",
            roughness: NSNumber(value: 0.0),
            metalness: "copper-metalness.png",
            normal: "copper-normal.png"
        ),
        position: SCNVector3Make(2, 0, 2),
        rotation: SCNVector4Make(0, 0, 0, 0)
    )
    rootNode.addChildNode(lucy.node)
} 
```

Enter fullscreen mode Exit fullscreen mode

网格被存储为[波前 obj 文件](https://en.wikipedia.org/wiki/Wavefront_.obj_file)(有史以来最简单的文件格式😌).从前面的代码中可以看出，我们使用了一个名为`MeshLoader`的类。它是如何工作的？它使用[模型 I/O](https://developer.apple.com/documentation/modelio) 苹果框架加载 obj 文件作为`MDLAsset`，然后我们提取第一个`MDLObject`。

```
class MeshLoader {

    static func loadMeshWith(name: String, ofType type: String) -> MDLObject {
        let path = Bundle.main.path(forResource: name, ofType: type)!
        let asset = MDLAsset(url: URL(fileURLWithPath: path))
        return asset[0]!
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们几乎准备好渲染我们的场景。最后要做的是实现`Scene`协议的方法来给场景添加一些运动。这个方法将被一个附加到主视图的点击手势调用，这个主视图将渲染我们的场景(我们一会儿就会看到它)。在它里面，我们使用方法`runAction`来围绕它的枢轴旋转相机，我们之前移动过它来拥有一个旋转轴来围绕场景移动相机。

```
func actionForOnefingerGesture(withLocation location: CGPoint, andHitResult hitResult: [Any]!) {
    self.camera.node.runAction(SCNAction.rotate(by: CGFloat(GLKMathDegreesToRadians(360)),
                                                around: SCNVector3Make(0, 1, 0),
                                                duration: 30))
} 
```

Enter fullscreen mode Exit fullscreen mode

我们准备渲染我们的场景。将我们的`PhysicallyBasedScene`的一个实例分配给一个`SCNView`，看看我们工作的漂亮结果。下面你可以找到我们创建的场景的视频。

[https://www.youtube.com/embed/yDMdAtv-3Bg](https://www.youtube.com/embed/yDMdAtv-3Bg)

就是这样！！你成功了！！现在，你可以向你的朋友展示你基于物理的场景，并为此感到自豪😎。你可以在 github 库的[中找到这个例子和其他场景。](https://github.com/chicio/Exploring-SceneKit)

*原载于[https://www . fabrizioduroni . it](https://www.fabrizioduroni.it/2018/01/03/scene-kit-physically-based-rendering.html)2018 年 1 月 3 日。*