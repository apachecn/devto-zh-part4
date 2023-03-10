# 如何在 Swift 中轻松使用错误协议

> 原文：<https://dev.to/ngo275/how-to-use-error-protocol-easily-in-swift-1j5e>

Swift 的`Error`协议没有任何默认内容。iOS 开发人员必须扩展这一协议来处理他们应用程序中的错误。我将介绍一个非常简单的例子来管理定制错误。您可以像下面这样定义您的错误情况。

```
// Error+Extension.swift

enum CustomError: Error {
    case errorOne
    case errorTwo
    case errorThree(message: String)
}

extension CustomError: LocalizedError {
    var errorDescription: String? {
        switch self {
        case .errorOne:
            return NSLocalizedString("Error one's description", comment: "Custom Error")
        case .errorTwo:
            return NSLocalizedString("Error two's description", comment: "Custom Error")
        case .errorThree(let message):
            return NSLocalizedString(message, comment: "Custom Error")
        }
    }
} 
```

[我写了一篇关于处理 UIAlertController](https://dev.to/ngo275/the-easiest-way-to-handle-uialertcontroller-in-swift-1i18) 的文章。用这个`AlertController`，`Error+Extension.swift`可以这样改进。

```
// Error+Extension.swift
enum CustomError: Error {
    case errorOne
    case errorTwo
    case errorThree(message: String)
}

extension CustomError: LocalizedError {
    var errorDescription: String? {
        switch self {
        case .errorOne:
            return NSLocalizedString("Error one's description", comment: "Custom Error")
        case .errorTwo:
            return NSLocalizedString("Error two's description", comment: "Custom Error")
        case .errorThree(let message):
            return NSLocalizedString(message, comment: "Custom Error")
        }
    }
}

extension Error {
    func displayAlert(completion: (() -> Void)? = nil) {
        let alert = AlertController(title: "Error", message: localizedDescription, preferredStyle: .alert)
        let action = UIAlertAction(title: "OK", style: .default)
        alert.addAction(action)
        alert.show(completion: completion)
    }
} 
```