# Go 1.13 错误和 gRPC 错误

> 原文：<https://dev.to/khepin/go-1-13-errors-and-grpc-errors-1gik>

我试图弄清楚一个错误是否是带有 go 1.13 风格错误的包装 grpc 错误(目前使用 golang.org/x/xerrors 包来填充该功能)。

找到底层错误将允许我根据状态报告它或不报告它。错误不需要出现在我们的错误报告系统中。

但是，您不能访问 grpc 错误的实际错误类型，因为它不是 grpc/状态包导出的类型。

基本上我想做:

```
if grpcCauseError := new(status.statusError); xerrors.As(err, &grpcCauseError) {
    errorCode := status.Code(grpcCauseError)
    switch errorCode {
    case codes.InvalidArgument, codes.NotFound, codes.AlreadyExists, codes.PermissionDenied, codes.Unimplemented, codes.Unauthenticated:
        // Don't report it
    default:
        // Report it
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这不会起作用，因为我不能访问`status.statusError`类型。我试图制造一个新的 grpc 错误来匹配:

```
if grpcCauseError := grpc.Errorf(codes.Unknown, "error"); xerrors.As(err, &grpcCauseError) {
    errorCode := status.Code(grpcCauseError)
    switch errorCode {
    case codes.InvalidArgument, codes.NotFound, codes.AlreadyExists, codes.PermissionDenied, codes.Unimplemented, codes.Unauthenticated:
        // Don't report it
    default:
        // Report it
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

但是这也不起作用，因为`grpcCauseError`最后只有类型`error`和*所有的*错误都将匹配那个接口，因此`if`语句总是成功的。

`status.statusError`类型输出两个方法:`Error()`和`GRPCStatus()`。所以我们可以做一个符合这个的界面:

```
type grpcError interface {
    Error() string
    GRPCStatus() *status.Status
} 
```

Enter fullscreen mode Exit fullscreen mode

为了能够与`xerrors.As()`一起使用，我们需要的不仅仅是一个指向接口的指针，我们还需要一个该接口的实际实例。这可以通过转换一个现有的 grpc 错误或者创建一个匹配该接口的新类型来实现:

```
baseGrpcError := grpc.Errorf(codes.Unknown, "oops")
grpcCauseError := e.(grpcError)
if xerrors.As(err, &grpcCauseError) {
    errorCode := status.Code(grpcCauseError)
    switch errorCode {
    case codes.InvalidArgument, codes.NotFound, codes.AlreadyExists, codes.PermissionDenied, codes.Unimplemented, codes.Unauthenticated:
        // Don't report it
    default:
        // Report it
    }
}
// OR
type mockGRPCError struct{}

func (*mockGRPCError) Error() string              { return "" }
func (*mockGRPCError) GRPCStatus() *status.Status { return &status.Status{} }

var grpcCauseError grpcError
grpcCauseError = mockGRPCError{}
if xerrors.As(err, &grpcCauseError) {
    errorCode := status.Code(grpcCauseError)
    switch errorCode {
    case codes.InvalidArgument, codes.NotFound, codes.AlreadyExists, codes.PermissionDenied, codes.Unimplemented, codes.Unauthenticated:
        // Don't report it
    default:
        // Report it
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这两种情况下，可以在一个函数中抽象出基本误差，这样我们可以得到一个更简洁的代码:

```
if grpcCauseError := getGrpcError(); xerrors.As(err, &grpcCauseError) {
    errorCode := status.Code(grpcCauseError)
    switch errorCode {
    case codes.InvalidArgument, codes.NotFound, codes.AlreadyExists, codes.PermissionDenied, codes.Unimplemented, codes.Unauthenticated:
        // Don't report it
    default:
        // Report it
    }
}

func getGrpcError() *grpcError {
    baseGrpcError := grpc.Errorf(codes.Unknown, "oops")
    grpcCauseError := e.(grpcError)
    return grpcCauseError
} 
```

Enter fullscreen mode Exit fullscreen mode