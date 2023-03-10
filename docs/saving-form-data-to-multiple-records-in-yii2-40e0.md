# 将表单数据保存到 Yii2 中的多条记录

> 原文：<https://dev.to/plumthedev/saving-form-data-to-multiple-records-in-yii2-40e0>

# 大谜团

上次我用 Yii2 写了一个 CMS，其中用户有自己的个人信息和关系。在 Yii2 中，更新用户资料不成问题，但当我想更新资料和条款关系时，却成了一个谜。

# 简单是关键

所以我创建了代表用户编辑个人表单的 Yii2 模型。
对于构造函数我是通过用户这是[活动记录](https://www.yiiframework.com/doc/api/2.0/yii-db-activerecord)

我想保存所有用户资料的详细信息，删除所有条款关系，并创建新的。

```
class EditPersonalForm extends Model{

    public $user;

    public $name;

    public $events;

    public function __construct(User $user, $config = [])
    {
        parent::__construct($config);

        $this->user = $user;

        // user details
        $this->name = $user->getName();

        // terms details
        $this->events = $user->getEventsList();
    }

    public function save($runValidation = true)
    {
        $userProfileUpdated = $this->updateUserProfile($runValidation);

        $this->updateUserEvents();

        return $userProfileUpdated;
    }

    public function updateUserProfile($runValidation)
    {
        $this->user->scenario = $this->scenario;
        $this->user->load(Yii::$app->request->post(), $this->formName());

        return $this->user->save($runValidation);
    }

    public function updateUserEvents()
    {
        TermRelationship::deleteAllEventsByUserID($this->user->getId());
        $this->createTermRelationships($this->events, 0);
    }

    private function createTermRelationships($termsID, $termType)
    {
        foreach ($termsID as $termID){
            $data = array(
                'termID' => $termID,
                'termType' => $termType,
                'userID' => $this->user->getId()
            );

            (new TermRelationship($data))->save();
        }
    }
} 
```

我通过在`__construct()`中给出属性值来完成表单字段。
在控制器动作中创建了编辑表单模型的新实例，并将其传递给视图。

此外，我要在控制器中做的是通过标准模型`load()`方法从请求中加载数据，并保存编辑表单，从而保存用户详细信息及其与术语的关系。

```
// standard controller action

if ($editForm->load(Yii::$app->request->post()) && $editForm->save()) {
    //success message

    return $this->refresh();
} 
```

这就够了，问候梅子！