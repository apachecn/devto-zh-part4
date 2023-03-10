# TPP 主题 16:纯文本的力量

> 原文：<https://dev.to/steadbytes/tpp-topic-16-the-power-of-plain-text-3ajh>

> 这篇文章最初出现在[steadbytes.com](https://steadbytes.com/blog/the-pragmatic-programmer-20th/topic-16-challenges/)
> 
> 见实用程序员 20 周年纪念版系列[第一帖](https://dev.to/steadbytes/the-pragmatic-programmer-20th-anniversary-edition-series-1e2l)介绍。

## 挑战 1

> 设计一个小的地址簿数据库(姓名、电话号码等等)，使用你选择的语言中简单的二进制表示。在阅读本挑战的其余部分之前，请完成此操作。
> 
> *   使用 XML 或 JSON 将该格式转换成纯文本格式。
> *   对于每个版本，添加一个新的可变长度字段，称为方向，您可以在其中输入每个人的家的方向。
> 
> 关于版本和可扩展性会出现什么问题？哪种形式更容易修改？如何转换现有数据？

完整代码可以在 [GitHub](https://github.com/SteadBytes/study/tree/master/the-pragmatic-programmer-20th/16/challenges/1) 上找到。

### 版本 1

#### 数据模型

每个地址簿记录由一个包含基本个人信息和地址字段的`Person`类表示。还使用一个 [UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier#Version_4_(random)) 为每个记录提供一个唯一的 Id。普遍存储地址*非常复杂，但是由于这不是数据建模的挑战，我假设了一个非常基本的英国地址模型:* 

```
# address_book/models.py 
from dataclasses import dataclass, field
from uuid import uuid4

@dataclass
class Person:
    first_name: str
    last_name: str
    phone_number: str
    house_number: str
    street: str
    town: str
    postcode: str
    id: str = field(default_factory=lambda: str(uuid4())) 
```

我使用 Python 3.7 [数据类](https://docs.python.org/3/library/dataclasses.html)，因为`Person`主要是(除了`Id`代)一个[数据传输对象](https://en.wikipedia.org/wiki/Data_transfer_object) (DTO)。用法:

```
>>> Person("Ben", "Steadman", "+1-087-184-1440", "1", "A Road", "My Town", "CB234")
Person(first_name='Ben', last_name='Steadman', phone_number='+1-087-184-1440', house_number='1', street='A Road', town='My Town', postcode='CB234', id='a14fe77b-b5d2-46e7-b42c-9392b4bbec28') 
```

为了帮助测试，`generate_people`将使用优秀的 [Faker](https://faker.readthedocs.io/en/master/index.html) 库:
生成任意的`People`实例

```
# address_book/models.py 
from faker import Faker

fake = Faker("en_GB")

def generate_people(n: int) -> Iterable[Person]:
    for _ in range(n):
        yield Person(
            fake.first_name(),
            fake.last_name(),
            fake.phone_number(),
            fake.building_number(),
            fake.street_name(),
            fake.city(),
            fake.postcode(),
        ) 
```

用法:

```
>>> list(generate_people(2))
[
    Person(
        first_name="Victor",
        last_name="Pearce",
        phone_number="01184960739",
        house_number="2",
        street="Mohamed divide",
        town="Charleneburgh",
        postcode="LS7 0DJ",
        id="cb242277-44dd-4836-98c7-ddbe10183fb4",
    ),
    Person(
        first_name="Stanley",
        last_name="Ashton",
        phone_number="(0131) 496 0908",
        house_number="2",
        street="Karen bridge",
        town="Port Gailland",
        postcode="L3J 2YF",
        id="ef85cfd1-08eb-4629-8747-3d8be1580fc7",
    ),
] 
```

#### 二进制表示

由于这个挑战是关于数据*格式*而不是构建数据库，我将*地址簿数据库*解释为*一个包含地址簿记录列表*的文件——而不是 [DBMS](https://en.wikipedia.org/wiki/Database#Database_management_system) 。

为了在`Person`类和二进制表示之间进行转换，可以使用 Python `struct`。

> 执行 Python 值和表示为 Python [字节](https://docs.python.org/3/library/stdtypes.html#bytes) opjects 的 C 结构之间的转换。
> 
> - <cite>Python [struct](https://docs.python.org/3/library/struct.html#struct.unpack) 文档</cite>

`Person`可以用下面的`Struct`来表示:

```
import struct

PersonStruct = struct.Struct("50s50s30s10s50s50s10s36s") 
```

对应于下面的 C 结构:

```
struct Person {
    char first_name[50];
    char last_name[50];
    char phone_number[30];
    char house_number[10];
    char street[50];
    char town[50];
    char postcode[10];
    char id[36];
}; 
```

二进制打包/解包用法:

```
>>> as_bytes = PersonStruct.pack(b'Ben', b'Steadman', b'+44(0)116 4960124', b'1', b'A Road', b'My Town', b'CB234', b'b36cb798-946e-4dca-b89c-f393616feb7b')
>>> as_bytes
b'Ben\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00Steadman\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00+44(0)116 4960124\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x001\x00\x00\x00\x00\x00\x00\x00\x00\x00A Road\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00My Town\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00CB234\x00\x00\x00\x00\x00b36cb798-946e-4dca-b89c-f393616feb7b'
>>> PersonStruct.unpack(as_bytes)(b'Ben\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00', b'Steadman\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00', b'+44(0)116 4960124\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00', b'1\x00\x00\x00\x00\x00\x00\x00\x00\x00', b'A Road\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00', b'My Town\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00', b'CB234\x00\x00\x00\x00\x00', b'b36cb798-946e-4dca-b89c-f393616feb7b') 
```

*   **注意**从`PersonStruct.unpack`返回的`tuple`的值是如何被*用`\x00`(空字节)填充*的，因为 struct 格式指定了一个比原来提供的值更长的字符串。这些需要在拆包成`Person`物件时移除。

为了对这些原始字节提供更高层次的抽象，可以将转换功能打包成一些处理`Person`对象的函数:

```
# address_book/binary.py 
import struct
from dataclasses import astuple

from models import Person

PersonStruct = struct.Struct("50s50s30s10s50s50s10s36s")

def from_bytes(buffer: bytes) -> Person:
    return Person(
        *(
            # remove null bytes added by string packing
            x.decode("utf-8").rstrip("\x00")
            for x in PersonStruct.unpack(buffer)
        )
    )

def to_bytes(p: Person) -> bytes:
    return PersonStruct.pack(
        *(s.encode("utf-8") for s in astuple(p))
    ) 
```

用法:

```
>>> me = Person("Ben", "Steadman", "+44(0)116 4960124", "1", "A Road", "My Town", "CB234")
>>> as_bytes = to_bytes(me)
>>> me_again = from_bytes(me)
>>> me == me_again
True 
```

这些`Person`转换函数可以在更高级的函数中使用，以读取和写入整个地址簿数据库:

```
# address_book/binary.py 
from functools import partial
from pathlib import Path
from typing import Iterable, List

def read_address_book(db: Path) -> List[Person]:
    people = []
    with db.open("rb") as f:
        for chunk in iter(partial(f.read, PersonStruct.size), b""):
            people.append(from_bytes(chunk))
    return people

def write_address_book(db: Path, people: Iterable[Person]):
    with db.open("wb") as f:
        f.write(b"".join(to_bytes(p) for p in people)) 
```

用法:

```
>>> people = list(generate_people(50))
>>> db = Path("data/address-book.bin")
>>> write_address_book(db, people)
>>> people_again = read_address_book(db)
>>> people == people_again
True 
```

#### 纯文本表示

我选择了 [JSON](https://www.json.org) 作为纯文本格式，因为出色的 Python 标准库 [`json`](https://docs.python.org/3/library/json.html) 模块使其易于使用。使用相同的`Person`模型，函数`from_dict`和`to_dict`分别类似于`from_bytes`和`to_bytes`，因为`json`模块将 JSON 对象与 Python 字典相互转换。

```
# address_book/plain_text.py 
from dataclasses import asdict

from .models import Person

def from_dict(d: dict) -> Person:
    return Person(**d)

def to_dict(p: Person) -> dict:
    return asdict(p) 
```

用法:

```
>>> me = Person("Ben", "Steadman", "+44(0)116 4960124", "1", "A Road", "My Town", "CB234")
>>> as_dict = to_dict(me)
>>> me_again = from_dict(me)
>>> me == me_again
True 
```

这些可以用来创建`read_address_book`和`write_address_book` :
的 JSON 版本

```
# address_book/plain_text.py 
import json
from functools import partial
from pathlib import Path
from typing import Iterable, List

def read_address_book(db: Path) -> List[Person]:
    with db.open() as f:
        return [from_dict(d) for d in json.load(f)]

def write_address_book(db: Path, people: Iterable[Person]):
    with db.open("w") as f:
        json.dump([to_dict(p) for p in people], f) 
```

用法:

```
>>> people = list(generate_people(50))
>>> db = Path("data/address-book.json")
>>> write_address_book(db, people)
>>> people_again = read_address_book(db)
>>> people == people_again
True 
```

#### 测试

每一个实现都包含在一组简单的单元测试中，这些单元测试断言了它们各自格式之间转换的正确性:

```
import pytest

from address_book import binary, plain_text
from address_book.models import Person, generate_people

@pytest.mark.parametrize("p", generate_people(50))
def test_to_bytes_inverts_from_bytes(p):
    p_bytes = binary.to_bytes(p)
    p_again = binary.from_bytes(p_bytes)
    assert p == p_again

@pytest.mark.parametrize("p", generate_people(50))
def test_to_dict_inverts_from_dict(p):
    p_dict = plain_text.to_dict(p)
    p_again = plain_text.from_dict(p_dict)
    assert p == p_again

@pytest.mark.parametrize(
    "module,fname", [(binary, "address-book.bin"), (plain_text, "address-book.json")]
)
def test_write_address_book_inverts_read_address_book(module, fname, tmp_path):
    db = tmp_path / fname
    # sanity check
    assert db.exists() is False

    people = list(generate_people(50))
    module.write_address_book(db, people)

    assert db.exists() is True
    assert db.stat().st_size > 0

    people_again = module.read_address_book(db)

    assert people == people_again 
```

### 版本 2(可变长度`directions`)

向模型添加额外的`directions`字段非常简单:

```
from dataclasses import dataclass, field
from typing import Iterable
from uuid import uuid4

from faker import Faker

fake = Faker("en_GB")

@dataclass
class Person:
    first_name: str
    last_name: str
    phone_number: str
    house_number: str
    street: str
    town: str
    postcode: str
    directions: str # new
    id: str = field(default_factory=lambda: str(uuid4()))

def generate_people(n: int) -> Iterable[Person]:
    for _ in range(n):
        yield Person(
            fake.first_name(),
            fake.last_name(),
            fake.phone_number(),
            fake.building_number(),
            fake.street_name(),
            fake.city(),
            fake.postcode(),
            # new
            fake.text(),  # random latin is about as useful as most directions
        ) 
```

#### 二进制表示

由于`struct`模块处理 C 结构，字符串被表示为格式字符串中指定的**固定长度**的 C `char`数组，即`struct.pack("11s", "hello world")`。为了在*中实现这一点，通用性*是一个相当复杂的过程，如果你在实际应用中需要这样做，建议使用第三方库，比如 [NetStruct](https://github.com/stendec/netstruct) 。然而，出于这个挑战的目的，我不会使用它和*，也不会实现一个通用的解决方案*——打包/解包记录的代码与记录的结构紧密耦合，我不建议在实际应用中遵循这种方法。然而，它确实展示了使用二进制格式时可能出现的困难。

由于`directions`字段的大小是可变的，使用`struct`打包/解包记录的完整格式字符串必须由*动态*创建:

```
>>> me = Person(
    "Ben",
    "Steadman",
    "+44(0)116 4960124",
    "1",
    "A Road",
    "My Town",
    "CB234",
    "Take a left at the roundabout",
)
>>> fmt = "50s50s30s10s50s50s10s{}s36s".format(len(me.directions))
>>> struct.pack(fmt, *(s.encode("utf-8") for s in astuple(me)))
b'Ben\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00Steadman\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00+44(0)116 4960124\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x001\x00\x00\x00\x00\x00\x00\x00\x00\x00A Road\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00My Town\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00CB234\x00\x00\x00\x00\x00Take a left at the roundaboutbfe3c3e5-8b65-4e49-8d26-3981257a0dee' 
```

此外，由于每个打包的记录将具有不同的大小，所以数据库文件不能像第一个实现中那样简单地以相同大小的块读取并传递给`from_bytes`。为了解决这个问题，每个记录前面都有它的字节大小。该值可用于确定从文件中读取并传递给`from_bytes`的下一个块大小。:

```
# address_book/binary.py 
PERSON_STRUCT_FMT = "50s50s30s10s50s50s10s{}s36s"

def to_bytes(p: Person) -> Tuple[bytes, int]:
    # dynamically add size to format for variable length directions field
    fmt = PERSON_STRUCT_FMT.format(len(p.directions))
    return (
        struct.pack(fmt, *(s.encode("utf-8") for s in astuple(p))),
        struct.calcsize(fmt),
    )

RecordSizeStruct = struct.Struct("I")

def write_address_book(db: Path, people: Iterable[Person]):
    with db.open("wb") as f:
        records_with_sizes = (
            RecordSizeStruct.pack(size) + p_bytes
            for p_bytes, size in (to_bytes(p) for p in people)
        )
        f.write(b"".join(records_with_sizes)) 
```

`to_bytes`仍然接收一个代表整个打包记录的`buffer`字节，但是为了处理可变长度的`directions`字段，它需要计算`buffer`中`directions`字段必须开始的位置，相应地分割它并单独解包每个部分:

```
# address_book/binary.py 
def from_bytes(buffer: bytes) -> Person:
    # calculate sizes of non-variable formats
    before_fmt, after_fmt = PERSON_STRUCT_FMT.split("{}s")
    before_start = struct.calcsize(before_fmt)
    after_start = len(buffer) - struct.calcsize(after_fmt)

    before, direction, after = (
        buffer[:before_start],
        buffer[before_start:after_start],
        buffer[after_start:],
    )

    # dynamically build struct format string for variable length field
    direction_fmt = "{}s".format(len(direction))
    data = (
        struct.unpack(before_fmt, before)
        + struct.unpack(direction_fmt, direction)
        + struct.unpack(after_fmt, after)
    )
    return Person(*(x.decode("utf-8").rstrip("\x00") for x in data))

def read_address_book(db: Path)->List[Person]:
    people = []
    with db.open("rb") as f:
        while True:
            # each record preceded by its size in bytes, use to determine number
            # of bytes to read from db for the entire record
            size_buf = f.read(RecordSizeStruct.size)
            if not size_buf:
                break  # reached end of db
            record_size = RecordSizeStruct.unpack(size_buf)[0]
            people.append(from_bytes(f.read(record_size)))
    return people 
```

考虑到`to_bytes`现在返回一个`tuple` :
，需要对测试稍作调整

```
@pytest.mark.parametrize("p", generate_people(50))
def test_to_bytes_inverts_from_bytes(p):
    p_bytes, size = binary.to_bytes(p)
    p_again = binary.from_bytes(p_bytes)
    assert p == p_again 
```

#### 纯文本表示

除了对`Person`类的更改之外，不需要进一步的更改来支持新的可变长度字段。

## 总结

虽然我已经同意作者对纯文本格式的偏好，但这个挑战无疑表明，对于大多数情况来说，纯文本是合适的格式。

二进制表示更难扩展，并且(至少在本例中)需要进行突破性的修改。这使得使用第一版本写入的任何数据(在引入可变长度`directions`字段之前)与使用第二版本写入的任何数据不兼容。需要设计一个版本化方案，并以二进制格式表示，例如使用预定义的“头”字节块来包含一些元数据。

使用标准的内置工具实现纯文本表示很简单，并且很容易扩展。如果`directions`字段被认为是可选的，第一个版本中写入的任何数据都与第二个版本完全兼容。转换数据将是一个简单的文本转换，事实上可以在 shell 中使用诸如 [jq](https://stedolan.github.io/jq/) 这样的工具直接实现。这里有一个添加`directions`字段的例子，将它设置为默认的`null` :

```
$ cat data/address-book.json | jq 'map(. + {"directions": null})'
[
  {
    "first_name": "Fiona",
    "last_name": "Power",
    "phone_number": "01314960440",
    "house_number": "91",
    "street": "Sam fields",
    "town": "North Shanebury",
    "postcode": "M38 1FH",
    "directions": null,
    "id": "264bfab6-f1a5-4adc-a86b-28ae8e41817b"
  },
  {
    "first_name": "Lorraine",
    "last_name": "Richards",
    "phone_number": "+448081570114",
    "house_number": "9",
    "street": "Ashleigh loaf",
    "town": "North William",
    "postcode": "M4H 5PW",
    "directions": null,
    "id": "b0b98056-c8ff-4b4e-a68b-b31e8ae43ac3"
  },
  ...
] 
```*