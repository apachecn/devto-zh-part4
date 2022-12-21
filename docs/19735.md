# 介绍一种新的编程语言

> 原文：<https://dev.to/mak12776/introducing-a-new-programming-language-k8g>

我在设计一种新的编程语言。我想为此写一个编译器。这将是开源和免费的，将在这里托管[。但是我没有激情和动力去写它。所以我决定写一个教程，介绍语言设计和哲学。这样我就能有动力继续写下去。](https://github.com/mak12776/codescript)

我给它起了个名字，`codescript` :
`codescript`是一种解释型的，高级的，通用的编程语言。`codescript`是动态类型化和静态类型化语言的混合体。

下面的代码是一个教程，通过例子解释语言的概念。我写不出好的英语句子，代码例子是我解释一些事情的最好方式。

也许我会随着时间的推移更新这个帖子。因此，请查看更新。

```
-- comments start with two '-'., and end with a newline.

++
block comments start with two '+', and end with two '+'.
++

**
I'm not sure about block comments, it may be better with two "*".
**

--# statements:

-- `;` can be used to separate statements, but they aren't mandatory.
-- `\n` (newline) will be considered as a statement separator, when needed.

a = 1 + 2
-- or
a =
1 +
2

-- ' ' can be used where you need to beautify your codes. can't be used between
--  digits of a number or letters of a variable name.

a_float = 10    .   3                           -- valid

variable    .   attribute_of_variable = 5       -- valid

a_number = 4493   9283                          -- two integers: 4493, 9283

-- `~` can be used to continue statements.

very_long_ling_long_long_long_long_long_long_long_and_long_variable_name = 10 ~
.30
-- very_..._variable_name = 10.30

--# variable names:

-- variable names must math regex pattern '[_a-zA-Z][_a-zA-Z0-9]*', and they 
--  can't be 'T', 'F', 'N' constants.
-- at start of a file, no variable defined.

-- none of these variables previously defined:

_abc1 = 1       -- valid
abcd = 1        -- valid

if = 1          -- valid
while = 1       -- valid
for = 1         -- valid
else = 1        -- valid

char = 1        -- valid
integer = 1     -- valid
int = 1         -- valid
float = 1       -- valid
string = 1      -- valid
True = 1        -- valid
NULL = 1        -- valid

print = 1       -- valid
puts = 1        -- valid
io = 1          -- valid
sys = 1         -- valid
stdio = 1       -- valid
stdlib = 1      -- valid
stdin = 1       -- valid
system = 1      -- valid

1good = 1       -- invalid
her-b = 1       -- invalid
her_b = 1       -- valid

--# types:

char = 'a'
int = 10
float = 12.3

-- 'T', 'F', 'N' characters are constant values.

bool = T || F
none = N

array = [1, 2, 3]           -- an array of any type
string = "abc"              -- an array of only char type

object = {}

code = #{a = $b}            -- a code object

function => 10;             -- a lazy evaluation, or function
-- or
function = @ => 1;          -- anonymous function

module = [# name]           -- a module, for external files and libraries

--# simple types:

english_first_letter = 'a'
seconds_in_a_year = 60 * 60 * 24 * 365
pi_number = 3.14159265

true = T
false = F
-- and
none = N

-- we can't compare none and booleans

T == F                      -- no error
N == T                      -- error: can't compare N and T

--# length of arrays:

var1 = "Hello"
var1_len = var1.len             -- var1_len = 5

var2 = [1, 2, 3]
var2_len = var2.len             -- var2_len = 3

--# append arrays:

var1 = "Hello"
var1 += " World"                -- var1 = "Hello World"

var2 = [1, 2, 3]
var2 += [4]                     -- var2 = [1, 2, 3, 4]

--# multiply arrays:

var1 = "-" * 5                  -- var1 = "-----"

var2 = "bye" * 3                -- var2 = "byebyebye"

--# defining objects:

-- objects are likes classes or structures, or maybe name spaces.

var1 = {
    name = "John"
    age = 45
}

var1_name = var1.name           -- var1_name = "John"
var1.color = "white"            -- set color attribute of var1

-- can be used for defining temporary variables:

var2 = {                        -- var2 = 160
    sum = 1 + 3 + 5 + 7
    result = sum * 10
}.result

--# defining code object:

define_var = #(var = 10);

define_var;                     

is_ten = (var == 10);           -- is_ten = T

--# defining lazy evaluations or functions:

sum a b => a + b;
-- or
sum = @ a b => (a + b);         -- anonymous function

--# check type of variables:

-- get type of variables by placing a '?' after them. they will be returned as
--  strings.

var = 2.3
var_type = var?                 -- var_type = "float"

var1 = [1, 2, 3]
is_array = (var1? == "array")   -- is_array = T

var2 = [1, 'a', "abc"]

var2_type = var2?               -- var2_type = "array"

-- get full type notation by placing two '?' after them. they will be returned 
--  as strings.

var2_full_type = var2??         -- var2_full_type = "[int, char, string]"

var3 = [[1], [1, 1.0]]
var3_full_type = var3??         -- var3_full_type = "[[int], [int, float]]"

var4 = {
    name = "John"
    age = 30
}
var4_type = var4?               -- var4_type = "object"
var4_full_type = var4??         -- var4_full_type = "{name: string, age: int}"

--# dynamic typed:

var1 = 10
var1 = "Hello"                  -- no error

--# static typed:

int_type value => (value? == "int");

number :: int_type;            -- type declartion
-- or
number :: (number? == "int");   -- same thing
-- or
number :: int_type = 1;          -- type declartion and variable assignment

number = 10                     -- no error
number = "bye"                  -- error: `number` assign assertion failed.

--# program flow:

--# branches:
-- mnemonic:                LABEL:

loop:
end:

--# jump to branches:
-- mnemonic:                -> LABEL

-> loop
-> end

--# conditional branches:
-- mnemonic:                ( CONDITION ): STATEMENT;

(a == N): a = 1;

(a >= 0): 
(
    a = a + 1;
    b = a + 2;
)

--# algorithm examples:

-- check whether a number is even or not

print = [# io].print

is_even num => (num % 2 == 0)

(is_even 10):
(
    print "even"
    -> end
)
print "old"
end:

-- find max number in a list

list = [1, 5, 2, 6, 3, 8, 0]

max = list[0]
index = 1

loop:
(index < list.len):
(
    (list[index] > max): max = list[index];
    index += 1;
    -> loop;
) 
```