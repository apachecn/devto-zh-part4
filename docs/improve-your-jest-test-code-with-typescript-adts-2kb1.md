# 使用 Typescript ADTs 改进 Jest 测试代码

> 原文：<https://dev.to/krumpet/improve-your-jest-test-code-with-typescript-adts-2kb1>

Jest 是一个在 JavaScript 项目中广泛使用的 JavaScript 测试框架。我个人在用 TypeScript 编写的 Angular 项目中使用它。它使得为单元测试创建服务的模拟变得容易，并且测试本身易于阅读、理解和在必要时扩展。

当一个组件改变时，它的测试也应该改变，以检查新实现的正确性。然而，在没有任何测试失败或给出任何警告的情况下，改变组件或注入的服务是可能的。这些情况中的一些以及 TypeScript 类型帮助最小化它们发生的方式是本文的主题。

让我们以这个`TestService`测试文件为例:

```
describe('TestService', () => {
    let authenticationServiceMock;
    let SUT: TestService;

    beforeEach(() => {
        const authorizationSources = ['system', 'override', 'automation'];

        authenticationServiceMock = {
            getAuthSources: jest.fn(() => authorizationSources),
            isSourceAuthorized: jest.fn((sourceCandidate: string) => authorizationSources.includes(sourceCandidate)),
            login: jest.fn((username: string, password: string) => of(username === 'admin' && password === '123')),
        };

        TestBed.configureTestingModule({
            providers: [
                { provide: AuthenticationService, useValue: authenticationServiceMock },
            ]
        });
        SUT = TestBed.get(TestService);
    });

    test('should be created', () => {
        expect(SUT).toBeTruthy();
    });

    test('can login', () => {
        const user = 'wrong';
        const pass = 'wrongpass';
        SUT.login(user, pass).subscribe(
            result => {
                expect(result).toBe(false);
            }
        );
        expect(authenticationServiceMock.login as jest.Mock).toHaveBeenCalledTimes(1);
        expect((authenticationServiceMock.login as jest.Mock).mock.calls[0][0]).toBe(user);
    });
}); 
```

这里有几个地方可以改进，以避免每次在测试服务中有所改变时，花费时间在微小的细节上改变测试文件。它也可以变得更加类型安全。

*   AuthenticationService 模拟没有类型，因此对 AuthenticationService 的任何更改都会导致该测试在不应该通过的时候继续通过。它也可能失败，即使 TestService 也会随着它的依赖关系而改变，但是测试会失败，这也是由于 AuthenticationService 的过时模拟实现
*   如果我们给 AuthenticationService 一个类型，我们仍然需要将它的函数转换为`jest.Mock`来使用类似`toHaveBeenCalledTimes`的 jasmine 匹配器，或者访问 jest mockInstance `mock`属性来检查函数调用中的参数。
*   当使用`mock.calls`数组时，它只是一个`any[][]`类型，如果我们想获得`login`方法参数的实际类型，我们必须将其转换为显式的冗长的模拟类型，如下所示:

```
 expect((authenticationServiceMock.login as jest.Mock<Observable<boolean>, [string, string]>).mock.calls[0][0]).toBe(user); 
```

*   即使使用这种语法，对`authenticationService`或`login`签名的任何更改都需要我们手动修复所有这些类型转换，甚至不清楚`jest.Mock`类型转换是否是问题所在。想象一下`login`曾经将`[string, number]`作为输入，现在我们将其重构为`[string, string]`。我们会得到一个非常冗长的错误消息，从中很难看出我们只需要将第二个参数的类型切换到`string`。

我们能做的最基本的事情，就是告诉编译器我们的 mock 是类型`AuthenticationService`，但是它的所有方法也是类型`jest.Mock`。为此，我们首先需要从`AuthenticationService`中提取所有的方法名，然后创建一个`Record`类型，其中键是方法名，值都是`jest.Mock` :

```
type FunctionPropertyNames<T> = { [K in keyof T]: T[K] extends (...args: any[]) => any ? K : never }[keyof T]; 
```

这个类型别名使用[映射类型](https://www.typescriptlang.org/docs/handbook/advanced-types.html#mapped-types)和[索引类型](https://www.typescriptlang.org/docs/handbook/advanced-types.html#index-types)来创建一个类型，该类型是来自类型`T`的属性名称的并集。在我们的例子中，`FunctionPropertyNames<AuthenticationService>`就是`"login" | "getAuthSources" | "isSourceAuthorized"`的意思。因此，我们的模拟服务类型别名将是:

```
type MockService<aService> = aService & Record<FunctionPropertyNames<aService>, jest.Mock>;

let authenticationServiceMock: MockService<AuthenticationService>; 
```

现在我们可以在任何需要原始服务的地方使用我们的 mock(因为它有原始的服务类型)，但是每当我们访问它的一个属性时，如果它是一个函数，它就会有附加的类型`jest.Mock`。比如:

```
expect(authenticationServiceMock.login).toHaveBeenCalledTimes(1);
expect((authenticationServiceMock.login).mock.calls[0][0]).toBe(user); 
```

无论何时我们想做什么，都不会再有尴尬的选角！

请注意，`mock`对象仍然使用`<any, any>`类型签名，因为我们没有说明每个函数的返回类型和参数应该是什么。要做到这一点，我们需要直接映射到原始服务类型上(再次使用映射类型)，这样我们就可以告诉每个函数属性是正确类型的模拟:

```
type BetterMockService<aService> = aService &
    { [K in keyof aService]: aService[K] extends (...args: infer A) => infer B ?
        aService[K] & jest.Mock<B, A> : aService[K] }; 
```

现在我们正在创建一个类型，它具有与`aService`相同的所有属性，但是对于每个作为函数的属性，它具有额外的类型`jest.Mock<B, A>`，其中`B`是它的返回类型，`A`是它的参数类型的元组。我们不需要在交集中包含`aService`,因为新的映射类型已经拥有了原始类型的所有属性，但是我在这里保留了它，以显示与之前解决方案的相似性。

希望这个想法或者它的改编可以在你的单元测试中输入模拟时帮助你。让我知道你使用的其他打字技巧。