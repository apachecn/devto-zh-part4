# c 程序不能正常工作。

> 原文：<https://dev.to/abhijeet_s1/c-program-is-not-working-properly-3np4>

//wap 输入包含大小写字母和数字的密码？

# 包括

# 包括

# 包括

int main()
{
char pass[10]；
int a，b，c = 0；
int I；
printf("输入密码:\ n ")；
scanf("%s "，pass)；

```
for(i=0;i<10;i++){

   if(islower(pass[i])){
       a++;
    }
   if(isdigit(pass[i])){
        b++;
    }
   if(isupper(pass[i])){
        c++;
    }
}
    if((a>0) && (b>0) && (c>0)){
        printf("pass is legit.");
    }else{
        printf("pass is not valid.");
    }

    return 0; 
```

}