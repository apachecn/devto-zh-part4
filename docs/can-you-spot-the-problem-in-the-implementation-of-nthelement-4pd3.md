# 您能发现 nthElement 实现中的问题吗？

> 原文：<https://dev.to/dwayne/can-you-spot-the-problem-in-the-implementation-of-nthelement-4pd3>

`nthElement` :
的实现有一个问题

```
nthElement : List a -> Int -> Result String a
nthElement list n =
  if n >= 0 then
    nthElementHelper list n
  else
    Err ("The index must be non-negative: " ++ String.fromInt n)

nthElementHelper : List a -> Int -> Result String a
nthElementHelper list n =
  case list of
    [] ->
      Err ("List too short by " ++ String.fromInt (n+1) ++ pluralize n "element" "elements")

    x :: rest ->
      if n == 0 then
        Ok x
      else
        nthElementHelper rest (n-1) 
```

Enter fullscreen mode Exit fullscreen mode

你能发现问题并解决它，使它通过下面所有的测试吗？

```
nthElement : Test
nthElement =
  describe "nthElement"
    [ describe "empty list"
        [ fuzz Fuzz.int "can never find the nth element of the empty list" <|
            \n ->
              Ch1.nthElement [] n
                |> Expect.err
        ]
    , describe "non-empty list"
        [ test "it returns the 1st element" <|
            \_ ->
              Ch1.nthElement [1, 2, 3, 4, 5] 0
                |> Expect.equal (Ok 1)
        , test "it returns the 2nd element" <|
            \_ ->
              Ch1.nthElement [1, 2, 3, 4, 5] 1
                |> Expect.equal (Ok 2)
        , test "it returns the 5th element" <|
            \_ ->
              Ch1.nthElement [1, 2, 3, 4, 5] 4
                |> Expect.equal (Ok 5)
        , test "it returns an error given a negative index" <|
            \_ ->
              Ch1.nthElement [1, 2, 3, 4, 5] -1
                |> Expect.equal (Err "The index must be non-negative: -1")
        , test "it returns an error given an out of bounds index" <|
            \_ ->
              Ch1.nthElement [1, 2, 3, 4, 5] 5
                |> Expect.equal (Err "List too short by 1 element")
        ]
    ] 
```

Enter fullscreen mode Exit fullscreen mode

请在下面的评论中分享你的想法。

**附言** *这个错误是我个人犯的，我是在测试中发现的。即使在类型化的函数式语言中，测试仍然是有用的。*