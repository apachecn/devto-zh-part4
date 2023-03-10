# 构建您的 Pokédex:第 2 部分- @ngrx/entity

> 原文：<https://dev.to/angular/build-your-pokedex-part-2-ngrx-entity-1cji>

这篇文章是我描述如何从**初学者到忍者**使用 NGRX 构建你的 Pokédex 的系列文章的一部分，如果你想阅读更多，你可以阅读下面的文章:

*   第一部分。构建你的 pokédex:NGRX 简介
*   第二部分。构建您的 Pokédex: @ngrx/entity
*   第三部分。构建您的 Pokédex:使用 create*函数改进 NgRX
*   第四部分。构建您的 Pokédex: @ngrx/data
*   第五部分。构建您的 Pokédex:测试 NgRX

* * *

[![cover-1](img/8103ccfd6f8d1aee844b3e90fa5f6ad0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_3gm5ojJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2A8z04ARQ6AeW6vz0OmRZFUw.png)

# 简介

在这篇文章中，我们将使用 [Angular](http://angular.io) 框架和 [NgRX](https://ngrx.io) 作为状态管理库来开发一个 **pokédex** 。

建议了解如何在中级水平上管理 Angular，并了解什么是状态管理库，以便正确理解这篇文章，因为在本系列中，我们将展示如何开发一个具体的示例(Pokédex)，这可以作为您 NgRX 学习的补充。

首先，沿着这些帖子构建的结果显示在下面的 GIF 中。

[![](img/c727daa5cdc26dd682ad432a3a8bfcec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zJ-4J1dO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/880/1%2A4rzmOje52kEmYCmSmGLPyg.gif)

有必要阅读这篇文章的第一部分，以完全理解正在建造的东西。在这篇文章中，我们将使用`@ngrx/entity`包改进第一篇文章中开发的代码，这将简化创建缩减器和选择器的任务。

# @ngrx/entity

`@ngrx/entity`包是一个管理记录集合的适配器。这个包提供了一个 API 来操作和查询实体集合。

因此，减少了创建管理模型集合的 reducer 的样板文件。此外，它为管理实体集合提供了高性能的 CRUD 操作。最后，它是用于选择实体信息的可扩展类型安全适配器。

第一步是安装将提供所有这些优势的软件包。

```
npm i @ngrx/entity 
```

## [实体状态](#entitystate)

实体状态是给定实体集合的预定义通用接口，具有以下接口:

```
interface EntityState<V> {
  ids: string[] | number[];
  entities: { [id: string | id: number]: V };
} 
```

在上一篇文章中，我们开发具有这两个属性的`PokemonState`的原因如下所示。通用属性如下:

*   **id**。集合中所有主要 id 的数组。
*   **实体**。集合中由主 id 索引的实体的字典。

如下图所示，`pokemon.state.ts`文件被替换为`pokemon.adapter.ts`。

**之前** 

```
import { Pokemon } from '@shared/interfaces/pokemon.interface';
export interface PokemonState {
  ids: number[];
  entities: { [key: string]: Pokemon };
} 
```

后**后** 

```
import { EntityState } from '@ngrx/entity';
import { Pokemon } from '@shared/interfaces/pokemon.interface';
import { createEntityAdapter } from '@ngrx/entity';

export const pokemonAdapter = createEntityAdapter<Pokemon>();

export interface PokemonState extends EntityState<Pokemon> {} 
```

在我们的新模型中，我们已经使用继承基于`EntityState`创建了别名`PokemonState`，尽管我们没有为状态添加任何新的属性。

另一方面，`createEntityAdapter`方法负责为我们的`Pokemon`数据模型创建适配器。这个适配器为我们提供了修改状态和创建选择器的方法集合。

该方法接受一个具有两个属性的对象进行配置。

*   **选择 Id** 。为集合选择主 id 的方法。当实体的主键为 id 时，该选项是可选的。
*   **排序比较器**。用于对集合排序的比较函数。只有当集合需要在显示之前进行排序时，才需要比较功能。设置为 false 将使集合不排序，这在 CRUD 操作期间更有性能。

在我们的例子中，我们没有使用任何配置属性，因为它是不必要的。

## 减速器

返回的适配器对象提供了一组方法，可以在 reducer 函数中使用这些方法来根据您提供的操作管理实体集合。

*   **getInitialState** 基于提供的类型返回实体状态的 initialState。initialState 提供给你的 reducer 函数。在我们的例子中，这个函数是`pokemonInitialState`内部的包装器。

```
export function pokemonInitialState(): PokemonState {
  return pokemonAdapter.getInitialState();
} 
```

**适配器集合方法**
实体适配器还提供了对实体进行操作的方法。这些方法可以一次更改一个到多个记录。如果进行了更改，每个方法都返回新修改的状态，如果没有进行更改，则返回相同的状态。

*   **addOne** :向集合中添加一个实体
*   **addMany** :向集合中添加多个实体
*   **addAll** :用提供的收藏替换当前收藏
*   **removeOne** :从集合中删除一个实体
*   **removeMany** :通过 id 或谓词从集合中删除多个实体
*   **removeAll** :清除实体集合
*   **updateOne** :更新集合中的一个实体
*   **updateMany** :更新集合中的多个实体
*   **upsertOne** :添加或更新集合中的一个实体
*   **upsertMany** :添加或更新集合中的多个实体
*   **map** :通过定义一个 map 函数来更新集合中的多个实体，类似于 Array.map

这组方法允许我们简化 reduce 函数，在这个函数中我们有复杂的嵌套对象来修改属性。这样，请注意这个函数的前后，实际上代码已经减少到调用一个方法。

**之前** 

```
import { PokemonActionTypes, PokemonActions } from './pokemon.actions';

import { PokemonState } from './pokemon.adapter';

export function pokemonInitialState(): PokemonState {
  return {
    ids: [],
    entities: {}
  };
}

function arrayToObject(array) {
  return array.reduce((obj, item) => {
    obj[item.id] = item;
    return obj;
  }, {});
}

export function pokemonReducer(
  state: PokemonState = pokemonInitialState(),
  action: PokemonActions
): PokemonState {
  switch (action.type) {
    case PokemonActionTypes.LOAD_POKEMONS_SUCCESS:
      return {
        ...state,
        entities: arrayToObject(action.payload)
      };

    case PokemonActionTypes.ADD_SUCCESS:
      return {
        ...state,
        entities: {
          ...state.entities,
          [action.pokemon.id]: action.pokemon
        }
      };

    case PokemonActionTypes.DELETE_SUCCESS:
      const entities = { ...state.entities };
      delete entities[action.id];
      return {
        ...state,
        entities
      };

    case PokemonActionTypes.UPDATE_SUCCESS:
      return {
        ...state,
        entities: {
          ...state.entities,
          [action.pokemon.id]: action.pokemon
        }
      };

    default:
      return state;
  }
} 
```

后**后** 

```
import { PokemonActionTypes, PokemonActions } from './pokemon.actions';
import { PokemonState, pokemonAdapter } from './pokemon.adapter';

export function pokemonInitialState(): PokemonState {
  return pokemonAdapter.getInitialState();
}

export function pokemonReducer(
  state: PokemonState = pokemonInitialState(),
  action: PokemonActions
): PokemonState {
  switch (action.type) {
    case PokemonActionTypes.LOAD_POKEMONS_SUCCESS:
      return pokemonAdapter.addAll(action.payload, state);

    case PokemonActionTypes.ADD_SUCCESS:
      return pokemonAdapter.addOne(action.pokemon, state);

    case PokemonActionTypes.DELETE_SUCCESS:
      return pokemonAdapter.removeOne(action.id, state);

    case PokemonActionTypes.UPDATE_SUCCESS:
      const { id } = action.pokemon;
      return pokemonAdapter.updateOne(
        {
          id,
          changes: action.pokemon
        },
        state
      );

    default:
      return state;
  }
} 
```

## 选择器

由创建的实体适配器返回的 **getSelectors** 方法提供了从实体中选择信息的函数。

因此，最广泛使用的四个选择器是可用的:

```
export interface EntitySelectors<T, V> {
    selectIds: (state: V) => string[] | number[];
    selectEntities: (state: V) => Dictionary<T>;
    selectAll: (state: V) => T[];
    selectTotal: (state: V) => number;
} 
```

最后，`pokemon.selector.ts`文件被稍微修改了一下，因为我们不需要构建`selectAll`选择器，因为我们将使用适配器提供的选择器。

**之前** 

```
import { createFeatureSelector, createSelector } from '@ngrx/store';

import { PokemonState } from './pokemon.adapter';

export const selectPokemonState = createFeatureSelector<PokemonState>(
  'pokemon'
);

export const selectAll = createSelector(
  selectPokemonState,
  state => Object.values(state.entities)
); 
```

后**后** 

```
import { PokemonState, pokemonAdapter } from './pokemon.adapter';
import { createFeatureSelector, createSelector } from '@ngrx/store';

export const selectPokemonState = createFeatureSelector<PokemonState>(
  'pokemon'
);

export const {
  selectIds,
  selectEntities,
  selectAll,
  selectTotal
} = pokemonAdapter.getSelectors(selectPokemonState); 
```

# 结论

在这篇文章中，我们使用`@ngrx/entity`包重构了 Pokédex 的小例子。适配器的使用将减少应用程序状态管理中不必要的复杂性。适配器允许我们根据需要轻松地扩展状态，并且在处理应用程序的状态时具有最常用的操作。

因此，在这篇文章中，我们讨论了以下主题:

*   分离可视组件的状态管理。
*   高效、轻松地创建管理状态的元素。
*   创建关注相关:**视图**的组件。
*   自动创建状态，因为使用@ngrx/entity 会非常重复。

本系列中的以下帖子将涵盖有趣的主题，例如:

*   使用`@ngrx/entity`自动创建效果、动作并简化 reduce 功能。
*   门面模式将通过`@ngrx/data`包使用。
*   测试应用程序的状态。

真正重要的是概念，而不是使用的技术或库。因此，对于那些开始有大角度应用并需要应用架构原则的人来说，这篇文章应该作为一个指南。

# 越来越多，越来越多...

*   [角度架构最佳实践](https://angular-academy.com/angular-architecture-best-practices/)
*   [角度架构- ng-conf](https://www.ng-conf.org/2019/angular-architecture-specific-layers/)
*   [棱角分明的建筑(官方文件)](https://angular.io/guide/architecture)
*   [NGRX](https://ngrx.io/)
*   [理解记忆](https://www.carloscaballero.io/understanding-javascript-typescript-memoization/)
*   [RxJS](https://rxjs-dev.firebaseapp.com/)
*   [立面图案](https://www.carloscaballero.io/design-patterns-facade/)

该岗位的 *GitHub 分支*为 https://github.com/Caballerog/ngrx-pokedex/tree/ngrx-part2******