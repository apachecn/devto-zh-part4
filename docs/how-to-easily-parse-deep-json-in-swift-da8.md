# 如何在 Swift 中轻松解析深度 json

> 原文：<https://dev.to/onmyway133/how-to-easily-parse-deep-json-in-swift-da8>

JSONCodable 很棒，但有时我们只需要在深度嵌套的 JSON 中快速获取价值。就像我对 Dart [所做的一样，如何在 Dart](https://github.com/onmyway133/blog/issues/198) 中解析深层 json 对象，让我们在 Swift 中完成。

参见[https://github . com/on myway 133/Omnia/blob/master/Sources/Shared/JSON . swift](https://github.com/onmyway133/Omnia/blob/master/Sources/Shared/JSON.swift)

```
public func resolve<T>(_ jsonDictionary: [String: Any], keyPath: String) -> T? {
    var current: Any? = jsonDictionary

    keyPath.split(separator: ".").forEach { component in
        if let maybeInt = Int(component), let array = current as? Array<Any> {
            current = array[maybeInt]
        } else if let dictionary = current as? JSONDictionary {
            current = dictionary[String(component)]
        }
    }

    return current as? T
} 
```

所以我们可以通过关键路径
来解决

```
class JSONTests: XCTestCase {
    func test() {
        let json: [String: Any] = [
            "outside": [
                "object": [
                    "number": 1,
                    "text": "hello"
                ],
                "arrayOfObjects": [
                    [
                        "number": 2,
                        "text": "two"
                    ],
                    [
                        "number": 3,
                        "text": "three"
                    ]
                ],
                "arrayOfArrays": [
                    [
                        "one", "two", "three", "four"
                    ],
                    [
                        "five", "six", "seven"
                    ]
                ]
            ]
        ]

        XCTAssertEqual(resolve(json, keyPath: "outside.object.number"), 1)
        XCTAssertEqual(resolve(json, keyPath: "outside.object.text"), "hello")
        XCTAssertEqual(resolve(json, keyPath: "outside.arrayOfObjects.1.number"), 3)
        XCTAssertEqual(resolve(json, keyPath: "outside.arrayOfArrays.1.1"), "six")
    }
} 
```

原帖[https://on myway 133 . github . io/blog/How-to-easy-parse-deep-JSON-in-Swift/](https://onmyway133.github.io/blog/How-to-easily-parse-deep-json-in-Swift/)