# 使用 utPLSQL 上下文测试死亡之星安全性

> 原文：<https://dev.to/pesse/testing-the-deathstar-security-with-utplsql-contexts-30p5>

utPLSQL 真正伟大的特性之一是上下文。它们允许在测试套件中进一步组织测试(也可以通过套件路径分层组织[)。](https://dev.to/pesse/100codeexamples--utplsqls-suite-hierarchy-in-action-lk3)

上下文可以帮助做几件事:

*   它们可以减少设置/拆卸时间(事情可以在每个上下文中完成一次，而不是在每次测试之前)
*   他们可以通过组合几个测试来帮助揭示意图
*   它们可以用来避免重复

在这个例子中，我们建立在主动死亡之星协议的基础上，在我们的例子中，它控制安全系统如何对一个未知的蒙面人作出反应(你们都知道在死亡之星蒙面的陌生人总是秘密的绝地)。

```
create or replace package deathstar_security as

  /* This is just for making the setting a
     little bit more realistic. Implementation-wise
     we dont make a distinction whether a person
     is hooded (which is a sure sign for an
     undercover jedi) or not
   */
  type t_person is record (
    is_hooded boolean
    );

  /* Returns a welcome message based on the
     active Deathstar protocols DEFENSE_MODE
   */
  function welcome(i_person t_person)
    return varchar2;

  /* Checks whether access is allowed based on
     the active Deathstar protocols ALERT_MODE
   */
  function access_allowed( i_person t_person )
    return boolean;
end;
/

create or replace package body deathstar_security as
  /* Helper-function to get the active
     Deathstar protocol row
   */
  function active_protocol
    return deathstar_protocols%rowtype
  as
    l_result deathstar_protocols%rowtype;
  begin
    select p.* into l_result
      from deathstar_protocols p
        inner join deathstar_protocol_active pa
          on p.id = pa.id;
    return l_result;
  end;

  function welcome(i_person t_person)
    return varchar2
  as
    l_protocol deathstar_protocols%rowtype
      := active_protocol();
  begin
    case l_protocol.defense_mode
      when 'BE_KIND' then
        return 'Be welcome!';
      when 'BE_SUSPICIOUS' then
        return 'Are you a jedi?';
      when 'SHOOT_FIRST_ASK_LATER' then
        return 'Die rebel scum!';
      else
        raise_application_error(-20000, 'Ooops, no welcome');
    end case;
  end;

  function access_allowed( i_person t_person )
    return boolean
  as
    l_protocol deathstar_protocols%rowtype
      := active_protocol();
  begin
    case l_protocol.alert_level
      when 'LOW' then
        return true;
      when 'MEDIUM' then
        return false;
      when 'VERY HIGH' then
        raise_application_error(-20100,
          'Unauthorized attempt to access a public area');
      else
        raise_application_error(-20000, 'Oooops, no access');
    end case;
  end;
end;
/ 
```

我们有两种方法`welcome`和`access_allowed`，根据激活的协议，安全系统会做出不同的反应。

为了减少设置时间(让我们假设设置活动协议是一个非常昂贵的操作)并给整个测试套件一些结构，我们使用`%context`注释并将我们的测试套件分成三个不同的部分:

```
create or replace package ut_deathstar_security as
    -- %suite(Deathstar Security)
    -- %suitepath(deathstar.defense)

    /* This first beforeall is only issued
       once for the whole suite - which can be
       a massive speed boost
     */
    -- %beforeall
    procedure setup_test_protocols;

    /* Every context can have an identifier */
    -- %context(low)
        /* With the displayname-annotation we can also
           give a label */
        -- %displayname(Protocol: Low)

        /* This is only issued once in this context */
        -- %beforeall
        procedure setup_protocol_low;

        -- %test(Hooded Person gets a kind welcome message)
        procedure low_welcome_message;

        -- %test(Entry to public area is allowed)
        procedure low_entry_allowed;
    /* Every context needs to be closed */
    -- %endcontext

    -- %context(medium)
        -- %displayname(Protocol: Medium)

        -- %beforeall
        procedure setup_protocol_medium;

        -- %test(Hooded Person gets a suspicious welcome message)
        procedure medium_welcome_message;

        -- %test(Entry to public area is denied)
        procedure medium_entry_allowed;
    -- %endcontext

    -- %context(high)
        -- %displayname(Protocol: High)

        -- %beforeall
        procedure setup_protocol_high;

        -- %test(Hooded Person is yelled at)
        procedure high_welcome_message;

        -- %test(Try to access public area throws exception)
        -- %throws(-20100)
        procedure high_entry_allowed;
    -- %endcontext
end;
/ 
```

测试的实现非常简单:

```
create or replace package body ut_deathstar_security as

  /* Helper-function to get a hooded test-person
     Not really needed but everything gets
     better with hoods!
   */
  function get_hooded_person
    return deathstar_security.t_person
  as
    l_result deathstar_security.t_person;
  begin
    l_result.is_hooded := true;
    return l_result;
  end;

  /* Helper-Function to make the tests
     more expressive
   */
  procedure expect_welcome_message(
    i_expected_msg varchar2)
  as
  begin
    ut.expect(
      deathstar_security.welcome(
          get_hooded_person()
        )
      ).to_equal(i_expected_msg);
  end;

  /* Helper-Function to make the tests
     more expressive
   */
  procedure expect_access(
    i_expected_access boolean)
  as
  begin
    ut.expect(
      deathstar_security.access_allowed(
          get_hooded_person()
        )
      ).to_equal(i_expected_access);
  end;

  procedure setup_test_protocols
  as
  begin
    /* For we want to completely control our tests,
       we also set up specific test-protocols because we
       cannot be sure they stay the same over time.
       This is highly dependent on the use case, of course
     */
    insert into deathstar_protocols
      values (-1, 'Test Low', 'LOW', 'BE_KIND', 80);
    insert into deathstar_protocols
      values (-2, 'Test Medium', 'MEDIUM', 'BE_SUSPICIOUS', 90);
    insert into deathstar_protocols
      values (-3, 'Test High', 'VERY HIGH', 'SHOOT_FIRST_ASK_LATER', 120);

    /* In case no active protocol entry exists */
    insert into deathstar_protocol_active ( id )
      select -1 from dual
        where not exists (select 1 from deathstar_protocol_active);
  end;

  procedure setup_protocol_low
  as
  begin
    update deathstar_protocol_active
      set id = -1;
  end;

  procedure low_welcome_message
  as
  begin
    expect_welcome_message('Be welcome!');
  end;

  procedure low_entry_allowed
  as
  begin
    expect_access(true);
  end;

  procedure setup_protocol_medium
  as
  begin
    update deathstar_protocol_active
      set id = -2;
  end;

  procedure medium_welcome_message
  as
  begin
    expect_welcome_message('Are you a jedi?');
  end;

  procedure medium_entry_allowed
  as
  begin
    expect_access(false);
  end;

  procedure setup_protocol_high
  as
  begin
    update deathstar_protocol_active
      set id = -3;
  end;

  procedure high_welcome_message
  as
  begin
    expect_welcome_message('Die rebel scum!');
  end;

  procedure high_entry_allowed
  as
  begin
    expect_access(false);
  end;
end;
/ 
```

测试的结果看起来很有条理，也很容易理解:

```
call ut.run('ut_deathstar_security');

deathstar
  defense
    Deathstar Security
      Protocol: Medium
        Hooded Person gets a suspicious welcome message [,003 sec]
        Entry to public area is denied [,002 sec]
      Protocol: Low
        Hooded Person gets a kind welcome message [,001 sec]
        Entry to public area is allowed [,001 sec]
      Protocol: High
        Hooded Person is yelled at [,001 sec]
        Try to access public area throws exception [,002 sec]

Finished in ,020629 seconds
6 tests, 0 failed, 0 errored, 0 disabled, 0 warning(s) 
```

您也可以使用 suitepath-notation 只测试一个特定的上下文:

```
call ut.run(':deathstar.defense.ut_deathstar_security.high');

deathstar
  defense
    Deathstar Security
      Protocol: High
        Hooded Person is yelled at [,002 sec]
        Try to access public area throws exception [,001 sec]

Finished in ,009016 seconds
2 tests, 0 failed, 0 errored, 0 disabled, 0 warning(s) 
```

和往常一样，你可以在我的 [github 库](https://github.com/pesse/sith-demo-db/blob/master/100CodeExamples/utplsql/15_utplsql_contexts_with_deathstar_protocol.sql)上找到完整的例子。

*我很乐意听到你如何喜欢这类例子，不要犹豫，联系我！*