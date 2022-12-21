# CSV 上传性能查询

> 原文：<https://dev.to/gauravjain449/csv-upload-performance-query-5e0b>

我正在使用 react-papaparse 读取 CSV 数据。我试过两种方法来读取数据并存储在一个数组中。

1.  this.state = ({csvData: []})在构造函数中，然后添加到 state

this.setState({ csvData: [...this.state.csvData，data.data]，isLoading: true，isRender: false }

1.  这个。_ data =[]；在此构造函数中。_ data . push(data . data)；并且在完成 this.setState({ csvData: this。_ 数据....

性能方面，第二个要好得多(第一个用了 22 秒，第二个用了 4 秒来读取 37000 条记录)

我的问题是，我可以使用 react 中推荐的第二种方法吗？