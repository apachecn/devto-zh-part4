# 我的 c 堆栈程序

> 原文：<https://dev.to/gireeshdesineni/my-c-stack-program-1nb6>

为输出
开[https://repl.it/@gireeshdg/ConstantPracticalAnimatronics](https://repl.it/@gireeshdg/ConstantPracticalAnimatronics)

# 包括

# 包括

结构节点
{
int info；
结构节点* ptr
}*top，*top1，* temp

int top element()；
void push(int data)；
虚空 pop()；
虚空空()；
void 显示()；
虚空毁灭()；
void stack_count()。
void create()；

int count = 0；

void main()
{
int no，ch，e；

```
printf("\n 1 - Push");
printf("\n 2 - Pop");
printf("\n 3 - Top");
printf("\n 4 - Empty");
printf("\n 5 - Exit");
printf("\n 6 - Dipslay");
printf("\n 7 - Stack Count");
printf("\n 8 - Destroy stack");
create();
while (1)
{
    printf("\n Enter choice : ");
    scanf("%d", &ch);

    switch (ch)
    {
    case 1:
        printf("Enter data : ");
        scanf("%d", &no);
        push(no);
        break;
    case 2:
        pop();
        break;
    case 3:
        if (top == NULL)
            printf("No elements in stack");
        else
        {
            e = topelement();
            printf("\n Top element : %d", e);
        }
        break;
    case 4:
        empty();
        break;
    case 5:
        exit(0);
    case 6:
        display();
        break;
    case 7:
        stack_count();
        break;
    case 8:
        destroy();
        break;
    default :
        printf(" Wrong choice, Please enter correct choice  ");
        break;
    }
} 
```

}
void create()
{
top = NULL；
}
void stack _ count()
{
printf(" \ n 堆栈中的元素数:%d "，count)；
}
void push(int data)
{
if(top = = NULL)
{
top =(struct node *)malloc(1 * sizeof(struct node))；
top->ptr = NULL；
top->info = data；
}
else
{
temp =(结构节点*)malloc(1*sizeof(结构节点))；
temp->ptr = top；
temp->info = data；
top = temp；
}
count++；
}
void display()
{
top 1 = top；
if(top 1 = = NULL)
{
printf(" Stack is empty ")；
返回；
}
while (top1！= NULL)
{
printf("%d "，top 1->info)；
top1 = top1->ptr；
}
}
void pop()
{
top 1 = top；
if(top 1 = = NULL)
{
printf(" \ n 错误:试图从空堆栈中弹出")；
返回；
}
else
top 1 = top 1->ptr；
printf("\n 弹出值:%d "，top->info)；
免费(顶)；
top = top 1；
计数-；
}
int top element()
{
return(top->info)；
}
void empty()
{
if(top = = NULL)
printf(" \ n Stack 为空")；
else
printf("\n 堆栈不为空，有%d 个元素"，count)；
}
void destroy()
{
top 1 = top；
while (top1！= NULL)
{
top 1 = top->ptr；
免费(上)；
top = top 1；
top 1 = top 1->ptr；
}
免费(top 1)；
top = NULL；printf("\n 所有堆栈元素被销毁")；
count = 0；
}