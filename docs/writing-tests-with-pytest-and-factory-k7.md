# 使用 pytest 和 pytest-factoryboy 编写测试

> 原文：<https://dev.to/aduranil/writing-tests-with-pytest-and-factory-k7>

用 python 测试可能会很棘手，所以我列出了适合我的方法。我喜欢使用 [pytest](https://docs.pytest.org/en/latest/) 和 [pytest-factoryboy](https://github.com/pytest-dev/pytest-factoryboy) 来生成测试数据。是一种比在测试数据库中维护夹具更容易的测试方法。

## 定义工厂。他们看起来很像模特。

```
# tests/factories.py 
import factory
from faker import Factory as FakerFactory

from django.contrib.auth.models import User
from django.utils.timezone import now

from app.models import Game, Message, GamePlayer, Round, Move

faker = FakerFactory.create()

class UserFactory(factory.django.DjangoModelFactory):
    class Meta:
        model = User

    email = factory.Faker("safe_email")
    username = factory.LazyAttribute(lambda x: faker.name())

    @classmethod
    def _prepare(cls, create, **kwargs):
        password = kwargs.pop("password", None)
        user = super(UserFactory, cls)._prepare(create, **kwargs)
        if password:
            user.set_password(password)
            if create:
                user.save()
        return user

class GameFactory(factory.django.DjangoModelFactory):
    room_name = factory.LazyAttribute(lambda x: faker.name())
    game_status = "active"
    created_at = factory.LazyAttribute(lambda x: now())
    updated_at = factory.LazyAttribute(lambda x: now())
    round_started = False
    is_joinable = True

    class Meta:
        model = Game

class GamePlayerFactory(factory.django.DjangoModelFactory):
    followers = 0
    selfies = 3
    user = factory.SubFactory(UserFactory)
    started = False
    game = factory.SubFactory(GameFactory)

    class Meta:
        model = GamePlayer

class MessageFactory(factory.django.DjangoModelFactory):
    game = factory.SubFactory(GameFactory)
    username = None
    message = factory.LazyAttribute(lambda x: faker.sentence())
    created_at = factory.LazyAttribute(lambda x: now())
    message_type = None

    class Meta:
        model = Message

# etc 
```

## 在一个`conftest.py`文件里我注册了工厂。

在 [`conftest.py`](https://stackoverflow.com/questions/34466027/in-pytest-what-is-the-use-of-conftest-py-files) 文件中定义的夹具将在任何测试文件中被识别。“注册”工厂允许您用一个**小写下划线类名将它们注入到您的测试中。在同一个文件中，我用我将在测试中使用的`@pytest.fixture()`来定义 fixtures，并用我刚刚注册的工厂来注入它们。** 

```
# tests/conftest.py import pytest
from pytest_factoryboy import register

from .factories import (
    UserFactory,
    GameFactory,
    GamePlayerFactory,
    MoveFactory,
    RoundFactory,
)

register(UserFactory)
register(GameFactory)
register(GamePlayerFactory)
register(MoveFactory)
register(RoundFactory)

@pytest.fixture()
def game(game_factory):
    return game_factory()

@pytest.fixture()
def rnd(game, round_factory):
    return round_factory(game=game, started=True)

@pytest.fixture()
def p_1(game, user_factory, game_player_factory):
    return game_player_factory(game=game, user=user_factory(), started=True)

#etc...more player fixtures defined 
```

## 写测试

我在这里写测试。我将我的夹具数据传入我的测试方法，还传入一个名为`move`的工厂，因为这是我在这里主要测试的内容。为了测试玩家的移动，我首先需要创建玩家、游戏和回合，这就是我在我的`conftest.py`文件中所做的。我只需要这样做一次，并且我不需要导入 fixtures，因为在`conftest.py`中声明的任何东西对测试文件都是可见的。

```
# tests/test_round_service.py 
import pytest

from app.services.round_service import (
    RoundTabulation,
    LEAVE_COMMENT,
    CALL_IPHONE,
    DISLIKE,
    GO_LIVE,
    POST_SELFIE,
    DONT_POST,
    NO_MOVE,
    DISLIKE_DM,
    POST_SELFIE_PTS,
    POST_SELFIE_DM,
    GO_LIVE_DM,
    NO_MOVE_DM,
)
from app.services import round_service, message_service
from app.models import Move, Message, GamePlayer

@pytest.mark.django_db
def test_iphone_go_live_do_selfie(rnd, p_1, p_2, p_3, move_factory):
    """if a girl calls a girl who is trying to take a selfie, she sustains go
    live damage"""
    move_factory(round=rnd, action_type=GO_LIVE, player=p_1, victim=None)
    move_factory(round=rnd, action_type=CALL_IPHONE, player=p_2, victim=p_3)
    move_factory(round=rnd, action_type=POST_SELFIE, player=p_3, victim=None)
    tab = RoundTabulation(rnd).tabulate()
    assert tab[p_1.id] == 0
    assert tab[p_2.id] == GO_LIVE_DM
    assert tab[p_3.id] == GO_LIVE_DM

@pytest.mark.django_db
def test_go_live_with_call(rnd, p_1, p_2, p_3, p_4, p_5, move_factory):
    """message was created, points are corrected, player is removed from player
    points array, they have one less story"""
    move_factory(round=rnd, action_type=GO_LIVE, player=p_1, victim=None)
    move = move_factory(round=rnd, action_type=CALL_IPHONE, player=p_2, victim=p_1)
    move_factory(round=rnd, action_type=DISLIKE, player=p_3, victim=p_1)
    move_factory(round=rnd, action_type=DISLIKE, player=p_4, victim=p_1)
    move_factory(round=rnd, action_type=LEAVE_COMMENT, player=p_5, victim=p_1)
    tab = RoundTabulation(rnd).tabulate()
    assert message(rnd.game, p_2.user.username) in message_service.iphone_msg(
        move, p_1.user.username
    )
    p_1 = GamePlayer.objects.get(id=p_1.id)
    assert p_1.go_live == 1
    assert tab[p_1.id] == -50 
```

## 执行测试

现在您的测试可以使用测试数据运行了！

```
[17:59:32] (master) selfies
🙋 docker exec -it 5c93db3f3183 bash
root@5c93db3f3183:/selfies# pytest app/tests/test_round_service.py
======================================= test session starts ========================================
platform linux -- Python 3.7.4, pytest-5.0.1, py-1.8.0, pluggy-0.12.0
Django settings: selfies.settings (from ini file)
rootdir: /selfies, inifile: pytest.ini
plugins: factoryboy-2.0.3, django-3.5.1
collected 15 items                                                                                 

app/tests/test_round_service.py ............... 
```