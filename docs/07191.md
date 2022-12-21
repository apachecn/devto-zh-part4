# 第三部分。构建您的 Pokédex:使用 create*函数改进 NgRX

> 原文：<https://dev.to/angular/part-3-build-your-pokedex-improve-ngrx-using-create-functions-2kj4>

这篇文章是我描述如何从**初学者到忍者**使用 NGRX 构建你的 Pokédex 的系列文章的一部分，如果你想阅读更多，你可以阅读下面的文章:

*   第一部分。构建你的 pokédex:NGRX 简介
*   第二部分。构建您的 Pokédex: @ngrx/entity
*   第三部分。构建你的 Pokédex:使用 create*函数改进 NgRX】
*   第四部分。构建您的 Pokédex: @ngrx/data
*   第五部分。构建您的 Pokédex:测试 NgRX

* * *

[![cover-1](img/8103ccfd6f8d1aee844b3e90fa5f6ad0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_3gm5ojJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2A8z04ARQ6AeW6vz0OmRZFUw.png)

# 简介

在这篇文章中，我们将使用 [Angular](http://angular.io) 框架和 [NgRX](https://ngrx.io) 作为状态管理库来开发一个 **pokédex** 。我们将使用 NgRX 8 中发布的新 create*函数。

为了正确理解这篇文章，最好了解如何在中级水平上管理 Angular 以及什么是状态管理库。在这个系列中，我们将展示如何开发一个具体的例子(Pokédex ),它可以补充您的 NgRX 学习。

首先，沿着这些帖子构建的结果显示在下面的 GIF 中。

[![](img/c727daa5cdc26dd682ad432a3a8bfcec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zJ-4J1dO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/880/1%2A4rzmOje52kEmYCmSmGLPyg.gif)

阅读本系列文章的第一部分和第二部分是很重要的，这样才能理解正在构建什么。在这篇文章中，我们将通过使用`@ngrx/entity`包中的 create*函数来改进之前在本系列中开发的代码，这将简化创建动作、缩减器和效果所需的样板代码。

# 创建动作

在 NgRX 中，当您想要创建动作时，需要大量的样板代码。您经常需要创建枚举、动作类型、类和联合类型。在这个新版本中，你可以更容易地创建动作。

NgRX 核心团队使用了著名的**工厂功能**设计模式来达到这个目标。工厂函数是 **createAction** 。createAction 函数接收两个参数:

1.  *动作类型*。是用于标识操作的著名字符串。
2.  *道具*。是操作元数据。例如，**号的有效载荷**。

为了比较两者，下面的代码说明了如何在我们的 Pokédex 中使用新的 **createAction** 函数。

**之前** 

```
export class LoadPokemon implements Action {
  readonly type = PokemonActionTypes.LOAD_POKEMONS;

  constructor() {}
}

export class LoadPokemonSuccess implements Action {
  readonly type = PokemonActionTypes.LOAD_POKEMONS_SUCCESS;

  constructor(public payload: Array<Pokemon>) {}
} 
```

后**后** 

```
loadPokemonFailed = createAction(
   PokemonActionTypes.LOAD_POKEMONS_FAILED,
   props<{ message: string }>()
 ),

add: createAction(PokemonActionTypes.ADD, props<{ pokemon: Pokemon }>()), 
```

在代码之前的*中，你需要创建一个实现`Action`接口的类，使用构造函数定义`type`属性和`payload`。另一方面，在*代码之后的*中，您只需要使用`createAction`函数创建动作，其中第一个参数是`type`，第二个参数是`props`属性(在我们的上下文中，它将是 payload)。*

尽管核心团队声明不需要使用枚举，但在我的特定编码风格中，我更喜欢定义一个动作枚举来了解动作集。

因此，前的*和*后的*`pokemon.action.ts`为以下几种:* 

```
import { Action } from '@ngrx/store';
import { Pokemon } from '@models/pokemon.interface';

export enum PokemonActionTypes {
  ADD = '[Pokemon] Add',
  ADD_SUCCESS = '[Pokemon] Add success',
  ADD_FAILED = '[Pokemon] Add failed',
  LOAD_POKEMONS = '[Pokemon] Load pokemon',
  LOAD_POKEMONS_SUCCESS = '[Pokemon] Load pokemon success',
  LOAD_POKEMONS_FAILED = '[Pokemon] Load pokemon failed',
  UPDATE = '[Pokemon] Update',
  UPDATE_SUCCESS = '[Pokemon] Update success',
  UPDATE_FAILED = '[Pokemon] Update failed',
  DELETE = '[Pokemon] Delete',
  DELETE_SUCCESS = '[Pokemon] Delete success',
  DELETE_FAILED = '[Pokemon] Delete failed'
}

export class LoadPokemon implements Action {
  readonly type = PokemonActionTypes.LOAD_POKEMONS;

  constructor() {}
}

export class LoadPokemonSuccess implements Action {
  readonly type = PokemonActionTypes.LOAD_POKEMONS_SUCCESS;

  constructor(public payload: Array<Pokemon>) {}
}
export class LoadPokemonFailed implements Action {
  readonly type = PokemonActionTypes.LOAD_POKEMONS_FAILED;

  constructor(public message: string) {}
}

export class Add implements Action {
  readonly type = PokemonActionTypes.ADD;

  constructor(public pokemon: Pokemon) {}
}

export class AddSuccess implements Action {
  readonly type = PokemonActionTypes.ADD_SUCCESS;

  constructor(public pokemon: Pokemon) {}
}
export class AddFailed implements Action {
  readonly type = PokemonActionTypes.ADD_FAILED;

  constructor(public message: string) {}
}

export class Delete implements Action {
  readonly type = PokemonActionTypes.DELETE;

  constructor(public id: number) {}
}
export class DeleteSuccess implements Action {
  readonly type = PokemonActionTypes.DELETE_SUCCESS;

  constructor(public id: number) {}
}
export class DeleteFailed implements Action {
  readonly type = PokemonActionTypes.DELETE_FAILED;

  constructor(public message: string) {}
}

export class Update implements Action {
  readonly type = PokemonActionTypes.UPDATE;

  constructor(public pokemon: Pokemon) {}
}
export class UpdateSuccess implements Action {
  readonly type = PokemonActionTypes.UPDATE_SUCCESS;

  constructor(public pokemon: Pokemon) {}
}
export class UpdateFailed implements Action {
  readonly type = PokemonActionTypes.UPDATE_FAILED;

  constructor(public message: string) {}
}

export type PokemonActions =
  | LoadPokemonSuccess
  | Add
  | AddSuccess
  | AddFailed
  | Delete
  | DeleteSuccess
  | DeleteFailed
  | Update
  | UpdateSuccess
  | UpdateFailed; 
```

```
import { createAction, props } from '@ngrx/store';

import { Pokemon } from '@models/pokemon.interface';

export enum PokemonActionTypes {
  ADD = '[Pokemon] Add',
  ADD_SUCCESS = '[Pokemon] Add success',
  ADD_FAILED = '[Pokemon] Add failed',
  LOAD_POKEMONS = '[Pokemon] Load pokemon',
  LOAD_POKEMONS_SUCCESS = '[Pokemon] Load pokemon success',
  LOAD_POKEMONS_FAILED = '[Pokemon] Load pokemon failed',
  UPDATE = '[Pokemon] Update',
  UPDATE_SUCCESS = '[Pokemon] Update success',
  UPDATE_FAILED = '[Pokemon] Update failed',
  REMOVE = '[Pokemon] Delete',
  REMOVE_SUCCESS = '[Pokemon] Delete success',
  REMOVE_FAILED = '[Pokemon] Delete failed'
}

export const actions = {
  loadPokemon: createAction(PokemonActionTypes.LOAD_POKEMONS),
  loadPokemonSuccess: createAction(
    PokemonActionTypes.LOAD_POKEMONS_SUCCESS,
    props<{ pokemons: Pokemon[] }>()
  ),
  loadPokemonFailed: createAction(
    PokemonActionTypes.LOAD_POKEMONS_FAILED,
    props<{ message: string }>()
  ),
  add: createAction(PokemonActionTypes.ADD, props<{ pokemon: Pokemon }>()),
  addSuccess: createAction(
    PokemonActionTypes.ADD_SUCCESS,
    props<{ pokemon: Pokemon }>()
  ),
  addFailed: createAction(
    PokemonActionTypes.ADD_FAILED,
    props<{ message: string }>()
  ),
  remove: createAction(PokemonActionTypes.REMOVE, props<{ id: number }>()),
  removeSuccess: createAction(
    PokemonActionTypes.REMOVE_SUCCESS,
    props<{ id: number }>()
  ),
  removeFailed: createAction(
    PokemonActionTypes.REMOVE_FAILED,
    props<{ message: string }>()
  ),
  update: createAction(
    PokemonActionTypes.UPDATE,
    props<{ pokemon: Pokemon }>()
  ),
  updateSuccess: createAction(
    PokemonActionTypes.UPDATE_SUCCESS,
    props<{ pokemon: Pokemon }>()
  ),
  updateFailed: createAction(
    PokemonActionTypes.UPDATE_FAILED,
    props<{ message: string }>()
  )
}; 
```

我已经导出了一个`action` const，这是一个包含动作名称作为键和动作本身作为值的字典。

*createAction* 是一个返回函数的工厂函数，叫做 ActionCreator，调用时返回一个 Action 对象。因此，当你想调度一个动作时，你必须调用 ActionCreator】

```
this.store.dispatch(addSuccess(pokemon: Pokemon)); 
```

不再需要创建与动作的类相关联的对象，现在可以直接调用该函数。

因此，必须对创建动作的所有效果应用以下重构:

**之前** 

```
@Effect()
loadAllPokemon$: Observable<any> = this.actions$.pipe(
  ofType(PokemonActions.PokemonActionTypes.LOAD_POKEMONS),
  switchMap(() =>
    this.pokemonService.getAll().pipe(
    map(pokemons => new PokemonActions.LoadPokemonSuccess(pokemons)),
      catchError(message => of(new PokemonActions.LoadPokemonFailed(message)))
    )
  )
); 
```

后**后** 

```
@Effect()
loadAllPokemon$: Observable<any> = this.actions$.pipe(
    ofType(PokemonActions.loadPokemon),
    switchMap(() =>
      this.pokemonService.getAll().pipe(
        map(pokemons => PokemonActions.loadPokemonSuccess({ pokemons })),
        catchError(message => of(PokemonActions.loadPokemonFailed({ message }))
          )
        )
      )
    )
  ); 
```

效果本身将在下一节中使用 **createEffects** 函数进行重构。

# 创建效果

NgRx 8 提供了`createEffect`方法，它是`@Effect()`装饰器的替代方法。使用`createEffect`代替装饰器的主要优点是它是类型安全的。也就是说，如果效果没有返回一个`Observable<Action>`，它将抛出编译错误。

在下面的代码片段中，我将向您展示应用新的`createEffect`方法前后的`loadAllPokemon$`效果。迁移非常容易。

**之前** 

```
@Effect()
loadAllPokemon$: Observable<any> = this.actions$.pipe(
    ofType(PokemonActions.loadPokemon),
    switchMap(() =>
      this.pokemonService.getAll().pipe(
        map(pokemons => PokemonActions.loadPokemonSuccess({ pokemons })),
        catchError(message => of(PokemonActions.loadPokemonFailed({ message }))
          )
        )
      )
    )
  ); 
```

后**后** 

```
loadAllPokemon$ = createEffect(() =>
    this.actions$.pipe(
      ofType(PokemonActions.loadPokemon),
      switchMap(() =>
        this.pokemonService.getAll().pipe(
          map(pokemons => PokemonActions.loadPokemonSuccess({ pokemons })),
          catchError(message =>
            of(PokemonActions.loadPokemonFailed({ message }))
          )
        )
      )
    )
  ); 
```

因此，前的*和*后的*`pokemon.effects.ts`分别是:*

**之前** 

```
import * as PokemonActions from '@states/pokemon/pokemon.actions';

import { Actions, Effect, ofType } from '@ngrx/effects';
import { Observable, of } from 'rxjs';
import { catchError, map, switchMap, tap } from 'rxjs/operators';

import { Injectable } from '@angular/core';
import { MatSnackBar } from '@angular/material';
import { Pokemon } from '@shared/interfaces/pokemon.interface';
import { PokemonService } from '@services/pokemon.service';

@Injectable()
export class PokemonEffects {
  constructor(
    private actions$: Actions,
    private pokemonService: PokemonService,
    public snackBar: MatSnackBar
  ) {}

  POKEMON_ACTIONS_SUCCESS = [
    PokemonActions.PokemonActionTypes.ADD_SUCCESS,
    PokemonActions.PokemonActionTypes.UPDATE_SUCCESS,
    PokemonActions.PokemonActionTypes.DELETE_SUCCESS,
    PokemonActions.PokemonActionTypes.LOAD_POKEMONS_SUCCESS
  ];

  POKEMON_ACTIONS_FAILED = [
    PokemonActions.PokemonActionTypes.ADD_FAILED,
    PokemonActions.PokemonActionTypes.UPDATE_FAILED,
    PokemonActions.PokemonActionTypes.DELETE_FAILED,
    PokemonActions.PokemonActionTypes.LOAD_POKEMONS_FAILED
  ];

  @Effect()
  loadAllPokemon$: Observable<any> = this.actions$.pipe(
    ofType(PokemonActions.PokemonActionTypes.LOAD_POKEMONS),
    switchMap(() =>
      this.pokemonService.getAll().pipe(
        map(response => new PokemonActions.LoadPokemonSuccess(response)),
        catchError(error => of(new PokemonActions.LoadPokemonFailed(error)))
      )
    )
  );

  @Effect()
  addPokemon$: Observable<any> = this.actions$.pipe(
    ofType(PokemonActions.PokemonActionTypes.ADD),
    switchMap((action: any) =>
      this.pokemonService.add(action.pokemon).pipe(
        map((pokemon: Pokemon) => new PokemonActions.AddSuccess(pokemon)),
        catchError(error => of(new PokemonActions.AddFailed(error)))
      )
    )
  );

  @Effect()
  deletePokemon$: Observable<any> = this.actions$.pipe(
    ofType(PokemonActions.PokemonActionTypes.DELETE),
    switchMap(({ id }) =>
      this.pokemonService.delete(id).pipe(
        map(() => new PokemonActions.DeleteSuccess(id)),
        catchError(error => of(new PokemonActions.DeleteFailed(error)))
      )
    )
  );

  @Effect()
  updatePokemon$: Observable<any> = this.actions$.pipe(
    ofType(PokemonActions.PokemonActionTypes.UPDATE),
    switchMap(({ pokemon }) =>
      this.pokemonService.update(pokemon).pipe(
        map(() => new PokemonActions.UpdateSuccess(pokemon)),
        catchError(error => of(new PokemonActions.UpdateFailed(error)))
      )
    )
  );

  @Effect({ dispatch: false })
  successNotification$ = this.actions$.pipe(
    ofType(...this.POKEMON_ACTIONS_SUCCESS),
    tap(() =>
      this.snackBar.open('SUCCESS', 'Operation success', {
        duration: 2000
      })
    )
  );
  @Effect({ dispatch: false })
  failedNotification$ = this.actions$.pipe(
    ofType(...this.POKEMON_ACTIONS_FAILED),
    tap(() =>
      this.snackBar.open('FAILED', 'Operation failed', {
        duration: 2000
      })
    )
  );
} 
```

后**后** 

```
import { Actions, createEffect, ofType } from '@ngrx/effects';
import { catchError, map, switchMap, tap } from 'rxjs/operators';

import { Injectable } from '@angular/core';
import { MatSnackBar } from '@angular/material';
import { Pokemon } from '@shared/interfaces/pokemon.interface';
import { actions as PokemonActions } from '@states/pokemon/pokemon.actions';
import { PokemonService } from '@services/pokemon.service';
import { of } from 'rxjs';

@Injectable()
export class PokemonEffects {
  constructor(
    private actions$: Actions,
    private pokemonService: PokemonService,
    public snackBar: MatSnackBar
  ) {}

  POKEMON_ACTIONS_SUCCESS = [
    PokemonActions.addSuccess,
    PokemonActions.updateSuccess,
    PokemonActions.removeSuccess,
    PokemonActions.loadPokemonSuccess
  ];

  POKEMON_ACTIONS_FAILED = [
    PokemonActions.addFailed,
    PokemonActions.updateFailed,
    PokemonActions.removeFailed,
    PokemonActions.loadPokemonFailed
  ];

  loadAllPokemon$ = createEffect(() =>
    this.actions$.pipe(
      ofType(PokemonActions.loadPokemon),
      switchMap(() =>
        this.pokemonService.getAll().pipe(
          map(pokemons => PokemonActions.loadPokemonSuccess({ pokemons })),
          catchError(message =>
            of(PokemonActions.loadPokemonFailed({ message }))
          )
        )
      )
    )
  );

  addPokemon$ = createEffect(() =>
    this.actions$.pipe(
      ofType(PokemonActions.add),
      switchMap((action: any) =>
        this.pokemonService.add(action.pokemon).pipe(
          map((pokemon: Pokemon) => PokemonActions.addSuccess({ pokemon })),
          catchError(message => of(PokemonActions.addFailed({ message })))
        )
      )
    )
  );

  deletePokemon$ = createEffect(() =>
    this.actions$.pipe(
      ofType(PokemonActions.remove),
      switchMap(({ id }) =>
        this.pokemonService.delete(id).pipe(
          map(() => PokemonActions.removeSuccess({ id })),
          catchError(message => of(PokemonActions.removeFailed({ message })))
        )
      )
    )
  );

  updatePokemon$ = createEffect(() =>
    this.actions$.pipe(
      ofType(PokemonActions.update),
      switchMap(({ pokemon }) =>
        this.pokemonService.update(pokemon).pipe(
          map(() => PokemonActions.updateSuccess({ pokemon })),
          catchError(message => of(PokemonActions.updateFailed(message)))
        )
      )
    )
  );

  successNotification$ = createEffect(
    () =>
      this.actions$.pipe(
        ofType(...this.POKEMON_ACTIONS_SUCCESS),
        tap(() =>
          this.snackBar.open('SUCCESS', 'Operation success', {
            duration: 2000
          })
        )
      ),
    { dispatch: false }
  );

  failedNotification$ = createEffect(
    () =>
      this.actions$.pipe(
        ofType(...this.POKEMON_ACTIONS_FAILED),
        tap(() =>
          this.snackBar.open('FAILED', 'Operation failed', {
            duration: 2000
          })
        )
      ),
    { dispatch: false }
  );
} 
```

注意，之前传递给每个效果的参数`dispatch: false`现在是传递给`createEffect`方法的第二个参数。记住选项`{ dispatch: false }`是用于不分派新动作的效果，增加这个选项也消除了效果必须返回一个`Observable<Action>`的限制。

# 减速器

新的`createReducer`方法允许创建一个没有`switch`语句的 reducer。有一个新的`on`方法来区分动作类型，它返回一个新的状态引用。另一个有趣的事实是不需要处理 reducer 中未处理动作的默认情况。

因此，前的*和*后的*`pokemon.reducers.ts`分别是:*

**之前** 

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

后**后** 

```
import { Action, createReducer, on } from '@ngrx/store';
import { PokemonState, pokemonAdapter } from './pokemon.adapter';

import { actions as PokemonActions } from './pokemon.actions';

export function pokemonInitialState(): PokemonState {
  return pokemonAdapter.getInitialState();
}

const pokemonReducer = createReducer(
  pokemonInitialState(),
  on(PokemonActions.loadPokemonSuccess, (state, { pokemons }) =>
    pokemonAdapter.addAll(pokemons, state)
  ),
  on(PokemonActions.addSuccess, (state, { pokemon }) =>
    pokemonAdapter.addOne(pokemon, state)
  ),
  on(PokemonActions.removeSuccess, (state, { id }) =>
    pokemonAdapter.removeOne(id, state)
  ),
  on(PokemonActions.updateSuccess, (state, { pokemon }) =>
    pokemonAdapter.updateOne({ id: pokemon.id, changes: pokemon }, state)
  )
);

export function reducer(state: PokemonState | undefined, action: Action) {
  return pokemonReducer(state, action);
} 
```

注意，`createReducer`方法接收一个参数列表:
第一个参数是初始状态，第二个参数是一个`on`方法列表。在`on`方法中，第一个参数是相关的动作。在我的例子中，我维护了动作`enum`，因为我喜欢这种数据结构。当然，您可以不使用枚举直接导出操作。`on`方法的第二个参数是一个回调函数，在这个回调函数中接收到了`state`和`payload`。之后，我们可以使用强大的`EntityAdapter`来执行最常见的操作。

# 结论

在这篇文章中，我们通过使用`@ngrx/entity`包的`create*`函数重构了我们的 Pokédex。create*函数的使用将减少应用程序状态管理中不必要的复杂性。此外，适配器用于执行最常见的操作(CRUD)。

因此，在这篇文章中，我们讨论了以下主题:

*   通过使用@ngrx/entity，自动创建状态，因为它是非常重复的。
*   使用`@ngrx/entity`自动创建效果、动作并简化 reduce 功能。

本系列中的以下帖子将涵盖有趣的主题，例如:

*   门面模式将通过`@ngrx/data`包使用。
*   测试应用程序的状态。

这篇文章最重要的部分是展示的概念，而不是使用的技术或库。因此，对于那些开始有大角度应用并需要应用架构原则的人来说，这篇文章应该作为一个指南。

# 越来越多，越来越多...

*   [annuning ngrix 8](https://medium.com/ngrx/announcing-ngrx-version-8-ngrx-data-create-functions-runtime-checks-and-mock-selectors-a44fac112627)
*   [角度架构最佳实践](https://angular-academy.com/angular-architecture-best-practices/)
*   [角度架构- ng-conf](https://www.ng-conf.org/2019/angular-architecture-specific-layers/)
*   [棱角分明的建筑(官方文件)](https://angular.io/guide/architecture)
*   [NGRX](https://ngrx.io/)
*   [RxJS](https://rxjs-dev.firebaseapp.com/)
*   [立面图案](https://www.carloscaballero.io/design-patterns-facade/)

该岗位的 *GitHub 分支*为 https://github.com/Caballerog/ngrx-pokedex/tree/ngrx-part3**********