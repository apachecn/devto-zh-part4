# 如何在 Swift 中制作简单的表单验证器

> 原文：<https://dev.to/onmyway133/how-to-make-simple-form-validator-in-swift-1hlg>

有时，我们希望验证包含许多字段的表单，例如姓名、电话、电子邮件以及不同的规则。如果验证失败，我们会显示错误消息。

我们可以制作简单的`Validator`和`Rule`

```
class Validator {
    func validate(text: String, with rules: [Rule]) -> String? {
        return rules.compactMap({ $0.check(text) }).first
    }

    func validate(input: InputView, with rules: [Rule]) {
        guard let message = validate(text: input.textField.text ?? "", with: rules) else {
            input.messageLabel.isHidden = true
            return
        }

        input.messageLabel.isHidden = false
        input.messageLabel.text = message
    }
}

struct Rule {
    // Return nil if matches, error message otherwise
    let check: (String) -> String?

    static let notEmpty = Rule(check: {
        return $0.isEmpty ? "Must not be empty" : nil
    })

    static let validEmail = Rule(check: {
        let regex = #"[A-Z0-9a-z._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,64}"#

        let predicate = NSPredicate(format: "SELF MATCHES %@", regex)
        return predicate.evaluate(with: $0) ? nil : "Must have valid email"
    })

    static let countryCode = Rule(check: {
        let regex = #"^\+\d+.*"#

        let predicate = NSPredicate(format: "SELF MATCHES %@", regex)
        return predicate.evaluate(with: $0) ? nil : "Must have prefix country code"
    })
} 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以非常有表现力地使用

```
let validator = Validator()
validator.validate(input: inputView, with: [.notEmpty, .validEmail]) 
```

Enter fullscreen mode Exit fullscreen mode

然后进行一些测试以确保它能正常工作

```
class ValidatorTests: XCTestCase {
    let validator = Validator()

    func testEmpty() {
        XCTAssertNil(validator.validate(text: "a", with: [.notEmpty]))
        XCTAssertNotNil(validator.validate(text: "", with: [.notEmpty]))
    }

    func testEmail() {
        XCTAssertNil(validator.validate(text: "onmyway133@gmail.com", with: [.validEmail]))
        XCTAssertNotNil(validator.validate(text: "onmyway133", with: [.validEmail]))
        XCTAssertNotNil(validator.validate(text: "onmyway133.com", with: [.validEmail]))
    }

    func testCountryCode() {
        XCTAssertNil(validator.validate(text: "+47 11 222 333", with: [.countryCode]))
        XCTAssertNotNil(validator.validate(text: "11 222 333", with: [.countryCode]))
        XCTAssertNotNil(validator.validate(text: "47 11 222 333", with: [.countryCode]))
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

支持我的应用程序

*   [推送 Hero -测试推送通知的纯 Swift 原生 macOS 应用](https://onmyway133.com/pushhero)
*   [PastePal -粘贴板、便笺和快捷方式管理器](https://onmyway133.com/pastepal)
*   [快速检查-智能待办事项管理器](https://onmyway133.com/quickcheck)
*   [Alias - App 和文件快捷方式管理器](https://onmyway133.com/alias)
*   [我的其他应用](https://onmyway133.com/apps/)

❤️❤️😇😍🤘❤️❤️