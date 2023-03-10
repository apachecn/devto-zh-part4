# Angular & Akita 中的一对多关系介绍

> 原文：<https://dev.to/arielgueta/introducing-one-to-many-relationship-in-angular-akita-37me>

在这篇文章中，我将向你展示如何用 Angular 和 Akita 创建一个博客。在这个过程中，我们将学习两种策略，可以用来管理与秋田的一对多关系。

我们的演示应用程序将包含一个显示文章列表的主页和一个显示完整文章及其评论的文章页面。我们将添加添加、编辑和删除评论的功能。所以我们的一对多关系，在这种情况下，是“一篇文章有很多评论”或者“一个评论属于一篇文章”。

让我们看看如何解决这个问题，但是首先，让我们看看从服务器得到的响应形状:

```
[{
  id: string;
  title: string;
  content: string;
  comments: [{
    id: string;
    text: string;
  }]
}] 
```

我们得到一个文章数组，其中每篇文章在一个`comments`属性中保存其评论。

## 策略一——非标准化数据

我们将从非标准化数据版本开始。这意味着我们将按原样使用服务器响应，而不修改它。我们将使用一个存储，即一个`ArticleStore`来存储文章及其评论。让我们看看它的实际效果。

首先，我们需要将 Akita 添加到我们的项目:

```
ng add @datorama/akita 
```

上面的命令将 Akita、Akita 的开发工具和 Akita 的原理图添加到我们的项目中。下一步是创建一个商店。我们需要维护一个文章集合，所以我们搭建了一个新的实体特性:

```
ng g af articles 
```

这个命令为我们生成一个文章存储、一个文章查询、一个文章服务和一个文章模型:

```
// article.model

import { ID } from '@datorama/akita';

export interface ArticleComment {
  id: ID;
  text: string;
}

export interface Article {
  id: ID;
  title: string;
  content: string;
  comments: ArticleComment[];
}

// articles.store
export interface ArticlesState extends EntityState<Article> {}

@Injectable({ providedIn: 'root' })
@StoreConfig({ name: 'articles' })
export class ArticlesStore extends EntityStore<ArticlesState, Article> {
  constructor() { super() }
}

// articles.query
@Injectable({ providedIn: 'root' })
export class ArticlesQuery extends QueryEntity<ArticlesState, Article> {

  constructor(protected store: ArticlesStore) {
    super(store);
  }
} 
```

现在，让我们定义我们的路线:

```
const routes: Routes = [
  {
    component: HomePageComponent,
    path: '',
    pathMatch: 'full'
  },
  {
    component: ArticlePageComponent,
    path: ':id'
  }
]; 
```

让我们创建`HomePageComponent` :

```
@Component({
  templateUrl: './homepage.component.html',
  styleUrls: ['./homepage.component.css']
})
export class HomepageComponent implements OnInit {
  articles$ = this.articlesQuery.selectAll();
  loading$ = this.articlesQuery.selectLoading();

  constructor(private articlesService: ArticlesService, 
              private articlesQuery: ArticlesQuery) {
  }

  ngOnInit() {
    !this.articlesQuery.getHasCache() && this.articlesService.getAll();
  }
} 
```

我们使用内置的 Akita 查询选择器。`selectAll`选择器被动地从商店获取商品，而`selectLoading`选择器指示我们是否需要显示一个微调器。

在`ngOnInit`钩子中，我们调用服务的`getAll`方法，该方法从服务器获取文章并将它们添加到商店。

```
@Injectable({ providedIn: 'root' })
export class ArticlesService {

  constructor(private store: ArticlesStore,
              private http: HttpClient) {
  }

  async getAll() {
    const response = await this.http.get('url').toPromise();
    this.store.set(response.data);
  }
} 
```

在我们的例子中，我们只想获取它们一次，所以我们使用内置的`getHasCache()`来检查我们的存储中是否有数据。当我们调用存储的`set`方法时，内部存储的缓存属性值自动更改为`true`。现在，我们可以构建模板:

```
<section class="container">
  <h1>Blog</h1>

  <h3 *ngIf="loading$ | async; else content">Loading...</h3>

  <ng-template #content>
    <app-article-preview *ngFor="let article of articles$ | async;"
                         [article]="article"></app-article-preview>
  </ng-template>

</section> 
```

