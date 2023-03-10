# 用 Python/Django 动态创建句子(madlibs)

> 原文：<https://dev.to/iceorfiresite/dynamically-created-sentences-in-python-django-madlibs-4l71>

这是一篇完全不同于我们之前所有其他类型的博文。这篇文章将不再关注最近的媒体发布，而是非常乏味，深入到新的[每周最佳选择](https://www.iceorfire.com/weekly-top-picks/)页面是如何工作的。*小心:前面有 Python/Django 代码！*

“每周最佳作品”页面列出了每个类别中本周排名前三的作品。每个媒体部分(书籍、电影、蓝光、专辑)都有一个动态生成的描述段落。我将在下面描述它是如何工作的。

我们正在处理三种 Django 模型:书籍、电影和专辑。模型比这里显示的要复杂一些，但我尽量保持简单。

## 电影课

```
class Movie(models.Model):
    """
    Movie class
    """

    # Fields
    title = models.CharField(max_length=100, help_text="Enter title")
    release_date = models.DateField()
    bluray_date = models.DateField(null=True, blank=True)
    poster = models.CharField(max_length=200, help_text="Enter poster file")
    overview = models.TextField(help_text="Enter overview")
    genre = models.ManyToManyField(MovieGenre, help_text="Select a genre for this movie")
    mpaa_rating = models.CharField(max_length=10, help_text="MPAA Rating") #details call
    run_time = models.IntegerField()
    slug = models.SlugField(max_length=100, null=True, blank=True)
    rating = models.IntegerField(default=60)
    trailer = models.CharField(max_length=100, help_text="Enter trailer link")
    director = models.CharField(max_length=100, blank=True, help_text="Enter director")
    theater_link = models.CharField(max_length=255, null=True, blank=True, help_text="Enter theater link")
    bluray_link = models.CharField(max_length=255, null=True, blank=True, help_text="Enter blu-ray link")
    votes = models.IntegerField(default=0)
    num_ratings = models.IntegerField(default=0)
    running_total = models.IntegerField(default=0)
    weighted_rating = models.IntegerField(default=0)

```

## 相册类

```
class Album(models.Model):
    """
    Album class
    """

    # Fields
    artist = models.CharField(max_length=100, help_text="Enter artist")
    title = models.CharField(max_length=100, help_text="Enter title")
    image = models.CharField(max_length=100, help_text="Enter image file")
    big_image = models.CharField(max_length=100, help_text="Enter image file")
    release_date = models.DateField()
    record_type = models.CharField(max_length=25, help_text="Enter record type")
    slug = models.SlugField(max_length=100, null=True, blank=True)
    length = models.IntegerField() #in seconds
    link = models.CharField(max_length=255, blank=True, help_text="Enter purchase link")
    genre = models.ManyToManyField(AlbumGenre, blank=True, help_text="Select a genre for this album")
    base_rating = models.IntegerField(default=60)
    votes = models.IntegerField(default=0)
    num_ratings = models.IntegerField(default=0)
    running_total = models.IntegerField(default=0)
    weighted_rating = models.IntegerField(default=0)

```

## 图书类

```
class Book(models.Model):
    """
    Book class
    """

    # Fields
    title = models.CharField(max_length=150, help_text="Enter title")
    cover_image = models.CharField(max_length=200, help_text="Enter image file")
    description = models.TextField(help_text="Enter description")
    author = models.CharField(max_length=100, help_text="Enter author")
    pages = models.IntegerField()
    release_date = models.DateField()
    genre = models.ManyToManyField(BookGenre, help_text="Select a genre for this book")
    slug = models.SlugField(max_length=100, null=True, blank=True)
    rating = models.IntegerField(default=60)
    votes = models.IntegerField(default=0)
    link = models.CharField(max_length=255, blank=True, help_text="Enter purchase link")
    num_ratings = models.IntegerField(default=0)
    running_total = models.IntegerField(default=0)
    weighted_rating = models.IntegerField(default=0)

```

接下来，我们需要一个实用方法来获取本周的日期范围。我们得到今天的日期，然后算出星期几，星期日是 0，星期六是 6。我们使用这些信息计算出一周的周日和周六，它们对应于我们的开始和结束日期。传回开始和结束日期！

```
def getThisWeekStartEnd():
    """
    return Sunday's and Saturday's datetime object for the current week
    """

    today = datetime.today().strftime("%Y-%m-%d")

    d1 = datetime.strptime(today, "%Y-%m-%d")
    day_of_week = d1.weekday()

    if day_of_week == 0:
        start_date = d1 + timedelta(days=-1)
        end_date = d1 + timedelta(days=5)
    elif day_of_week == 1:
        start_date = d1 + timedelta(days=-2)
        end_date = d1 + timedelta(days=4)
    elif day_of_week == 2:
        start_date = d1 + timedelta(days=-3)
        end_date = d1 + timedelta(days=3)
    elif day_of_week == 3:
        start_date = d1 + timedelta(days=-4)
        end_date = d1 + timedelta(days=2)
    elif day_of_week == 4:
        start_date = d1 + timedelta(days=-5)
        end_date = d1 + timedelta(days=1)
    elif day_of_week == 5:
        start_date = d1 + timedelta(days=-6)
        end_date = d1
    elif day_of_week == 6:
        start_date = d1
        end_date = d1 + timedelta(days=6)

    return start_date, end_date

```

现在我们有了开始和结束日期，让我们编写 views.py 函数来查询我们的数据。我们首先调用刚刚编写的函数来获取开始和结束日期。接下来，我们查询日期范围内的 3 个 Book 数据对象，并按降序排序(前几个结果)。对电影和相册对象做同样的事情。注:剧场版和蓝光都是电影。如果我们没有本周的数据，可以查询上周的数据。(我们应该添加一个后备，以防不返回任何数据，但这符合我们的目的。)我们有了数据，所以要造句子...我们将在下面介绍。

```
def top_picks(request):
    start_date, end_date = getThisWeekStartEnd()

    books = Book.objects.filter(release_date__range=[start_date, end_date]).filter(active=True).order_by('-rating')[:3]
    theaters = Movie.objects.filter(release_date__range=[start_date, end_date]).filter(active=True).filter(bluray_date__isnull=True).order_by('-rating')[:3]
    blurays = Movie.objects.filter(bluray_date__range=[start_date, end_date]).filter(active=True).filter(bluray_date__isnull=False).order_by('-rating')[:3]
    albums = Album.objects.filter(release_date__range=[start_date, end_date]).filter(active=True).order_by('-base_rating')[:3]

    if len(books) == 0:
        books = Book.objects.filter(release_date__range=[start_date + timedelta(days=-6), end_date + timedelta(days=-6)]).filter(active=True).order_by('-rating')[:3]

    if len(theaters) == 0:
        theaters = Movie.objects.filter(release_date__range=[start_date + timedelta(days=-6), end_date + timedelta(days=-6)]).filter(active=True).filter(bluray_date__isnull=True).order_by('-rating')[:3]

    if len(blurays) == 0:
        blurays = Movie.objects.filter(bluray_date__range=[start_date + timedelta(days=-6), end_date + timedelta(days=-6)]).filter(active=True).filter(bluray_date__isnull=False).order_by('-rating')[:3]

    if len(albums) == 0:
        albums = Album.objects.filter(release_date__range=[start_date + timedelta(days=-6), end_date + timedelta(days=-6)]).filter(active=True).order_by('-base_rating')[:3]

    books_text = build_book_sentences(books)
    theaters_text = build_movie_sentences(theaters)
    blurays_text = build_movie_sentences(blurays)
    albums_text = build_album_sentences(albums)

    return render(
        request,
        'top_picks.html',
        context={
            'books':books,
            'books_text':books_text,
            'theaters':theaters,
            'theaters_text':theaters_text,
            'blurays':blurays,
            'blurays_text':blurays_text,
            'albums':albums,
            'albums_text':albums_text
        },
    )

```

太好了！我们已经有了数据对象，所以让我们构建动态生成的句子。我们将创建三个相似的函数:一个用于书籍，一个用于电影，一个用于相册。这三种方式都是一样的...创建一个形容词列表、一个动作列表，并在我们遍历媒体对象时动态地分配它们。接下来，我们将变量应用于随机选择的句子结构。最后，把句子串联起来，传回去。

```
def build_book_sentences(books):
    sentences = ''
    adjectives = ['exciting', 'finely crafted', 'inventive', 'fresh', 'vivid', 'mesmerizing', 'imaginative']
    actions = [
        "invents a spellbinding world that draws you in. ",
        "grabs you and doesn't let go until you've read the last page. ",
        "paints a literary picture that makes you feel as though you're right there in the action. ",
        "presents a new take on a familiar genre with a page-turning style. ",
        "demands you curl up in a comfy chair and keep reading until the very last word. "
    ]

    used_adj = []
    used_action = []

    for book in books:
        sel_adj = random.choice(adjectives)
        sel_action = random.choice(actions)

        while sel_adj in used_adj or sel_action in used_action:
            sel_adj = random.choice(adjectives)
            sel_action = random.choice(actions)

        used_adj.append(sel_adj)
        used_action.append(sel_action)

        genre = book.genre.first().name.lower().replace('&', 'and')

        structure = [
            "{}'s {} new {} book, {}, {}".format(book.author, sel_adj, genre, book.title, sel_action),
            "In the {} {} book, {}, {} {}".format(sel_adj, genre, book.title, book.author, sel_action)
        ]

        sel_struct = random.choice(structure)
        sentences = sentences + sel_struct

    return sentences

```

```
def build_movie_sentences(movies):
    sentences = ''
    adjectives = ['exciting', 'finely crafted', 'inventive', 'fresh', 'vivid', 'mesmerizing', 'imaginative']
    actions = [
        "invents a spellbinding world that draws you in. ",
        "grabs you and doesn't let go until you've watched the credits roll. ",
        "paints a cinematic picture that makes you feel as though you're right there in the action. ",
        "presents a new take on a familiar genre with a fascinating style. ",
        "demands you perch on the edge of your seat and keep watching until the very last scene. "
    ]

    used_adj = []
    used_action = []

    for movie in movies:
        sel_adj = random.choice(adjectives)
        sel_action = random.choice(actions)

        while sel_adj in used_adj or sel_action in used_action:
            sel_adj = random.choice(adjectives)
            sel_action = random.choice(actions)

        used_adj.append(sel_adj)
        used_action.append(sel_action)

        genre = movie.genre.first().name.lower().replace('&', 'and')

        structure = [
            "{}'s {} new {} movie, {}, {}".format(movie.director, sel_adj, genre, movie.title, sel_action),
            "In the {} {} movie, {}, {} {}".format(sel_adj, genre, movie.title, movie.director, sel_action)
        ]

        sel_struct = random.choice(structure)
        sentences = sentences + sel_struct

    return sentences

```

```
def build_album_sentences(albums):
    sentences = ''
    adjectives = ['exciting', 'finely crafted', 'inventive', 'fresh', 'vivid', 'mesmerizing', 'imaginative']
    actions = [
        "casts a musical spell that draws you in. ",
        "grabs you by the ears and doesn't let go until last note. ",
        "paints a auditory picture that makes you feel as though you're right there on stage. ",
        "presents a new take on a familiar genre with a captivating style. ",
        "demands you crank the stereo to 11 and keep dancing until the very last track. "
    ]

    used_adj = []
    used_action = []

    for album in albums:
        sel_adj = random.choice(adjectives)
        sel_action = random.choice(actions)

        while sel_adj in used_adj or sel_action in used_action:
            sel_adj = random.choice(adjectives)
            sel_action = random.choice(actions)

        used_adj.append(sel_adj)
        used_action.append(sel_action)

        genre = album.genre.first().name.lower().replace('&', 'and')

        structure = [
            "{}'s {} new {} album, {}, {}".format(album.artist, sel_adj, genre, album.title, sel_action),
            "In the {} {} album, {}, {} {}".format(sel_adj, genre, album.title, album.artist, sel_action)
        ]

        sel_struct = random.choice(structure)
        sentences = sentences + sel_struct

    return sentences

```

我们的最后一步是将数据传递给 Django 模板，这里我们不讨论这个。查看我们的[已完成页面](https://www.iceorfire.com/weekly-top-picks/)。