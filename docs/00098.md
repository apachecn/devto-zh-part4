# 用 CRNK 创建 REST API，第 2 部分

> 原文：<https://dev.to/nchrisz/creating-rest-api-with-crnk-part-2-4m1h>

在第 1 部分中，我们用 Spring Framework 和 CRNK 创建了一个基本的 REST 应用程序。我们有一个基本模型，说明我们的数据在数据库中的代表性，外部用户可以获取、发布或放置这些数据。

## 外部面向数据

在上一篇文章中，我们只处理了一个模型，知道外部请求的每个信息都应该完整地给出。但是，如果您构建一个 API，其中的模型可能包含外部用户不应该看到的信息，比如您使用一些数据作为过滤器的基础，那么有一种方法可以隐藏这些信息。创建了两个模型，一个供外部使用，一个供存储库使用。然后，将创建一个映射器来链接外部模型和内部模型。

这是通过实现 JpaModuleConfigurator 和覆盖 configure 来完成的。该参数是 JpaModuleConfig，它允许您配置 CRNK 应用程序。下面我们添加一个存储库，在这里我们用一个映射器绑定外部和内部两个模型。

```
 package com.test.store.configuration;

import com.test.store.model.ModelMapper;
import com.test.store.model.ModelExternal;
import com.test.store.model.ModelEntity;
import io.crnk.jpa.JpaModuleConfig;
import io.crnk.jpa.JpaRepositoryConfig;
import io.crnk.spring.setup.boot.jpa.JpaModuleConfigurer;
import org.springframework.stereotype.Component;

@Component
public class JpaModuleConfigurator implements JpaModuleConfigurer {

  @Override
  public void configure(JpaModuleConfig jpaModuleConfig) {
       jpaModuleConfig.addRepository(JpaRepositoryConfig.builder(
                                     ModelEntity.class,
                                     ModelExternal.class, new ModelMapper())
                      .build());
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

为了让 JpaModuleConfigurator 使用映射器，它需要实现 JpaMapper，在 JPA mapper 中指定两个模型，并覆盖在它们之间跳转的方法。

```
 package com.test.store.model;

import com.test.store.model.ModelExternal;
import com.test.store.model.ModelEntity;
import io.crnk.jpa.mapping.JpaMapper;
import io.crnk.jpa.query.Tuple;
import org.springframework.stereotype.Component;
import javax.persistence.EntityManager;
import javax.persistence.PersistenceContext;

@Component
public class ModelMapper implements JpaMapper<ModelEntity, ModelExternal> {

  @PersistenceContext
  private EntityManager entityManager;@Override
  public ModelExternal map(Tuple tuple) {
    ModelExternal dto = new ModelExternal();

    ModelEntity entity = tuple.get(0, ModelEntity.class);
    dto.setData(entity.getData());
    return dto;
  }

  @Override
  public ModelEntity unmap(ModelExternal dto) {
    ModelEntity entity;
    if (dto.getId() == null) {
      entity = new ModelEntity();
    } else {
      entity = entityManager.find(ModelEntity.class, dto.getId());
      if (entity == null) {
        throw new RuntimeException("Couldn't find entity");
      }
    }
    return translateToEntity(dto, entity);
  }

