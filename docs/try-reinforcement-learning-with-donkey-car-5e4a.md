# 用驴车试试强化学习

> 原文：<https://dev.to/0xkoji/try-reinforcement-learning-with-donkey-car-5e4a>

### 1 创建虚拟 env

我用的是 pyenv

```
$ python -m virtualenv py37 --python=python3.7 
```

Enter fullscreen mode Exit fullscreen mode

激活 py37 并安装软件包

```
$ pip install python-socketio flask eventlet pygame numpy pillow h5py scikit-image opencv-python gym 
```

Enter fullscreen mode Exit fullscreen mode

我为以下回购创建了`dCar folder`

### 2 克隆 donkeycar

```
$ mkdir dCar
$ cd dCar
$ git clone https://github.com/wroscoe/donkey donkeycar
$ pip install -e donkeycar 
```

Enter fullscreen mode Exit fullscreen mode

### 3 克隆自动驾驶沙盒

```
$ git clone https://github.com/tawnkramer/sdsandbox.git
$ cd sdsandbox
$ pip install -r requirements.txt 
```

Enter fullscreen mode Exit fullscreen mode

### 4 克隆驴 _ 健身房

```
$ git clone https://github.com/tawnkramer/donkey_gym
$ pip install -e donkey_gym 
```

Enter fullscreen mode Exit fullscreen mode

快到了。我们还需要一样东西来运行模拟器。
下载二进制【https://github.com/tawnkramer/donkey_gym/releases】T2T4】然后保存到`Applications`

### 5 运行模拟器

在这种情况下，我们将从`dCar`目录
运行模拟器

```
$ python donkey_gym/examples/reinforcement_learning/ddqn.py --sim=/Applications/donkey_sim.app/Contents/MacOS/donkey_sim 
```

Enter fullscreen mode Exit fullscreen mode

然后你会看到如下所示的模拟器

