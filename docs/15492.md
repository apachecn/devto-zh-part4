# 如何用 Axios 下载文件

> 原文：<https://dev.to/doxomo/how-to-download-files-with-axios-2b8g>

使用 Blob()构造函数的简单示例:

```
export function someFunction(values) {
  return (dispatch) => {
    ...
    const method = 'GET';
    const url = 'http://go.api/download_file';
    ...
    axios
      .request({
        url,
        method,
        responseType: 'blob', //important
      })
      .then(({ data }) => {
        const downloadUrl = window.URL.createObjectURL(new Blob([data]));
        const link = document.createElement('a');
        link.href = downloadUrl;
        link.setAttribute('download', 'file.zip'); //any other extension
        document.body.appendChild(link);
        link.click();
        link.remove();
      });
  };
} 
```

Enter fullscreen mode Exit fullscreen mode