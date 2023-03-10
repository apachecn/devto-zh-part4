# 快速破解:将 MathML 转换成 LaTeX

> 原文：<https://dev.to/furkan_kalkan1/quick-hack-converting-mathml-to-latex-159c>

最近需要把文章元数据中的一些 MathML 代码从 [SCOAP3](https://beta.scoap3.org/) 转换成 LaTex 格式。大多数机构存储库对 XML 实体进行转义，因此 MathML 不能正确呈现。我试过了 [Wiris 的 API](http://www.wiris.com/en/book/export/html/1696) ，但是它非常慢，而且在大部分长公式中都会出错。
最后，我发现 [Yaroshevich 的 XSL 模式](http://xsltml.sf.net)工作正常。

示例 Python 代码:

```
import lxml.etree as ET

def to_latex(text):

    """ Remove TeX codes in text"""
    text = re.sub(r"(\$\$.*?\$\$)", " ", text) 

    """ Find MathML codes and replace it with its LaTeX representations."""
    mml_codes = re.findall(r"(<math.*?<\/math>)", text)
    for mml_code in mml_codes:
        mml_ns = mml_code.replace('<math>', '<math >') #Required.
        mml_dom = ET.fromstring(mml_ns)
        xslt = ET.parse("mmltex/mmltex.xsl")
        transform = ET.XSLT(xslt)
        mmldom = transform(mml_dom)
        latex_code = str(mml_dom)
        text = text.replace(mml_code, latex_code)
    return text 
```