[![](img/8b301c3a137d589e0894896ba0f0ce05.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--c_Hq_r6C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rra6fjfctbabgcbuo3dj.png)

点击`Play!`开始学习。

```
/Users/koji.kanao/Documents/py37/lib/python3.7/site-packages/skimage/viewer/__init__.py:6: UserWarning: Viewer requires Qt
  warn('Viewer requires Qt')
WARNING: Logging before flag parsing goes to stderr.
W0709 21:17:37.700056 4583351744 deprecation_wrapper.py:119] From ddqn.py:26: The name tf.keras.initializers.normal is deprecated. Please use tf.compat.v1.keras.initializers.normal instead.

W0709 21:17:37.701951 4583351744 deprecation_wrapper.py:119] From ddqn.py:218: The name tf.ConfigProto is deprecated. Please use tf.compat.v1.ConfigProto instead.

W0709 21:17:37.702128 4583351744 deprecation_wrapper.py:119] From ddqn.py:220: The name tf.Session is deprecated. Please use tf.compat.v1.Session instead.

2019-07-09 21:17:37.712400: I tensorflow/core/platform/cpu_feature_guard.cc:142] Your CPU supports instructions that this TensorFlow binary was not compiled to use: AVX2 FMA
W0709 21:17:37.712966 4583351744 deprecation_wrapper.py:119] From ddqn.py:221: The name tf.keras.backend.set_session is deprecated. Please use tf.compat.v1.keras.backend.set_session instead.

starting DonkeyGym env donkey subprocess started
binding to ('0.0.0.0', 9091)
waiting for sim to start..
2019-07-09 21:17:37.883 donkey_sim[69097:904450] Could not find image named 'ScreenSelector'.
waiting for sim to start..
waiting for sim to start..
waiting for sim to start..
waiting for sim to start..
waiting for sim to start..
2019-07-09 21:17:54.684 donkey_sim[69097:904450] Color LCD preferred device: AMD Radeon Pro 560 (high power)
2019-07-09 21:17:54.684 donkey_sim[69097:904450] Metal devices available: 2
2019-07-09 21:17:54.684 donkey_sim[69097:904450] 0: Intel(R) HD Graphics 630 (low power)
2019-07-09 21:17:54.684 donkey_sim[69097:904450] 1: AMD Radeon Pro 560 (high power)
2019-07-09 21:17:54.684 donkey_sim[69097:904450] Using device AMD Radeon Pro 560 (high power)
waiting for sim to start..
got a new client ('127.0.0.1', 58233)
SceneSelectionReady
connection dropped
waiting for sim to start..
got a new client ('127.0.0.1', 58234)
W0709 21:18:01.760221 4583351744 deprecation.py:506] From /Users/koji.kanao/Documents/py37/lib/python3.7/site-packages/tensorflow/python/ops/init_ops.py:1251: calling VarianceScaling.__init__ (from tensorflow.python.ops.init_ops) with dtype is deprecated and will be removed in a future version.
Instructions for updating:
Call initializer instance with the dtype argument instead of passing it to the constructor
Episode:  0
EPISODE 0 TIMESTEP 30 / ACTION [0.70797646, 0.3] / REWARD 0.8670342954954999 / EPISODE LENGTH 30 / Q_MAX  0
fps 14.5105030403251
EPISODE 0 TIMESTEP 60 / ACTION [-0.5889623, 0.3] / REWARD -0.4946022760490001 / EPISODE LENGTH 60 / Q_MAX  0
EPISODE 0 TIMESTEP 90 / ACTION [0.40704942, 0.3] / REWARD -1.9541489999891999 / EPISODE LENGTH 90 / Q_MAX  0
fps 20.018557585621934
episode: 0   memory length: 107   epsilon: 0.9895139999999955  episode length: 107
Episode:  1
episode: 1   memory length: 108   epsilon: 0.9894159999999954  episode length: 1
Episode:  2
EPISODE 2 TIMESTEP 120 / ACTION [0.884501, 0.3] / REWARD 0.772067625056 / EPISODE LENGTH 12 / Q_MAX  26.828781
episode: 2   memory length: 147   epsilon: 0.9855939999999938  episode length: 39
Episode:  3
episode: 3   memory length: 148   epsilon: 0.9854959999999937  episode length: 1
Episode:  4
EPISODE 4 TIMESTEP 150 / ACTION [0.49025267, 0.3] / REWARD 0.86992308065572 / EPISODE LENGTH 2 / Q_MAX  26.773907
EPISODE 4 TIMESTEP 180 / ACTION [0.06281003, 0.3] / REWARD -1.0 / EPISODE LENGTH 32 / Q_MAX  24.477978
episode: 4   memory length: 180   epsilon: 0.9823599999999924  episode length: 32
Episode:  5
EPISODE 5 TIMESTEP 210 / ACTION [-0.56007874, 0.3] / REWARD 4.7462194558588 / EPISODE LENGTH 30 / Q_MAX  26.608269
episode: 5   memory length: 211   epsilon: 0.979321999999991  episode length: 31
Episode:  6
episode: 6   memory length: 212   epsilon: 0.979223999999991  episode length: 1
Episode:  7
EPISODE 7 TIMESTEP 240 / ACTION [0.34383777, 0.3] / REWARD 1.068961473664648 / EPISODE LENGTH 28 / Q_MAX  26.291294
episode: 7   memory length: 251   epsilon: 0.9754019999999893  episode length: 39
Episode:  8
episode: 8   memory length: 252   epsilon: 0.9753039999999893  episode length: 1
Episode:  9
EPISODE 9 TIMESTEP 270 / ACTION [0.60506356, 0.3] / REWARD -0.38350943820499994 / EPISODE LENGTH 18 / Q_MAX  27.504139
episode: 9   memory length: 294   epsilon: 0.9711879999999875  episode length: 42
Episode:  10
episode: 10   memory length: 295   epsilon: 0.9710899999999875  episode length: 1
Episode:  11
EPISODE 11 TIMESTEP 300 / ACTION [-0.45778775, 0.3] / REWARD 1.26087653748968 / EPISODE LENGTH 5 / Q_MAX  34.701992 
```

Enter fullscreen mode Exit fullscreen mode