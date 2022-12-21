# 数据科学在线课程

> 原文：<https://dev.to/intellipaat/data-science-online-course-h98>

users = [[0，" Hero "，0]，
[1，" Dunn "，2]，
[2，" Sue "，3]，
[3，" Chi "，3]]

从类型导入 Tuple、Sequence、List、Any、Callable、Dict、Iterator
从集合导入 defaultdict

# 我们稍后会用到几个类型别名

Row = Dict[str，Any] #数据库行
where clause = Callable[[Row]，bool] #单个行的谓词
在多个行上有 Clause = Callable[[List[Row]]，bool] #谓词

类表:
def **init** (self，columns: List[str]，types:List[type])->None:
assert len(columns)= = len(types)，" # of columns 必须= = # of types "

```
 self.columns = columns         # Names of columns
    self.types = types             # Data types of columns
    self.rows: List[Row] = []      # (no data yet)

def col2type(self, col: str) -> type:
    idx = self.columns.index(col)      # Find the index of the column,
    return self.types[idx]             # and return its type.

......

    # Check for right types of values
    for value, typ3 in zip(values, self.types):
        if not isinstance(value, typ3) and value is not None:
            raise TypeError(f"Expected type {typ3} but got {value}")

    # Add the corresponding dict as a "row"
    self.rows.append(dict(zip(self.columns, values)))

def __getitem__(self, idx: int) -> Row:
    return self.rows[idx]

def __iter__(self) -> Iterator[Row]:
    return iter(self.rows)

def __len__(self) -> int:
    return len(self.rows) 
```

......

```
 return f"{self.columns}\n{rows}"

def update(self,
           updates: Dict[str, Any],
           predicate: WhereClause = lambda row: True):
    # First make sure the updates have valid names and types
    for column, new_value in updates.items():
        if column not in self.columns:
            raise ValueError(f"invalid column: {column}")

    .....

    # Now update
    for row in self.rows:
        if predicate(row):
            for column, new_value in updates.items():
                row[column] = new_value

def delete(self, predicate: WhereClause = lambda row: True) -> None:
    """Delete all rows matching predicate"""
    self.rows = [row for row in self.rows if not predicate(row)] 
```

.....

```
 if keep_columns is None:         # If no columns specified,
        keep_columns = self.columns  # return all columns

    if additional_columns is None:
        additional_columns = {}

    # New column names and types
    new_columns = keep_columns + list(additional_columns.keys())
    keep_types = [self.col2type(col) for col in keep_columns]

    # This is how to get the return type from a type annotation.
    # It will crash if `calculation` doesn't have a return type.
    add_types = [calculation.__annotations__['return']
                 for calculation in additional_columns.values()]

  .....

    for row in self.rows:
        new_row = [row[column] for column in keep_columns]
        for column_name, calculation in additional_columns.items():
            new_row.append(calculation(row))
        new_table.insert(new_row) 
```

这是对数据科学计划的简短描述，有关学习数据科学的更多详细信息，并且想要完成数据科学课程和认证，请联系我们并立即访问 Intellipaat