  private ModelEntity translateToEntity(ModelExternal dto, ModelEntity entity) {
    entity.setData(dto.getData);
    return entity;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

外部用户仍然可以对模型使用 CRNK 默认搜索查询，但您可以控制将看到的内容。如果您有想要隐藏的数据，可以在映射器中控制它，而在外部模型中您没有该数据。

## 修改仓库数据

上面给出了一个机会，向外部用户隐藏可能存在于数据库中的数据。一个疑问和问题可能是，数据库中不存在的信息是否将被呈现给用户。如果基于对 API 的查询，我们希望将响应改回来，该怎么办？

为此，我们不能用 JpaModuleConfigurator 连接这两个模型。这两种模型都需要有自己的存储库，其中面向内部的存储库是连接到数据库的存储库。面向外部的存储库将保存面向内部的存储库，作为在不同调用中使用的属性。

对于这个例子，我们假设已经对 API 进行了一个带有年龄的查询，我们希望将每个人及其年龄差返回给该查询。我们的 ModelExternal 的新属性是

`private int ageDifference;`

在前一篇文章中，我们将数据保存在内存中。这一次我们将创建一个存储库，它连接到一个保存数据的数据库。如前所述，我们声明了存储库使用的模型以及标识符的属性。

```
 package com.test.store.repository;

import com.test.store.model.ModelEntity;
import org.springframework.data.jpa.repository.Query;
import org.springframework.data.repository.CrudRepository;
import org.springframework.data.repository.query.Param;
import org.springframework.stereotype.Service;import java.util.List;
import java.util.UUID;

@Service
public interface ModelEntityCrudRepository extends CrudRepository<ModelEntity, UUID> {
} 
```

Enter fullscreen mode Exit fullscreen mode

与 ResourceRepositoryBase 相比，这个存储库不需要保存太多内容，因为 CrudRepository 是数据库的通信器，我们对这里的任何更改都不感兴趣。如果你想在你的 API 中对数据库进行特定的调用，你可以创建特定的数据库查询，例如

```
@Query("select s from model s where partner_id = :partnerId")  
List<StoreEntity> findByPartnerId(@Param("partnerId") String partnerId); 
```

Enter fullscreen mode Exit fullscreen mode

面向外部的存储库设置起来会有点棘手。我们需要将它定义为一个 CRNK 存储库，但是也要将它与我们上面的 ModelEntity 连接起来。首先，我们创建一个扩展 ResourceRepositoryV2 的接口，供我们使用 CRNK 默认查询。

```
 package com.test.store.repository;

import com.test.store.model.store.Store;
import io.crnk.core.repository.ResourceRepositoryV2;
import java.util.UUID;

public interface ModelExternalRepository extends 
                             ResourceRepositoryV2<ModelExternal, UUID> {} 
```

Enter fullscreen mode Exit fullscreen mode

接下来是主存储库。

```
 package com.test.store.repository;

import com.test.store.model.ModelExternal;
import com.test.store.model.ModelEntity;
import com.test.store.translator.ModelTranslator;
import io.crnk.core.boot.CrnkBoot;
import io.crnk.core.exception.BadRequestException;
import io.crnk.core.exception.RepositoryNotFoundException;
import io.crnk.core.queryspec.QuerySpec;
import io.crnk.core.repository.ResourceRepositoryBase;
import io.crnk.jpa.JpaEntityRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.ApplicationContext;
import org.springframework.stereotype.Component;
import java.util.Comparator;
import java.util.List;
import java.util.UUID;
import java.util.stream.Collectors;

@Component
public class ModelExternalRepositoryImpl extends 
                               ResourceRepositoryBase<ModelExternal, UUID> implements ModelExternalRepository {

  private JpaEntityRepository modelEntityJpaRepository;@Autowired
  ApplicationContext appContext;

  public ModelExternalRepositoryImpl() {
    super(ModelExternal.class);
    modelEntityJpaRepository = null;
  }

  @Override
  public ModelExternal findOne(UUID id, QuerySpec querySpec) {
    if (modelEntityJpaRepository == null) {
      initJpaRepositoryEntity();
    }
    return ModelTranslator.map((ModelEntity)  modelEntityJpaRepository.findOne(id, querySpec));
  }

  @Override
  public ModelExternalResourceList findAll(QuerySpec querySpec) {
    if (modelEntityJpaRepository == null) {
      initJpaRepositoryEntity();
    }
    ResourceList<ModelEntity> modelEntityResourceList = modelEntityJpaRepository.findAll(querySpec);
    ...

modelResourceList.addAll(ModelTranslator.translate(modelEntityResourceList));
    ...

    return modelResourceList;
  }

  /**
   * Initialize the JpaRepository.
  */
  private void initJpaRepositoryEntity() {
    CrnkBoot crnkBoot = appContext.getBean(CrnkBoot.class);
    modelEntityJpaRepository = crnkBoot.getModuleRegistry()
                                           .getRepositories()
                                           .stream()
                                           .filter(repository -> repository instanceof JpaEntityRepository)
                                           .map(repository -> (JpaEntityRepository) repository)
                                           .filter(repository -> repository.getEntityClass().equals(ModelEntity.class)
                                                           && repository.getResourceClass().equals(ModelEntity.class))
                                           .findFirst()
                                           .orElseThrow(() -> new RepositoryNotFoundException("ModelEntity JpaRepository not found"));
    }

  public static class ModelResourceList extends ResourceListBase<ModelExternal, MetaInformation, LinksInformation> {}
} 
```

Enter fullscreen mode Exit fullscreen mode

我们将按重要性来划分。

这里比较重要的一点是 initJpaRepositoryEntity。这是因为当存储库被初始化时，它不能被控制，自动连接内部存储库可能会抛出错误，说它还不存在。为了避免这种情况，对数据的第一个调用将是两个存储库。所发生的是，它将搜索连接到数据库的 ModelEntity 存储库，并加载到该存储库中。

逐步打破:

1.  首先，我们搜索 CrnkBoot bean，它将包含我们所有的存储库。
2.  加载所有模块，用 CrnkBoot 注册并获取所有的库
3.  从浏览它们开始，寻找每个作为 JpaRepository 的存储库
4.  对于每个 JpaRepository，查找连接到我们的 ModelEntity 类的 JPA repository

在所有这些步骤之后，我们将数据库存储库加载到了面向外部的存储库中。

一个外部调用将命中我们的面向外部的存储库，它连接到我们的数据库存储库。它将从查询中获取信息(如果需要，您可以通过创建新的并将其发送到内部存储库来修改查询)。我们从中获取一些数据，并将其转化为面向外部的数据。通过翻译，我们可以添加我们希望外部用户看到的数据。

以上只是将数据从外部用户和内部用户中分离出来的两种方式。我们仍然保留默认的 CRNK 行为，包括过滤和排序。使用两个存储库和排序的最后一个技巧是修改对存储库的查询，然后获得比您需要的更多的数据。对外部存储库中的数据进行排序，只返回一组数据。

## 添加新的过滤

在第 1 部分中，我们讨论了过滤以及标准过滤 CRNK 将为您提供什么。但是以后您可能希望为存储库保存的特定数据添加过滤。

要添加过滤器，您需要扩展 filterOperator

```
 package com.test.store.filter;

import io.crnk.core.queryspec.FilterOperator;

public class PersonFilterOperator extends FilterOperator {

  public PersonFilterOperator(String id) {
    super(id);
  }

  /**
   * Handle new filterOperator. Should in default pass if value2 (value given by request) is given.
   * @param value1 value from DB
   * @param value2 value from request
   * @return true if value from request isn't null
  */
  @Override
  public boolean matches(Object value1, Object value2) {
    /*
     This class and this method is only used for new FilterOperators. FilterOperators are used in making a request and using crnk filtering.
     Value 1 is the data from the DB and Value 2 is from the request. FilterOperator.LIKE may first check if value2 exist then later on
     check value1 == value2\. As we don't want to check values on that level we are only interested to check if the user provided a value.
     */
    return value2 != null;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的过滤器除了检查随请求传递的值是否包含数据之外，什么也不做。但是您可以修改方法匹配，以适合您希望您的过滤做什么。现在，仅仅添加这个类不会将过滤添加到您的应用程序中，但是您需要将它添加到 Url 映射器中。

```
 package com.test.store.configuration;

import com.test.store.filter.StoreFilterOperator;
import io.crnk.core.queryspec.FilterOperator;
import io.crnk.core.queryspec.mapper.DefaultQuerySpecUrlMapper;
import org.springframework.stereotype.Component;

@Component
public class PersonQuerySpec extends DefaultQuerySpecUrlMapper {

  public static final FilterOperator WITHIN= new PersonFilterOperator("WITHIN");
  public static final FilterOperator NEAR = new PersonFilterOperator("NEAR");
  public ModelQuerySpec() {
    super.addSupportedOperator(WITHN);
    super.addSupportedOperator(NEAR);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们向 DefaultQuerySpecUrlMapper 添加了一个新的 filterOperator，这样当一个调用使用新的过滤器时，CRNK 就知道如何处理它了。上面的例子使用了与上面相同的类，它除了使用
进行过滤调用之外什么也不做

```
/person?filter[NEAR][age]=25 
```

Enter fullscreen mode Exit fullscreen mode

作品。如第 1 部分所述，使用您自己的 filterOperator 时，重要的是过滤器在对 API 的请求和从存储库到底层数据结构的查询之间工作。这意味着如果使用特定的 filterOperator，您将必须实现一个 repository 类来解包您的 querySpec 并修改请求。

## 总结

正如本文所指出的，用 CRNK 做更多具体的事情可能会成为一个问题。CRNK 帮助连接两个模型，外部的和内部的，以帮助隐藏数据，但是如果在响应中需要数据的话，就变得更加棘手了。就我们所见，没有一种方法可以控制如何加载存储库。这意味着，要让不同的存储库互相交流，需要部署黑客。

CRNK 目前相对较新，所以希望其中一些东西可以被简化，社区可以围绕它成长。