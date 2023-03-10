# Python 脚本:验证信用卡号——Luhn 的算法

> 原文：<https://dev.to/anuragrana/python-script-validating-credit-card-number-luhn-s-algorithm-2f7c>

本文原载于 PythonCircle.com**。**

 **Luhn 算法，也称为“模数 10”算法，是一种校验和公式，用于验证各种标识号，如信用卡号、IMEI 号、美国的国家提供商标识号、加拿大社会保险号和以色列 ID 号。

**算法:**
该公式根据包含的校验位验证一个数字，该校验位通常追加到部分账号后，生成完整账号。

正在生成校验位:

*   假设你有一个数字:3 - 7 - 5 - 6 - 2 - 1 - 9 - 8 - 6 - 7 - X，其中 X 是校验位。
*   现在从最右边的数字开始，即校验位，每隔一个数字加倍。新号码将是:3 - 14 - 5 - 12 - 2 - 2 - 9 - 16 - 6 - 14 - X
*   现在，如果一个数字的 double 大于 9，请将这些数字相加。所以数字会变成:3 - 5 - 5 - 3 - 2 - 2 - 9 - 7 - 6 - 5 - X
*   现在将所有数字相加。47 + X
*   非支票部分乘以 9。所以它将是 47 * 9 = 423
*   乘法结果中的单位数字是校验位。X = 3
*   有效号码应该是 37562198673。

**验证生成的数字:**
您可以使用网上可用的工具，根据 Luhn 的算法验证生成的数字是否有效。您可以通过访问[这个](http://www.validcreditcardnumber.com/)网站来验证该号码。

**验证信用卡号的 Python 脚本:**

```
"""
Python script to check the validity of credit card numbers
Author : PythonCircle.Com
Read more : https://www.pythoncircle.com/post/485/python-script-8-validating-credit-card-number-luhns-algorithm/
"""

import sys

def usage():
    msg = """

        usage:
        python3 credit_card_validator credit_card_number

        example:
        python3 credit_card_validator 34678253793

    """
    print(msg)

def get_cc_number():
    if len(sys.argv) < 2:
        usage()
        sys.exit(1)

    return sys.argv[1]

def sum_digits(digit):
    if digit < 10:
        return digit
    else:
        sum = (digit % 10) + (digit // 10)
        return sum

def validate(cc_num):
    # reverse the credit card number
    cc_num = cc_num[::-1]
    # convert to integer list
    cc_num = [int(x) for x in cc_num]
    # double every second digit
    doubled_second_digit_list = list()
    digits = list(enumerate(cc_num, start=1))
    for index, digit in digits:
        if index % 2 == 0:
            doubled_second_digit_list.append(digit * 2)
        else:
            doubled_second_digit_list.append(digit)

    # add the digits if any number is more than 9
    doubled_second_digit_list = [sum_digits(x) for x in doubled_second_digit_list]
    # sum all digits
    sum_of_digits = sum(doubled_second_digit_list)
    # return True or False
    return sum_of_digits % 10 == 0

if __name__ == "__main__":
    print(validate(get_cc_number())) 
```

代码可在 [Github](https://github.com/anuragrana/Python-Scripts/blob/master/credit_card_validator.py) 上获得。

更多来自[PythonCircle.com](https://www.pythoncircle.com):

*   [排名前五的 Python 书籍](https://www.pythoncircle.com/post/646/top-5-python-books/)
*   [如何使用 Mailgun 和 Python Django 免费发送批量邮件](https://www.pythoncircle.com/post/146/how-to-send-bulk-emails-for-free-using-mailgun-and-python-django/)
*   [使用 Python Request 和 Beautifulsoup 抓取 2019 年印度大选数据并分析](https://www.pythoncircle.com/post/683/scraping-data-of-2019-indian-general-election-using-python-request-and-beautifulsoup-and-analyzing-it/)**