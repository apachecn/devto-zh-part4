# 自制性能评估员

> 原文：<https://dev.to/zex/homebrew-performance-evaluator-ng8>

绩效改进是终生的工作。首先，你需要知道它现在是如何工作的。做的方式通常取决于规模。

之前用 Python 开发了一个分布式性能测试系统，花了相当长的时间进化，很复杂。我想在这个全新的项目中尝试一些不同的东西。在介绍各种性能测试框架之前，我想先做一个简单易用的应用程序。

我们走吧。

请记住:简单方便。

那么它应该能够

*   向 API 服务器发送请求
*   评估时间成本
*   生成一些报告
*   Cookie 支持
*   安装ˌ使成形

评估者

```
type Evaluator struct {
  cli *http.Client
  report []Item
  cookie *Cookie
} 
```

`Item`保存请求结果

```
type Item struct {
  Url *url.URL `json:"url,omitempty"`
  Elapsed time.Duration `json:"elapsed,omitempty"`
  CreatedAt time.Time `json:"created_at,omitempty"`
} 
```

`Cookie`保存会话相关数据。

```
type Cookie struct {
  data map[string]interface{}
} 
```

获取一些数据

```
func (self *Evaluator) Elapsed(req *http.Request) (*http.Response, error) {
  before := time.Now()
  rsp, err := self.cli.Do(req)
  self.report = append(self.report, Item{
    Url: req.URL,
    Elapsed: time.Now().Sub(before),
    CreatedAt: before,
  })
  return rsp, err
} 
```

是时候打印一些结果了。

```
func (self *Evaluator) GenReport() error {
  fmt.Println()
  fmt.Println("-- report --")
  for _, v := range self.report {
    fmt.Println(v.String())
  }
  return nil
} 
```

为了获得漂亮的打印效果，也为`Item`创建一个`String`函数。

```
func (self Item) String() string {
  return fmt.Sprintf("%v [%s:%v] %s", self.CreatedAt.Unix(), self.Url.Path, self.Url.RawQuery, self.Elapsed.String())
} 
```

可能会生成一些 CSV 以供进一步处理。我在 Go 中使用了漂亮的字体，很好阅读，但是很难处理。

```
func (self *Evaluator) GenReportCSV(path string) error {
  // ...
  w := csv.NewWriter(fd)
  w.Write([]string{"Timestamp", "Api", "Query", "Elapsed"})
  for _, v := range self.report {
    if err = w.Write([]string{
        fmt.Sprintf("%d", v.CreatedAt.Unix()),
        v.Url.Path,
        fmt.Sprintf("%s", v.Url.RawQuery),
        fmt.Sprintf("%.4f", v.Elapsed.Seconds())});
      err != nil {
        return err
      }
  }
  w.Flush()
  return nil
} 
```

创建一个虚拟客户端来做一些疯狂的事情，`wg`是一个`WaitGroup`
变量来等待所有疯狂的客户端完成它们的操作。

```
func dummy_terminal() {
  app := apps.NewApp()
  defer app.Close()

  // ... do something crazy to API ...

  if err := app.GenReport(); err != nil {
    fmt.Println("generate report failed: ", err)
  }

  out := fmt.Sprintf("%s/performance-%d.csv", *output_base, time.Now().UnixNano())
  if err := app.GenReportCSV(out); err != nil {
    fmt.Println("generate report csv failed: ", err)
  }
  wg.Done()
} 
```

在命令行用`flag`和
进行配置

```
var(
  terminals   = flag.Int("terminals", 30, "total concurrent terminals")
  output_base = flag.String("output_base", "/tmp", "backend output_base")
) 
```

在 Fabric :D 发现了一个很棒的日志模块

```
go get github.com/hyperledger/fabric/common/flogging 
```

CSV 输出看起来像

```
Timestamp,Api,Query,Elapsed
1562142836,/api/home,,2.0599
1562142842,/api/list,,0.0311
1562142842,/api/somepage,id=99,0.0323
1562142842,/api/somepage,id=66,1.6131
1562142845,/api/somepage,id=33,0.0432 
```

控制台输出基本相同，只是时间被格式化了。