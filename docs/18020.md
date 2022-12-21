# DBIC 测试:rs _ with _ new _ rows

> 原文：<https://dev.to/jplindstrom/dbic-testing-rswithnewrows-5b11>

这里有一个方便的小[DBIx::Class](https://metacpan.org/pod/DBIx::Class)*ResultSet*方法，当您需要编写一个测试来验证数据库更改时可以使用。

```
=head2 rs_with_new_rows() : $resultset_with_new_data

Resultset with only PK ids which are greater than the currently
largest one, i.e. it will only return rows created going forwards.

Great for testing.

=cut

sub rs_with_new_rows {
    my $self = shift;
    my $max_id = $self->get_column("id")->max();
    return $self->search({ id => { ">" => $max_id } });
} 
```

将它放入您的 *ResultSet* 类的基类中，以便所有的类都可以一次性使用它。

如您所见，基本操作是查找最高的`id`列值，然后查找 id 大于该值的行。

这带来了一些警告。首先，PK 列需要是一个不断增加的值(可能是`auto_increment`或`sequence`，这取决于您的数据库)。

第二，PK 栏显然需要叫`id`。如果不是这样，你可以通过使用 *ResultSource* 类的 [primary_columns](https://metacpan.org/pod/DBIx::Class::ResultSource#primary_columns) 方法来解决这个问题。

第三，如果这是一个合适的库，它应该足够灵活和健壮，可以用作更复杂查询的一部分，我们应该使用*ResultSet*[current _ source _ alias](https://metacpan.org/pod/DBIx::Class::ResultSet#current_source_alias)来完全限定列名。

对于这个简单的用例，它工作得很好。

# 最后，让我们编写测试

下面是在测试中如何使用`rs_with_new_rows`方法:

```
note "*** Setup";
my $tree_node_rs = $schema->resultset("TreeNode")->rs_with_new_rows();

note "*** Run";
# Run code under test, which creates tree nodes

note "*** Test";
is($tree_node_rs->count, 3, "Correct number of tree nodes created");
ok(
    ! $tree_node_rs
        ->search(undef, { order_by => "id" })->first
        ->parent_id,
    "First (top) tree node has no parent",
); 
```

这是可行的，因为我们可以在 DBIC 链接结果集，而不需要实际执行任何 SQL 查询。

方法`rs_with_new_rows`以对`->search`的调用结束，调用返回一个添加了新搜索标准的*新*结果集。在我们要求一些数据之前，查询不会运行，例如使用`->all`或`->count`。