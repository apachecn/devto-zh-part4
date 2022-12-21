# 在 Unity 中制作自定义检查器来处理属性

> 原文：<https://dev.to/emanuelvintila/making-a-custom-inspector-in-unity-to-handle-properties-lkj>

Unity 的内置检查器只允许我们修改字段。在本文中，我们将看到如何扩展检查器来显示属性。

为了创建一个自定义的检查器，我们必须从`UnityEditor.Editor`派生。这是类声明以及我们将使用的属性。

```
public class MyEditor : Editor
{
    protected Type InspectedType { get; set; }
    protected object InspectedObject { get; set; }
    protected List<PropertyInfo> Properties { get; set; }
    protected List<FieldInfo> Fields { get; set; }
} 
```

首先，在检查器的`OnEnable`方法中，让我们收集被检查对象的可设置字段和属性。

```
protected virtual void OnEnable()
{
    InspectedObject = serializedObject.targetObject;
    InspectedType = InspectedObject.GetType();

    Properties = InspectedType
        .GetProperties(BindingFlags.Public | BindingFlags.Instance)
        .Where(property => property.DeclaringType == InspectedType)
        .Where(property => (property.SetMethod?.IsPublic).GetValueOrDefault())
        .ToList();

    Fields = InspectedType
        .GetFields(BindingFlags.Public | BindingFlags.Instance)
        .Concat(InspectedType
                .GetFields(BindingFlags.NonPublic | BindingFlags.Instance)
                .Where(field => field.GetCustomAttribute<SerializeField>() != null)
               )
        .Where(field => field.IsInitOnly == false)
        .ToList();
} 
```

现在，我们需要一个方法来为特定类型创建一个带有标签和值的字段。

```
protected virtual object MakeFieldForType(Type type, string label, object value)
{
    T F<T>(Func<string, T, GUILayoutOption[], T> fn)
    {
        return fn(label, (T) value, null);
    }

    if (type == typeof(bool))
        return F<bool>(EditorGUILayout.Toggle);
    if (type == typeof(int))
        return F<int>(EditorGUILayout.IntField);
    if (type == typeof(long))
        return F<long>(EditorGUILayout.LongField);
    if (type == typeof(float))
        return F<float>(EditorGUILayout.FloatField);
    if (type == typeof(double))
        return F<double>(EditorGUILayout.DoubleField);
    if (type == typeof(string))
        return F<string>(EditorGUILayout.TextField);

    throw new ArgumentException(nameof(type));
} 
```

最后，让我们重写`OnInspectorGUI`方法；它使用上面定义的`MakeFieldForType`方法。

```
public override void OnInspectorGUI()
{
    EditorGUILayout.LabelField("Properties");
    foreach (PropertyInfo property in Properties)
    {
        string label = property.Name;
        object value = property.GetValue(InspectedObject);
        property.SetValue(InspectedObject, MakeFieldForType(property.PropertyType, label, value));
    }

    EditorGUILayout.Separator();

    EditorGUILayout.LabelField("Fields");
    foreach (FieldInfo field in Fields)
    {
        string label = field.Name;
        object value = field.GetValue(InspectedObject);
        field.SetValue(InspectedObject, MakeFieldForType(field.FieldType, label, value));
    }
} 
```

显然，这是一个基本的例子，你必须在`MakeFieldForType`方法中处理更多的类型。

这种方法有缺点。`SerializedObject`的撤销功能丢失。多重编辑支持也丢失了，但是可以很容易地添加回来(留下您对此的解决方案的评论😉).

为了测试我们新的自定义检查器，我们需要从中派生出一个类，并用`CustomEditor`属性对其进行注释，这告诉 Unity 它应该使用相应类型的编辑器。

```
using System.Collections.Generic;
using UnityEditor;
using UnityEngine;

public class ExampleClass : MonoBehaviour
{ 
    public float Prop { get; set; }
    public float PropPrivateSet { get; private set; }
    public float PropReadOnly { get; }
    public float PropComputed => field * 2;

    public float field;
    public readonly float readonlyField = 5;
    [SerializeField]
    private float privateField;
}

[CustomEditor(typeof(ExampleClass))]
public class ExampleClassEditor : MyEditor { } 
```

内置检查器更强大为什么还要这样做？因为内置的检查器不支持属性，而属性是封装中至关重要的一部分。您永远不会希望向公众公开字段，而是将它们包装在属性中。甚至自动实现的属性也比字段好得多。

想象一下，有一天你决定在设置一个特定的字段时要实现一些其他的逻辑。您必须将它包装在一个属性中(C#方式)，或者为它编写 getter 和 setter 方法(这或多或少相当于编写一个属性)。这样做意味着打破现有代码中已经存在的字段用法。

总之:在公共接口中总是使用属性而不是字段！