让我们转到文章页面组件。

```
@Component({
  templateUrl: './article-page.component.html',
  styleUrls: ['./article-page.component.css']
})
export class ArticlePageComponent implements OnInit {
  article$: Observable<Article>;
  articleId: string;
  selectedComment: ArticleComment = {} as ArticleComment;

  constructor(private route: ActivatedRoute,
              private articlesService: ArticlesService,
              private articlesQuery: ArticlesQuery) {
  }

  ngOnInit() {
    this.articleId = this.route.snapshot.params.id;
    this.article$ = this.articlesQuery.selectEntity(this.articleId);
  }

  async addComment(input: HTMLTextAreaElement) {
    await this.articlesService.addComment(this.articleId, input.value);
    input.value = '';
  }

  async editComment() {
    await this.articlesService.editComment(this.articleId, this.selectedComment);
    this.selectedComment = {} as ArticleComment;
  }

  deleteComment(id: string) {
    this.articlesService.deleteComment(this.articleId, id);
  }

  selectComment(comment: ArticleComment) {
    this.selectedComment = { ...comment };
  }

  trackByFn(index, comment) {
    return comment.id;
  }
} 
```

首先，我们从`ActivatedRoute` provider snapshot 属性获取当前的文章 id。然后，我们用它通过使用`selectEntity`选择器从商店中反应性地选择商品。我们创建了三种方法来添加、更新和删除评论。让我们看看模板:

```
<div *ngIf="article$ | async as article">
  <h1>{{ article.title }}</h1>
  <p>{{ article.content }}</p>

  <h3>Comments</h3>
  <div *ngFor="let comment of article.comments; trackBy: trackByFn" 
       (click)="selectComment(comment)">
    {{ comment.text }} 
    <button (click)="deleteComment(comment.id)">Delete</button>
  </div>

  <h5>New Comment</h5>

  <div>
    <textarea #comment></textarea>
    <button type="submit" (click)="addComment(comment)">Add</button>
  </div>

  <h5>Edit Comment</h5>

  <div>
    <textarea [(ngModel)]="selectedComment.text"></textarea>
    <button type="submit" (click)="editComment()">Edit</button>
  </div>
</div> 
```

让我们以完整的服务实现来结束。

```
@Injectable({ providedIn: 'root' })
export class ArticlesService {

  constructor(private store: ArticlesStore,
              private http: HttpClient) {
  }

  async getAll() {
    const response = await this.http.get('url').toPromise();
    this.store.set(response.data);
  }

  async addComment(articleId: string, text: string) {
    const commentId = await this.http.post(...).toPromise();

    const comment: ArticleComment = {
      id: commentId,
      text
    };

    this.store.update(articleId, article => ({
      comments: arrayAdd(article.comments, comment)
    }));
  }

  async editComment(articleId: string, { id, text }: ArticleComment) {
    await this.http.put(...).toPromise();

    this.store.update(articleId, article => ({
      comments: arrayUpdate(article.comments, id, { text })
    }));
  }

  async deleteComment(articleId: string, commentId: string) {
    await this.http.delete(...).toPromise();

    this.store.update(articleId, article => ({
      comments: arrayRemove(article.comments, commentId)
    }));
  }
} 
```

