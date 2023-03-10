# Web Atoms 中简单和复杂的数据验证

> 原文：<https://dev.to/web-atoms/simple-and-complex-data-validation-in-web-atoms-33om>

用户界面中的数据验证对每个应用程序来说都是至关重要的，而 Web Atoms 使得它非常容易编写。

## 简单验证

验证访问器用`@Validate` decorator 修饰，并以单词`error`为前缀。您可以在 UI 中绑定这些访问器来显示错误。

例如，

```
 export default SignupViewModel extends AtomViewModel {

    @Inject
    public navigationService: NavigationService;

    public model = {
        firstName: null,
        lastName: null
    };

    // both validate properties will return undefined value
    // unless `this.isValid` is referenced.

    @Validate
    public get errorFirstName(): string {
        return this.model.firstName ? "" : "First name is required";
    }

    @Validate
    public get errorLastName(): string {
        return this.model.firstName ? "" : "Last name is required";
    }

    public signup(): Promise<void> {

        // as soon as this property is called first time
        // validation decorator will update and error will be displayed
        if (!this.isValid) {
            await this.navigationService.alert(`Please enter required fields`);
            return;
        }

        // optional, if you want to reuse same form
        // you can call resetValidations to remove all errors
        this.resetValidations();
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

网络版 TSX

```
class Component extends AtomControl {

   public viewModel: SignupViewModel;

   public create() {
       this.viewModel = this.resolve(SignupViewModel);
       this.render(
<div>
    <input 
        placeholder="First name:"
        value={Bind.twoWays(() => this.viewModel.model.firstName)}/>
    <span
        class="error"
        text={Bind.oneWay(()) => this.viewModel.errorFirstName}/> 
    <input 
        placeholder="Last name:"
        value={Bind.twoWays(() => this.viewModel.model.lastName)}/>
    <span
        class="error"
        text={Bind.oneWay(()) => this.viewModel.errorLastName}/> 
    ...

    <button
        eventClick={ () => this.viewModel.signup() }>Signup</button> 
</div>
       );
   }

} 
```

Enter fullscreen mode Exit fullscreen mode

Xaml 的 TSX

```
class Component extends AtomControl {

   public viewModel: SignupViewModel;

   public create() {
       this.viewModel = this.resolve(SignupViewModel);
       this.render(
<XF.StackLayout>
    <XF.Entry
        placeholder="First name:"
        text={Bind.twoWays(() => this.viewModel.model.firstName)}/>
    <XF.Label
        class="error"
        text={Bind.oneWay(()) => this.viewModel.errorFirstName}/> 
    <XF.Entry 
        placeholder="Last name:"
        text={Bind.twoWays(() => this.viewModel.model.lastName)}/>
    <XF.Label
        class="error"
        text={Bind.oneWay(()) => this.viewModel.errorLastName}/> 
    ...

    <XF.Button
        command={ () => this.viewModel.signup() }
        text="Signup"/>

</XF.StackLayout>
       );
   }

} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，当页面被加载时，错误跨度不会显示任何内容。即使`firstName`和`lastName`都是空的。一旦用户点击`Signup`按钮，`this.isValid` get 方法将开始观察所有`@Validate`装饰器方法的变化，用户界面将开始显示错误消息。

## 多视图模型验证

较大的 UI 将需要多个较小的 UI 组件，在 Web Atoms 中，您可以轻松地创建一个带有视图模型的 UI，该视图模型引用父视图模型，父视图模型的验证扩展到子视图，即使子视图模型无效，它也会为`isValid`返回 false。

### 根保险查看

