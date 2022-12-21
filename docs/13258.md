# 在 Postgres 中对多个记录使用联合和动态更新

> 原文：<https://dev.to/stephencweiss/using-coalesce-and-dynamic-updates-to-multiple-records-in-postgres-50jd>

我想更新多条记录上的多个字段，并处理这样的情况:在每条记录的更新中并没有出现所有的信息。

基本上，我希望`patch`而不是`put`，当它到达 Postgres 时，我不希望那些空值被设置为`null`。

然而，一个常规的`UPDATE`会这样做——如果我不提供它们，就用 null 覆盖我的值。幸运的是，Postgres 自带的`COALESCE`可以适应这种情况。让我们来看看。

我将使用和我使用 Typescript 的`Pick`一样的例子来改善沟通和减少维护

复习一下，该表定义为:

```
interface IMyTable {
  id: string
  foreign_id: string
  name: string

  is_enabled: boolean
  is_custom: boolean
  display_order?: number

  name_en: string
  name_sp?: string
  name_fr?: string

  description_en?: string
  description_sp?: string
  description_fr?: string
} 
```

我希望再次更新的列是`is_enabled`和`display_order`

`patch`的请求体示例可能是:

```
[
  {
    "id": "427001",
    "is_enabled": true
  },
  {
    "id": "427002",
    "display_order": 2
  },
  {
    "id": "427003",
    "is_enabled": true,
    "display_order": 3
  }
] 
```

# 单笔记录更新

如果我只是试图一次更新一条记录，我会有更多关于条件语句的选项来添加到我的查询中。

```
async changeMyTable(proposal: readonly MyTableProposal): Promise<...> {

  const updateQuery = SQL`
    WITH proposed_vals (id, is_enabled, display_order) AS (VALUES (${proposal.id}, ${proposal.is_enabled}, ${proposal.display_order})`
    UPDATE my_table AS t
    SET `;
  if( proposal.is_enabled ) updateQuery.append(SQL`is_enabled = COALESCE( CAST (p.is_enabled as bool), CAST(t.is_enabled as bool) )`)
  if( proposal.display_order ) updateQuery.append(SQL`, display_order = COALESCE( CAST (p.display_order as int4), CAST (t.display_order as int4) )`)
  updateQuery.append(SQL`
    FROM proposed_vals AS p
    WHERE
      t.id = p.id
      and t.end_version is null
    RETURNING
       t.*
    `);

  const data = await this._pool.query(updateQuery);
  return data.rows;
} 
```

**NB** :如果`display_order`存在*而*不存在`is_enabled`，这个*将*抛出一个语法错误

# 处理多条歧义记录

那么，这怎么能适用于多条记录呢？

对于多条记录，我使用了`update...with`语法:

这就是`COALESCE`语句的用武之地。如果元素存在，我们可以使用初始值作为自动防故障装置，而不必事先了解*。如果字段*不存在*，我们将使用现有的值。*

 *我最初尝试使用`COALESCE`的结果是:

```
async changeMyTable(proposal: readonly MyTableProposal[]): Promise<...> {
  const valuesToUpdate = helpers.SQLJoinStatement( /* ... */ )
  const updateQuery = SQL`WITH proposed_vals (id, is_enabled, display_order) AS (VALUES `
  .append(valuesToUpdate)
  .append(SQL` )
     UPDATE my_table AS t
     SET is_enabled = COALESCE( p.is_enabled, t.is_enabled )
     , display_order = COALESCE( p.display_order, t.display_order )
     FROM proposed_vals AS p
     WHERE
       t.id = p.id
       and t.end_version is null
     RETURNING
        t.*
     ;`);

  const data = await this._pool.query(updateQuery);
  return data.rows;
} 
```

然而这就抛出了错误:`500 COALESCE types text and boolean cannot be matched`。

经过研究，我发现我可以用一个`CAST`来确保类型，从而解决这个问题。我觉得这样做很舒服，因为我知道我的建议书打印得很好，而且我已经做好了表格。

```
async changeMyTable(proposal: readonly MyTableProposal[]): Promise<...> {
  const valuesToUpdate = helpers.SQLJoinStatement( /* ... */ )
  const updateQuery = SQL`WITH proposed_vals (id, is_enabled, display_order) AS (VALUES `
  .append(valuesToUpdate)
  .append(SQL` )
     UPDATE my_table AS t
     SET is_enabled = COALESCE( CAST (p.is_enabled AS BOOL), CAST(t.is_enabled AS BOOL) )
     , display_order = COALESCE( CAST (p.display_order AS INT4), CAST (t.display_order AS INT4) )
     FROM proposed_vals AS p
     WHERE
       t.id = p.id
       AND t.end_version IS NULL
     RETURNING
        t.*
     ;`);

  const data = await this._pool.query(updateQuery);
  return data.rows;
} 
```

用另一双眼睛，我能够再次提炼这个。问题是 my `proposed_vals`中的值隐式地具有文本或数字类型。如果我希望它是独立的，我需要提醒 Postgres 这一点。

代替`CAST (value AS type)`，我可以使用更简洁的双冒号符号`::`,并将其仅应用于建议的值。

```
UPDATE metadata_lookupvalues AS luv
  SET is_enabled = COALESCE( p.is_enabled::BOOL, luv.is_enabled )
    , display_order = COALESCE( p.display_order::INT4, luv.display_order )
  FROM proposed_vals AS p
  WHERE
    luv.id = p.id
    AND luv.end_version IS NULL
  RETURNING
    luv.* 
```

# 相关

*   [SQL |堆栈溢出中的双冒号(::)符号](https://stackoverflow.com/questions/5758499/double-colon-notation-in-sql)
*   在 PostgreSQL 中::做什么？堆栈溢出*