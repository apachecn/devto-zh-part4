# 我如何写微服务(第二部分)

> 原文：<https://dev.to/zenithar/how-i-write-micro-service-part-2-1aj9>

我们将根据洁净和六边形建筑原则准备一个 Golang 项目。

> TL；Github 上的 DR 代码库-<[https://github.com/Zenithar/go-spotigraph](https://github.com/Zenithar/go-spotigraph)

# 域

为了操作概念，我们需要实现它们。还是用`Spotify agile terminology`吧。

*   `User`是系统标识
    *   `User`有一个`id`
    *   `User`有一个`principal`来描述用户`private`的身份(电子邮件、LDAP dn 等。)
*   `Squad`是一群`User`一起开发产品
    *   `Squad`有一个`id`
    *   `Squad`有一个`label`
    *   `Squad`有一个`Product Owner`，被一个`User:id`引用
*   `Guild`根据亲缘关系是一组`User`
    *   `Guild`有一个`id`
    *   `Guild`有一个`label`
    *   `Guild`有`members`，集合了`User:id`
*   `Tribe`是一群从事共同项目的`Squad`
    *   `Tribe`有一个`id`
    *   `Tribe`有一个`label`
    *   `Tribe`有`members`，集合了`User:id`
*   `Chapter`是根据技能组合的一组`User`
    *   `Chapter`有一个`id`
    *   `Chapter`有一个`label`
    *   `Chapter`有`members`，集合了`User:id`
    *   `Chapter`有一个`Chapter Leader`，被一个`User:id`引用

# 核心服务

> 从这里开始，我假设你知道如何与戈朗合作-[https://tour.golang.org](https://tour.golang.org)

首先，我创建一个用于创建完整项目的`internal`包，它将在专用的可运行工件中执行。

```
$ mkdir spotigraph
$ go mod init go.zenithar.org/spotigraph
$ mkdir internal 
```

> 从项目包外部无法访问包。

```
 + internal
   + helpers
     - id.go
     - password.go
     - time.go
   + models
     - user.go 
```

## 帮手

帮助器应该包含`models`的附加功能，例如:

*   密码编码、验证和策略升级(算法弃用)
*   ID 生成和验证
*   时间函数间接用于时间驱动测试

```
$ mkdir internal/helpers 
```

### 随机 ID 生成

例如，`id.go`应该包含所有用于生成和验证 ID 语法的逻辑。

```
package helpers

import (
    "github.com/dchest/uniuri"
    validation "github.com/go-ozzo/ozzo-validation"
    "github.com/go-ozzo/ozzo-validation/is"
)

// IDGeneratedLength defines the length of the id string
const IDGeneratedLength = 32

// IDGeneratorFunc returns a randomly generated string useable as identifier
var IDGeneratorFunc = func() string {
    return uniuri.NewLen(IDGeneratedLength)
}

// IDValidationRules describes identifier contract for syntaxic validation
var IDValidationRules = []validation.Rule{
    validation.Required,
    validation.Length(IDGeneratedLength, IDGeneratedLength),
    is.Alphanumeric,
} 
```

> 我喜欢使用`ozzo-validation`,因为创建可组合的验证检查很容易

### 分布式顺序号生成

在分布式环境中，序列号确实很难，如果真的想从计数器中得到一个序列号，你只有两种方法:

*   真实的唯一来源(同一数据库中的序列)
*   使用*索尼* / *大*/*雪*片算法之一

> 我使用 bigflake 是因为它可以用于 1024 个以上的并发生成器，用 Base62 编码的结果应该很短。

你应该去看看我这边的项目

### 主哈希法

另一个关于`principal`处理的例子，你不需要以纯文本的形式存储它，想想隐私，数据库泄露，没有人是完美的...所以您可以在存储之前散列`principal`。散列`principal`并不是数据库的角色，这是您的隐私合规性需求的一部分。

> 我们不必存储真实的`principal`，只需用它的 hash 作为证明。哈希将在用户登录
> 时计算并存储。如果，不，当数据库将泄漏在互联网上，它应该更难扭转从数据库记录(PrincipalHash)本金
> ，由于我们使用一个额外的盐(排序)使用服务器范围。

```
package helpers

import (
    "encoding/base64"

    "golang.org/x/crypto/blake2b"
)

// Default hash key for principal hash function, must be different in each environment.
var principalHashKey = []byte(`7EcP%Sm5=Wgoce5Sb"%[E.<&xG8t5soYU$CzdIMTgK@^4i(Zo|)LoDB'!g"R2]8$`)

// PrincipalHashFunc return the principal hashed using Blake2b keyed algorithm
var PrincipalHashFunc = func(principal string) string {
    // Prepare hasher
    hasher, err:= blake2b.New512(principalHashKey)
    if err!= nil {
        panic(err)
    }

    // Append principal
    _, err = hasher.Write([]byte(principal))
    if err!= nil {
        panic(err)
    }

    // Return base64 hash value of the principal hash
    return base64.RawStdEncoding.EncodeToString(hasher.Sum(nil))
}

// SetPrincipalHashKey used to set the key of hash function
func SetPrincipalHashKey(key []byte) {
    if len(key)!= 64 {
        panic("Principal hash key length must be 64bytes long.")
    }

    principalHashKey = key
} 
```

> `principalHashKey`是默认行为的硬编码，您必须调用导出函数`SetPrincipalHashKey`来更新散列键。
> 
> 不要使用 simple hash simple(没有 key)因为在这种情况下，秘密只是基于你使用的哈希算法！

在特殊情况下，当你需要本金的真实价值时，你必须首先考虑“我真的需要它吗？”或者“我真的会用吗？”。
例如，您需要它来查找表中的用户，认为“没有哈希就足够了”，如果主体是电子邮件，并且您决定
使用主体列作为电子邮件存储，您将无法从哈希中恢复实际值。所以你唯一能做的就是
加密邮件但不在同一个表中，也许不是由同一个微服务处理。

您应该将包含所有系统信息(主体散列、密码散列、日期)的`Account`模型和`Profile`模型分割在一个专用的
中。因为你还必须在`Profile`中引入同意，将用法与授权联系起来。也许`Attribute Based Encryption`
应该把工作做好，但今天还挺年轻的。

对于每个服务来说，`principalHashKey`应该是不同的，但是对于需要一起工作的服务来说是相同的，如果你使用散列值一个
分组键的话。如果对外部函数调用使用真正的主体，可以使用不同的`key`。

> 这就像贝宝对 OIDC 的扩展。真实身份只由调用者持有，每个消费者
> 服务都有自己的内部标识符，以防止数据库交叉验证。但有时这是一个必需的特性。

### 时间间接

为了在测试期间设置时间，您可以通过声明一个别名来间接使用`time.Now`函数，并在任何地方使用它。

```
package helpers

// TimeFunc is used for time based tests
var TimeFunc = time.Now 
```

> 对于更复杂的时钟模拟，我建议你考虑[github.com/jonboulle/clockwork](//github.com/jonboulle/clockwork)。

### 密码

当你决定储存它时，必须小心处理。

> 关于我的密码编码器的更多信息命名为[屠夫](https://github.com/Zenithar/go-butcher)

密码存储的主要问题不仅是你用来散列它的秘密和神秘的方法
，而且还有处理散列所需的时间考虑和 cpu 工作，以防止时间攻击
密码恢复。

在软件的生命周期中，密码编码策略也可能会及时改变，`algoritm deprecation`、
、`higher security requirements`。在开始在数据库中存储密码之前，您必须考虑这一点。
我让库来处理密码存储，并让库能够临时使用
的“旧”密码策略编码来验证密码，然后用新策略生成哈希。

> 事实上，在密码编码
> 策略改变的情况下，您不必要求所有用户更改他们的密码。

像我一样，你也可以使用服务器范围的密钥来加密散列结果，使用 Enveloppe 加密
方法，这将减少恢复密码的机会。因为在字符串
中有一个头，可以帮助你知道使用哪种算法和设置来编码。

对于`ultimate paranoids`，和我一样，你把基于密码的派生功能放在一个专用的分布式微服务中，显然带有
服务认证和传输加密，只有这个服务才会知道明文中的胡椒和盐。
这也有助于你在服务实例之间分配负载。

> 使用`cleartext`密码后，可以分配和擦除内存- [安全软件飞地，用于在内存中存储敏感信息。](https://github.com/awnumar/memguard)T3】

```
package helpers

import (
    "context"
    "fmt"
    "sync"

    "go.zenithar.org/pkg/log"
    "go.zenithar.org/butcher"
    "go.zenithar.org/butcher/hasher"
    "github.com/trustelem/zxcvbn"
)

var (
    once sync.Once

    // butcher hasher instance
    butch *butcher.Butcher

    // PasswordPepper is added to password derivation during hash in order to
    // prevent salt/password bruteforce
    // Do not change this!! It will makes all stored password unrecoverable
    pepperSeed = []byte("TuxV%AqKB0|gxjEB!vc~]T8Hf[q|xgS('3S<IEnqOv:jF&F8}+pur)N@DHYulF#")
)

const (
    // PasswordQualityScoreThreshold is the password quality threshold
    PasswordQualityScoreThreshold = 3
)

// Initialize butcher hasher
func init() {
    once.Do(func() {
        var err error
        if butch, err = butcher.New(
            butcher.WithAlgorithm(hasher.ScryptBlake2b512),
            butcher.WithPepper(pepperSeed),
        ); err!= nil {
            log.Bg().Fatal("Unable to initialize butcher hasher")
        }
    })
}

// SetPasswordPepperSeed used to set the peppering parameter for butcher
func SetPasswordPepperSeed(key []byte) {
    if len(key)!= 64 {
        panic("Password peppering seed length must be 64bytes long.")
    }

    pepperSeed = key
}

// PasswordHasherFunc is the function used for password hashing
var PasswordHasherFunc = func(password string) (string, error) {
    return butcher.Hash([]byte(password))
}

// PasswordVerifierFunc is the function used to check password hash besides the cleartext one
var PasswordVerifierFunc = func(hash string, cleartext string) (bool, error) {
    return butcher.Verify([]byte(hash), []byte(cleartext))
}

// CheckPasswordPolicyFunc is the function used to check password storage policy and
// call the assigner function to set the new encoded password using the updated 
// password storage policy, if applicable.
var CheckPasswordPolicyFunc = func(hash string, cleartext string, assigner func(string) error) (bool, error) {
    if butcher.NeedsUpgrade([]byte(hash)) {
        if err:= assigner(cleartext); err!= nil {
            return false, err
        }
    }
    return true, nil
}

// CheckPasswordQualityFunc is the function used to check password quality regarding security constraints
var CheckPasswordQualityFunc = func(cleartext string) error {
    quality:= zxcvbn.PasswordStrength(cleartext, nil)
    if quality.Score < PasswordQualityScoreThreshold {
        return fmt.Errorf("Password quality insufficient, try to complexify it (score: %d/%d)", quality.Score, PasswordQualityScoreThreshold)
    }
    return nil
} 
```

> 我使用`zxvbn`作为密码强度评估器([github.com/trustelem/zxcvbn](//github.com/trustelem/zxcvbn)

### 型号

```
$ mkdir internal/models 
```

例如`user.go`定义如下:

```
package models

import (
    "fmt"
    "time"

    "go.zenithar.org/spotigraph/internal/helpers"

    validation "github.com/go-ozzo/ozzo-validation"
    "github.com/go-ozzo/ozzo-validation/is"
)

// User describes user attributes holder
type User struct {
    // Internal identifier generated with custom strategy
    ID                 string    `bson:"user_id" db:"id"`
    // Principal must be a valid external principal value (Email, LDAP DN, etc.)
    Principal          string    `bson:"principal" db:"principal"`
    // Create date of the current user
    Created            time.Time `bson:"created" db:"created"`
    // PasswordModifiedAt is the date when the secret was modified
    PasswordModifiedAt time.Time `bson:"password_modified_at" db:"password_modified_at"`
    // Secret is the password cryptographic hash
    Secret             string    `bson:"secret" db:"secret"`
}

// NewUser returns an user instance
func NewUser(principal string) *User {
    return &User{
        // Generate an identity from ID helper
        ID:        helpers.IDGeneratorFunc(),
        // Hash the given principal using helper
        Principal: helpers.PrincipalHashFunc(principal),
        // Set the creation date using the time function helper
        Created:   helpers.TimeFunc(),
    }
} 
```

#### 密码管理

> 密码不是特定于持久性适配器的，它是`User`模型的一个实体属性。

```
// SetPassword updates the password hash of the given account
func (u *User) SetPassword(password string) (err error) {
    // Check password quality
    err = helpers.CheckPasswordQualityFunc(password)
    if err!= nil {
        return err
    }

    // Generate password hash
    u.Secret, err = helpers.PasswordHasherFunc(password)
    if err!= nil {
        return err
    }

    // Update last modified date
    u.PasswordModifiedAt = helpers.TimeFunc()

    // No error
    return nil
}

// VerifyPassword checks if given password matches the hash
func (u *User) VerifyPassword(password string) (bool, error) {
    // Validate password hash using constant time comparison
    valid, err:= helpers.PasswordVerifierFunc(u.Secret, password)
    if!valid || err!= nil {
        return false, err
    }

    // Check if password need upgrades
    return helpers.CheckPasswordPolicyFunc(u.PasswordHash, password, func(pwd string) error {
        return u.SetPassword(pwd)
    })
} 
```

*   `SetPassword`用于使用密码助手更新密码散列，根据密码复杂性策略评估给定密码，使用`scrypt-blake2b-512`算法进行散列。
*   `VerifyPassword`用于用本地编码密码验证给定的明文密码。如果自密码存储以来密码编码策略发生了变化，则使用最后的密码编码策略验证密码，如果密码匹配，则更新为最新的密码编码策略。如果编码的密码被修改，`User:passwordHash`将使用回调进行更新。

> 如果您希望能够更新密码编码策略而不要求每个人都更改他们的密码，则密码编码更新策略是强制性的。

#### 验证

对于`User`验证，让我们使用`ozzo-validation`实现一个`Validate() error`函数。

```
// Validate entity contraints
func (u *User) Validate() error {
    return validation.ValidateStruct(u,
        // User must have a valid id
        validation.Field(&u.ID, helpers.IDValidationRules...),
        // User must have a principal with valid printable ASCII characters as value
        validation.Field(&u.Principal, validation.Required, is.PrintableASCII),
    )
} 
```

> 在创建和更新请求时，将从持久性适配器中调用此方法

通过在模型中添加逻辑，您必须添加单元测试来检查规范的符合性。

```
package models_test

import (
    "testing"

    . "github.com/onsi/gomega"

    "go.zenithar.org/spotigraph/internal/models"
)

func TestUserValidation(t *testing.T) {

    // Prepare the testscase list
    tcl := []struct {
        name      string
        principal string
        expectErr bool
    }{
        // Failed testcases
        {"blank principal", "", true},
        {"1234", "1234", true},

        // Valid testcases
        {"valid email", "toto@foo.com", false},
    }

    for _, tc := range tcl {
        t.Run(tc.name, func(t *testing.T) {
            // Flag test to be run in parallel
            t.Parallel()

            // Matcher library
            g:= NewGomegaWithT(t)

            // Initialize object to be tested
            underTest:= models.NewUser(f.principal)
            g.Expect(underTest).ToNot(BeNil(), "Entity should not be nil")

            // Function to test
            if err := underTest.Validate(); err!= nil {
                // Error occurs, check if not expected
                if !f.expectErr {
                    t.Errorf("Validation error should not be raised, %v raised", err)
                }
            } else {
                // No error, check if expected
                if f.expectErr {
                    t.Error("Validation error should be raised")
                }
            }
        })
    }
} 
```

> 注意，表格驱动测试模式对于从测试用例中分离测试数据非常有用([https://Dave . Cheney . net/2019/05/07/prefere-table-driven-tests](https://dave.cheney.net/2019/05/07/prefer-table-driven-tests))。

# 结论

此时，您应该已经定义了所有的域实体，您的项目将要处理的内容。
现在还不要考虑“我如何将它存储在我的数据库中”,因为数据库模型可能与实体不同，所以首先将您的
域创建为普通对象树，然后应用存储库模式。

在下一篇文章中，我们将看到我如何使用`models`实现`persistence adapters`。

## 参考文献

*   [Github Spotigraph](https://github.com/Zenithar/go-spotigraph)
*   [用围棋嘲讽时间](https://medium.com/agrea-technogies/mocking-time-with-go-a89e66553e79)
*   [在大规模分布式环境中生成唯一 id](https://www.callicoder.com/distributed-unique-id-sequence-number-generator/)
*   [安全的软件飞地，用于在内存中存储敏感信息。](https://github.com/awnumar/memguard)
*   [Dropbox 密码复杂性评估工具](//github.com/trustelem/zxcvbn)