```
interface IInsurance  {
    id?: number;
    date?: Date;
    broker: string;
    type: string;
    applicants: IApplicant[];
}

export interface IApplicant {
    name: string;
    type: string;
    address?: string;
    city?: string;
}

export default class InsuranceViewModel extends AtomViewModel {

    @Inject
    public navigationService: NavigationService;

    public model: IInsurance = {
        broker: "",
        type: "General",
        applicants: [
            {
                name: "",
                type: "Primary"
            }
        ]
    };

    @Validate
    public get errorBroker(): string {
        return this.model.broker ? "" : "Broker cannot be empty";
    }

    public addApplicant(): void {
        this.model.applicants.add({
            name: "",
            type: "Dependent"
        });
    }

    public async save(): Promise<void> {
        if (!this.isValid) {
            await this.navigationService.alert("Please fix all errors", "Error");
            return;
        }
        await this.navigationService.alert("Save Successful", "Success");
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

#### Insurance.html

我们在保险表格中显示申请人列表，我们可以添加更多的申请人，注意，每个申请人的验证将根据申请人的类型而不同。

```
export default class Insurance extends AtomControl {

   public create(): void {
      this.viewModel =  this.resolve(InsuranceViewModel) ;

      this.render(
      <div>
         <div>
            <input
               placeholder="Name"
               value={Bind.twoWays((x) => x.viewModel.model.broker)}>
            </input>
            <span
               style="color: red"
               text={Bind.oneWay((x) => x.viewModel.errorBroker)}>
            </span>
         </div>
         <AtomItemsControl
            items={Bind.oneTime((x) => x.viewModel.model.applicants)}>
            <AtomItemsControl.itemTemplate>
               <Applicant>
               </Applicant>
            </AtomItemsControl.itemTemplate>
         </AtomItemsControl>
         <button
            eventClick={Bind.event((x) => (x.viewModel).addApplicant())}>
            Add Applicant
         </button>
         <div>Other fields...</div>
         <button
            eventClick={Bind.event((x) => (x.viewModel).save())}>
            Save
         </button>
      </div>
      );
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 嵌套申请人视图

打字稿

```
export default class ApplicantViewModel extends AtomViewModel {

    @Inject
    public navigationService: NavigationService;

    public model: IApplicant;

    @Validate
    public get errorName(): string {
        return this.model.name ? "" : "Name cannot be empty";
    }

    @Validate
    public get errorAddress(): string {
        return this.model.address ? "" : "Address cannot be empty";
    }

    public async delete(): Promise<void> {
        if (!( await this.navigationService.confirm("Are you sure you want to delete this?") )) {
            return;
        }
        (this.parent as InsuranceViewModel).model.applicants.remove(this.model);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

#### Applicant.html

申请人视图是一个独立的视图，有自己的视图模型，没有父列表也可以使用。

```
export default class Applicant extends AtomControl {

   public create(): void {

       /** Following method will initialize and bind parent property of
        * ApplicantViewModel to InsuranceViewModel, this is specified in the form
        * of lambda so it will bind correctly after the control has been created
        * successfully.
        *
        * After parent is attached, parent view model will include all children validations
        * and will fail to validate if any of child is invalid
        */
      this.viewModel =  this.resolve(ApplicantViewModel, () => ({ model: this.data, parent: this.parent.viewModel })) ;

      this.render(
      <div
         style="margin: 5px; padding: 5px; border: solid 1px lightgray; border-radius: 5px">
         <div>
            <input
               placeholder="Name"
               value={Bind.twoWays((x) => x.viewModel.model.name)}>
            </input>
            <span
               style="color: red"
               text={Bind.oneWay((x) => x.viewModel.errorName)}>
            </span>
         </div>
         <div>
            <input
               placeholder="Address"
               value={Bind.twoWays((x) => x.viewModel.model.address)}>
            </input>
            <span
               style="color: red"
               text={Bind.oneWay((x) => x.viewModel.errorAddress)}>
            </span>
         </div>
         <button
            eventClick={Bind.event((x) => (x.viewModel).delete())}>
            Delete
         </button>
      </div>
      );
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

> 创建子视图时，我们将父视图指定为可视父视图模型。所以每当这个子视图无效时，甚至父视图也将无效。