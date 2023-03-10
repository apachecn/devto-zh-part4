# 嘲笑嘲笑者

> 原文：<https://dev.to/angelesbroullon/mock-a-mockito-307>

# 简介

大多数程序员都知道单元测试的概念，并且在学习自动化测试的美妙之处的同时也涉猎了一下 [Junit](http://junit.org/junit4/) 框架。然而，很少有人见过 [Mockito](http://site.mockito.org/) ，它实际上是单元测试的最佳框架之一。

我们可以在服务器上使用一个分层结构，根据它们的功能来分割元素，按照这种思路，我们可以在逻辑层中模块化代码。这就是莫奇托出现的地方。

[![The server layered model: Service layer -> Business  layer -> DataAccess layer -> Database, the first 3 are tagged as Java code](img/e5414d10101354d337d1dca9583d30cc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--W3DuMV9L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9q6v88gsv302zc9pg8eu.png)

通过使用*“模仿者系统”*，我们可以按照*行为驱动开发*范例，用行为仿真替代整个依赖组件类。解释这项任务的最好方式是使用一个例子，所以让我们假设我们有一个名为`IDocumentAccessDao`的接口，它将由类`DocumentAccessDao`实现。这个数据访问对象有一些使用 Jdbc 的数据库访问，虽然我们打算创建测试来覆盖它的所有指令集，但实际连接到数据库是没有意义的，因为它可能不可用，并使我们的测试失败(这也将是集成测试，而不是单元测试)。

## 流程:这个我们怎么喝？

### 1。设置 Maven 依赖项

第一步是将测试依赖项加入到我们的项目中，我们可以通过 Maven 将它们添加到 pom.xml 文件中。

最初写这篇文章时，❕These 是稳定的版本

```
<dependency>
   <groupId>junit</groupId>
   <artifactId>junit</artifactId>
   <version>4.12</version>
   <scope>test</scope>
</dependency>
<dependency>
   <groupId>org.mockito</groupId>
   <artifactId>mockito-all</artifactId>
   <version>1.9.5</version>
   <scope>test</scope>
</dependency> 
```

### 2。模仿数据访问类

❗️If:我们正在使用一个组件，这个组件也可以在其他类中使用(例如， [JDBC](http://www.oracle.com/technetwork/java/javase/jdbc/index.html) 或 [JPA](http://openjpa.apache.org/) 实现来处理与数据库的连接)，对这些组件应用继承会很好，因为它们是高度可重用的。

```
@Local
public interface IDocumentAccessDao extends IJdbcTemplateDao{
  void getCollaborateDocumentStatus() throws GenericDAOException;
} 
```

让我们从创建测试类开始，我们将称之为 DocumentAccessDaoTest，但是不要忘记，如果您使用的是 Spring，您可能希望从 context.xml 文件中加载模拟。

```
<!- DATAACCESS-DAO ->
<context:component-scan base-package="org.mydomain.app.dataaccess.dao.impl" />
<!- DATAACCESS-DAO-MOCKITO ->
<context:component-scan base-package="org.mydomain.aap.dataaccess.mocked" /> 
```

现在让我们检查一下我们要测试的类:

```
@Repository
@Qualifier("DocumentAccessDAO")
@Stateless(name = "DocumentAccessDAO")
@TransactionAttribute(TransactionAttributeType.REQUIRED)
@Interceptors({ SpringBeanAutowiringInterceptor.class })
public class DocumentAccessDAO extends AbstractJdbcTemplateDAO implements
 IDocumentAccessDAO {

 @EJB
 private DocumentDAO documentDAO;
 private DocumentAccessRowMapper dmRowMapper = new DocumentAccessRowMapper();
 private DocumentAccessPreparedStatementSetter dPreparedStatementSetter
  = new DocumentAcccessPreparedStatementSetter();

 @Override
 public List<Document> getCollaborateDocumentStatus() throws GenericDAOException {
  List<Document> listDocs = new LinkedList<Document>();
  try {
   List<Map<String, Object>> resultSet = this.jdbcTemplate.queryForList(
       SqlDocumentManager.COLL_DOCUMENT_STATUS);
   if (!CollectionUtils.isEmpty(resultSet)) {
    for (Map<String, Object> data : resultSet) {
     String docId = String.valueOf(data.get("DOCID"));
     int version = documentDAO.getLastDocumentVersion(docId);
     Document document = documentDAO.getDocumentVersion(docId, version, null);
     listDocs.add(document);
    }
   }
  } catch (DataAccessException e) {
   throw new GenericDAOException(e);
  }
  return listDocs;
 }
} 
```

我们可以看到它使用了对`DocumentDAO`和`JdbcTemplate`方法的调用，所以我们需要模拟这些调用以避免运行其他类的代码。因此，我们将在 DocumentAccessDAOTest 类中使用以下 3 个属性:

*   `documentDAO`:我们将要测试的实体。
*   `mocker`:数据库连接模仿器。
*   `documentDAOMock`:我们打算只执行`DocumentAccessDAO`中的代码，所以我们将通过获取从这个对象调用的每个方法的默认虚拟值来模拟它。

initMock 上的代码将遵循以下结构:

*   初始化模拟:我们需要知道对模拟对象的不同调用的预期结果。这种方法的语法看起来像`initMockForMethod (inputParameters, resultExpected)`，稍后将详细介绍。
*   调用我们想要测试的方法。
*   通过使用断言指令，检查获得的结果是否是我们*期望的结果*。如果我们期望一个异常，我们应该使用一个“期望的”注释。

```
@TransactionAttribute(TransactionAttributeType.REQUIRED)
@Interceptors(SpringBeanAutowiringInterceptor.class)
@ContextConfiguration(locations = "/appDao-testConfiguration.xml")
@RunWith(SpringJUnit4ClassRunner.class)
public class DocumentAccessDAOTest {
  // all the mocks will be injected into this instance
  @InjectMocks
  private DocumentAccessDAO documentDAO;
  // initialize the mocker via annotation
  @Mock
  private JdbcTemplate jdbcTemplate;
  @Mock
  private DocumentAccessDAO documentDAO;

  @Before
  public void initMock() {
    // initialize generic behaviour
    Mockito.when(jdbcTemplate.queryForList(Matchers.anyString())
      .thenReturn(createResultList());
    Mockito.when(documentDAOMock.getLastDocumentVersion(Matchers.anyString()))
      .thenReturn(1);
    Mockito.when(documentDAO.getDocumentVersion((String)
      Matchers.anyString(), Matchers.anyString(), Matchers.anyString()).thenReturn(
      creatDummyDocument());
  }

  @Test
  public void getCollaborateDocumentStatusReturnsValidResultExpected()
   throws GenericDAOException {
    // method to call
    List<Document> listDocument = managerDAO.getCollaborateDocumentStatus();
    // check result
    Assert.assertTrue(!listDocument.isEmpty() && listDocument.size() == 4
     && listDocument.get(0).getDocId().equals(MockedDocumentValues.MY_DOCUMENT_ID) 
     && listDocument.get(1) == null);
  }

  // the exception is managed through the annotation
  @Test(expected = GenericDAOException.class)
  public void getCollaborateDocumentStatusReturnsxceptionException()
   throws GenericDAOException {
    // initialise non-generic mocked methods for this test
    Mockito.when(jdbcTemplate.queryForList(Matchers.anyString())
     .thenThrow( new RecoverableDataAccessException(MockedValues
       .GENERIC_DAO_EXCEPTION_TEXT));    
    // method to call
    managerDAO.getCollaborateDocumentStatus();
  }

  private Document createDummyDocument(){
    Document document = new Document();
    document.setVersion(1);
    document.setDocId(MockedDocumentValues.MY_DOCUMENT_ID);
    return document;
  }
} 
```

如您所见，我们使用类`MockedDocumentValues.java`为一些参数生成虚拟值。这个类属于 Junit 辅助项目中一组名为`Mocked*Values`的公共类，以避免测试用例中的重复值。

* * *

## 针对更复杂情况的食谱

我将对我发现的所有奇怪情况做一个快速的语法列举:

**1。如何模仿标准方法**

最简单的语法是:

*   `Mockito.when(method).thenReturn(valueExpected)`

```
Mockito.when(documentDAOMock.getLastDocumentVersion(sql)).thenReturn(1); 
```

**2。抛出异常**

如果该方法返回值，语法将是:

*   `Mockito.when(method).thenThrow(new Exception())`

```
Mockito.when(this.template.update(sql, status, docId))
.thenThrow(new RecoverableDataAccessException(ERROR_CONNECTION_GENERATOR_STRING)); 
```

但是，如果该方法返回 void，则语法会有所不同，所以要小心句子最后部分的括号:

*   `Mockito.dothrow(Exception.class).when(instance).method(parameter)`

```
Mockito.doThrow(IOException.class).when(this.em).
persist(Matchers.any(Object.class)); 
```

**3。使用匹配器**

*   当我们想要独立于参数内容返回一个值时，我们可以使用`Matchers.any(Object.class)`(其中`Object`可以是我们喜欢的任何自定义类，或者如果使用一个经典的 Java 类型，我们可以使用它们自己的方法:`anyString()`、`anyInt()`、`anyList()`……)。

```
Mockito.when(documentDAOMock.getLastDocumentVersion(
Matchers.anyString())).thenReturn(1); 
```

*   如果我们想做类似的事情，混合我们不介意其内容的参数，而其他值可能是重要的，我们应该组合`Matchers.any(Object.class)`和`Matchers.eq(instance)`

```
Mockito.when(this.template.update(
  Matchers.eq(SqlDocumentManager.INSERT_DOC_AUTH_ACTION), 
  Matchers.any(PreparedStatementSetter.class))).thenThrow(
      new RecoverableDataAccessException(MockedValues.GENERIC_DAO_EXCEPTION_TEXT)); 
```

*   另一个有用的方法是 Matchers.isA(class)。当我们有一系列 em.persist(object)并且我们必须找到我们实际上需要其中的哪一个时，我们可以通过指出它所属的实例类来确定它。

```
public <T> void initMockForFindFails(Class<T> entityClass, Object primaryKey) {
  Mockito.when(this.em.find(
    Macthers.isA(InvalidPersistanceClass.class), 
    Matchers.eq(primaryKey))).thenThrow(new NoResultException());
} 
```

**4。用可能的输入/输出参数模拟程序(持久化方法)**

有时，我们必须在通过`entityManager.persist` (instanceObject)将一个对象插入数据库后检查它的新主键。当这种情况发生时，我们必须模仿这个方法来模拟收到的答案，就像我们在这个例子中所做的那样。

```
/**
 * Mocks the update done when persisting a LegalDoc in the database
 */
public void initMockForPersistLegalDoc() {
  Mockito.doAnswer(new AssignIdToLegalDocAnswer(LEGAL_ID)).when(em).persist(
    Matchers.any(LegalDoc.class));
}

private class AssignIdToLegalDocAnswer implements Answer<Void> {

  private int legalDocId;
  public AssignIdToLegalDocAnswer(int legalDocId) {
    this.legalDocId = legalDocId;
  }

  @Override
  public Void answer(InvocationOnMock invocation) throws Throwable {
    LegalDoc legalDoc = (LegalDoc) invocation.getArguments()[0];
    legalDoc.setLegalDocId(legalDocId);
    return null;
  }

} 
```

使用`doAnswer`方法的另一个复杂的例子是通过回答“在运行中”不仅定义输出或返回语句的变化，而且我们可以定义输入/输出参数。

```
public void initMockForMyProcedure(MyInputOutputObject object1){
  Mockito.doAnswer(new Answer<MyCustomOutputObject>() {
    @Override
    public MyCustomOutputObject answer(final InvocationOnMock invocation)
     throws Throwable {
      // the original argument may be changed, only for this function
      final MyInputOutputObject originalArgument = (invocation.getArguments())[0];
      // we define the ourput parameter value here
      final MyCustomOutputObject returnedValue = new MyCustomOutputObject();
      returnedValue.setValueOutput(new MyCustomOutput());

      return returnedValue;
    }
  }).when(myService).myProcedure(Matchers.any(MyInputOutputObject.class));
} 
```

**5。将 JPA 查询响应方法模仿为单一方法**

这避免了在一个方法中使用几对**“命名查询”/“get results”**时出现的问题，因此它们中每一个的结果不会混淆。

```
public <T> void initMockForCreateNamedQueryGetSingleResult(String sqlQuery,
 T returnedObject, boolean shouldFail) {
  Query mockedQuery = Mockito.mock(Query.class);
  if (shouldFail) {
    Mockito.when(mockedQuery.getSingleResult()).thenThrow(new NoResultException());
   } else {
    Mockito.when(mockedQuery.getSingleResult()).thenReturn(returnedObject);
   }
   Mockito.when(this.em.createNamedQuery(sqlQuery)).thenReturn(mockedQuery);
}

public <T> void initMockForCreateNamedQueryGetResultList(String sqlQuery,
 List<T> returnedObject, boolean shouldFail) {
  Query mockedQuery = Mockito.mock(Query.class);
  if (shouldFail) {
    Mockito.when(mockedQuery.getResultList()).thenThrow(new NoResultException());
  } else {
    Mockito.when(mockedQuery.getResultList()).thenReturn(returnedObject);
  }
  Mockito.when(this.em.createNamedQuery(sqlQuery)).thenReturn(mockedQuery);
} 
```

**6。模仿抽象类**

通过用`CALL_REAL_METHODS`模仿抽象类，可以将抽象类实例化为普通类。

```
public void initMockers() {
  dao = Mockito.mock(AbstractDocumentDAOImpl.class, Mockito.CALLS_REAL_METHODS);
  dao.setEntityManager(jpaMocker.getEntityManager());
} 
```

**7。嘲笑一个“？”参数**

这些应该用 doReturn 指令来嘲笑，就像在没有返回语句的方法上抛出异常一样。

```
public void initMockForGetMap(Map<String, ?> expectedValue) {
  Mockito.doReturn(expectedValue).when(getter).getMap(Matchers.anyString(),
   Matchers.anyString());
} 
```

**8。模仿一个‘对象…’参数**
这些被称为 **vargars 参数**。例如，要模仿类似`JdbcTemplate.queryForList(String sql, Object… args)`的东西，我们需要使用`Matchers<CLASSNAME>.anyVararg()`

```
public void initMockForQueryForList(List<String> expectedvalue){
  Mockito.when(this.template.queryForList(Matchers.anyString(),
   Matchers.<Object>anyVararg())).thenReturn(expectedValue);
} 
```

* * *

## 莫克托的局限性

1.  莫奇托不能模拟期末考试
2.  Mockito 不能模仿静态方法
3.  Mockito 不能模仿 final 方法

如果您需要模拟包含任何此类问题的遗留代码，您应该使用 Powermock，但是要考虑到并非所有的 Mockito 版本都与 [Powermock](http://powermock.github.io/) 完全兼容。

❗️当你的应用程序中有一个邪恶的静态类，并且你无法在不搞乱一半应用程序的情况下清除它时，你可以考虑使用单例模式来避免这个问题。

```
public final class SendMailHelper{
  // this way we make sure we have only one instance
  private static SendMailHelper instance;

  // no one external can create new instance
  private SendMailHelper(){
  }

  //we control here the instance creation
  public static SendMailHelper getInstance(){
    if (instance == null){
      instance = new SendMailHelper();
    }
    return instance;
  }

  // just in case we need to set a mocker
  public void setMailHelper(SendMailHelper helper){
    instance = helper;
  }

} 
```

然后，在我们用来调用`SendMailHelper.method()`的类中，我们添加一个属性声明，当需要时，我们可以为测试设置它(在`initMock()`方法中)。

```
SendMailHelper sendMailHelper = SendMailHelper.getInstance(); 
```

* * *

## 关于最佳 Junit 实践的建议

**1。一次只测试一个代码单元**
当我们试图测试一个单元时，它可能有多个用例。我们应该总是在单独的测试用例中测试每个用例。例如，如果我们正在为一个函数编写测试用例，这个函数应该有两个参数，并且应该在进行一些处理后返回值，那么不同的用例可能是:

*   第一个参数可以为空。它应该抛出 InvalidParameterException。
*   第二个参数可以为空。它应该抛出 InvalidParameterException。
*   两者都可以为空。它应该抛出 InvalidParameterException。
*   最后，测试函数的有效输出。它应该返回有效的预定输出。

当您进行一些代码更改或重构时，这很有帮助:运行测试用例应该足以检查功能没有被破坏。此外，如果你改变任何行为，你需要改变一些测试用例。

**2。使每个测试独立于所有其他测试**
不要创建单元测试用例链。这将阻止您确定测试用例失败的根本原因，并且您将不得不花费时间调试代码。此外，它还会产生依赖性，这意味着如果您必须更改一个测试用例，那么您需要在多个测试用例中进行不必要的更改。

**3。模拟所有外部服务**
否则，那些外部服务的行为会与多个测试重叠，并且不同的单元测试会影响彼此的结果。

我们必须确保每个测试在运行之前都将相关的静态数据重置为已知的状态。我们必须尽量避免测试和系统之间的依赖，这样以不同的顺序运行它们就不会影响结果。

**4。给你的单元测试起一个清晰一致的名字**
这是要记住的最重要的一点。我们必须根据它们实际做什么和测试什么来命名我们的测试用例。使用类名和方法名作为测试用例名称的测试用例命名约定从来都不是一个好主意，因为每次您更改方法名或类名时，您将最终更新大量的测试用例。

但是，如果我们的测试用例名称是逻辑的，即基于操作，那么你几乎不需要修改，因为应用程序逻辑很可能保持不变。

例如，测试用例名称应该是这样的(假设 EmployeeTest 是我们的 Junit 类):

```
EmployeeTest.createWhithNullIdShouldThrowException();
EmployeeTest.createWithNegativeIdShouldThrowException();
EmployeeTest.createWithDuplicateIdShouldThrowException();
EmployeeTest.createWithValidIdShouldPass(); 
```

**5。目标是每个单元测试方法精确地执行一个断言**
我们应该尝试每个测试用例只测试一件事，所以每个测试用例使用一个断言。这样，如果某个测试用例失败了，你就能确切地知道哪里出错了。

**6。创建针对异常**
的单元测试，如果你的一些测试用例，期望从应用程序中抛出异常，使用“预期”属性。尝试避免在 catch 块中捕获异常，并使用 fail 或 assert 方法来结束这些测试。

**7。不要在单元测试中打印任何东西**
如果你正确地遵循了所有的指导方针，那么你将永远不需要在你的测试用例中添加任何打印语句。如果你觉得你需要一个，重新审视你的测试用例。

**8。从泛型类扩展以避免重写代码**
在模仿数据库连接时，尽可能多地使用泛型抽象类(例如`JdbTemplateDAO`和`JpaDAO`

**9。检查您将要创建的模拟值是否已经不存在**
当模拟与最常用的实体相关的值时，检查它们是否已经不存在于辅助类中。

**10。测试实现多个接口**
的类时，创建一个 Junit 套件。我们的测试设计是面向接口的，所以如果一个类实现多个接口，为了更容易地查看覆盖率，我们可以使用`@Suite.SuiteClasses`注释创建套件，如本例所示。

```
@RunWith(Suite.class)
@Suite.SuiteClasses({ DocumentIDocumentLocalTest.class, DocumentIDocumentTest.class })
  public class DocumentBeanTestSuite {
} 
```