在每个 CRUD 方法中，我们首先更新服务器，只有当操作成功时，才使用 Akita 的内置数组 [utils](https://netbasal.gitbook.io/akita/general/state-array-utils) 更新相关注释。

现在，让我们检查一下替代策略。

## 策略二-数据归一化

这个策略需要对我们从服务器获得的数据进行规范化。这个想法是创建两个商店。负责存储整个评论。`ArticlesStore`负责存储文章，其中每篇文章都有一个`comments`数组属性，包含相关评论的 id。

```
ng g af articles
ng g af comments 
```

让我们看看模型。

```
// article.model
export interface Article {
  id: ID;
  title: string;
  content: string;
  comments: (Comment | ID)[];
}

// commment.model
export interface Comment {
  id: ID;
  text: string;
} 
```

现在，让我们修改一下`ArticleService` `getAll`方法。

```
@Injectable({ providedIn: 'root' })
export class ArticlesService {

  constructor(private articlesStore: ArticlesStore,
              private commentsService: CommentsService,
              private commentsStore: CommentsStore,
              private http: HttpClient) {
  }

  async getAll() {
    const response = await this.http.get('url').toPromise();

    const allComments = [];

    const articles = response.data.map(currentArticle => {
      const { comments, ...article } = currentArticle;
      article.comments = [];

      for(const comment of comments) {
        allComments.push(comment);
        article.comments.push(comment.id);
      }
      return article;
    });

    this.commentsStore.set(allComments);
    this.articlesStore.set(articles);
  }
} 
```

我们创建一个新的 articles 数组，用评论 id 替换每篇文章中的`comment`对象。接下来，我们创建`allComments`数组，它保存所有的注释。最后，我们将它们都添加到相应的存储中。

现在，让我们看看我们需要在文章页面中改变什么。因为我们需要显示文章及其评论，所以我们需要创建一个派生查询，将文章与其评论连接起来。让我们创造它。

```
@Injectable({ providedIn: 'root' })
export class ArticlesQuery extends QueryEntity<ArticlesState, Article> {

  constructor(protected store: ArticlesStore, private commentsQuery: CommentsQuery) {
    super(store);
  }

  selectWithComments(articleId: string) {
    return combineLatest(
      this.selectEntity(articleId),
      this.commentsQuery.selectAll({ asObject: true })
    ).pipe(map(([article, allComments]) => ({
      ...article,
      comments: article.comments.map(id => allComments[id])
    })));
  }
} 
```

我们创建了接受`articleId`的`selectWithComments`选择器，在文章和评论之间创建了一个连接，并基于`comments`id 返回带有评论的映射版本。现在，我们可以在组件中使用它:

```
export class ArticlePageComponent implements OnInit {
  article$: Observable<Article>;

  constructor(private route: ActivatedRoute,
              private articlesService: ArticlesService,
              private articlesQuery: ArticlesQuery) {
  }

  ngOnInit() {
    this.articleId = this.route.snapshot.params.id;
    this.article$ = this.articlesQuery.selectWithComments(this.articleId);
  }
} 
```

让我们看完`ArticlesService` :
中的变化

```
@Injectable({ providedIn: 'root' })
export class ArticlesService {

  constructor(private articlesStore: ArticlesStore,
              private commentsService: CommentsService,
              private commentsStore: CommentsStore,
              private http: HttpClient) {
  }

  async getAll() {}

  async addComment(articleId: string, text: string) {
    const commentId = await this.commentsService.add(articleId, text);

    this.articlesStore.update(articleId, article => ({
      comments: arrayAdd(article.comments, commentId)
    }));
  }

  async editComment(comment: Comment) {
    this.commentsService.edit(comment);
  }

  async deleteComment(articleId: string, commentId: string) {
    await this.commentsService.delete(commentId);

    this.articlesStore.update(articleId, article => ({
      comments: arrayRemove(article.comments, commentId)
    }));
  }

} 
```

在这种情况下，当我们执行添加或删除操作时，我们需要更新`CommentsStore`和`ArticlesStore`。在编辑的情况下，我们只需要更新`CommentsStore`。这里是`CommentsService`。

```
@Injectable({ providedIn: 'root' })
export class CommentsService {

  constructor(private commentsStore: CommentsStore) {
  }

  async add(articleId: string, text: string) {
    const id = await this.http.post().toPromise();
    this.commentsStore.add({
      id,
      text
    });

    return id;
  }

  async delete(id: string) {
    await await this.http.delete(...).toPromise();
    this.commentsStore.remove(id);
  }

  async edit(comment: Comment) {
    await this.http.put(...).toPromise();
    return this.commentsStore.update(comment.id, comment);
  }
} 
```

## 总结

我们学习了如何管理与秋田一对多关系的两种策略。在大多数情况下，我会选择第一种策略，因为它更简洁、更直接。当您的应用程序中有大量编辑操作，并且您关心性能时，第二种策略可能很有用。

但是请记住，过早的优化是万恶之源。