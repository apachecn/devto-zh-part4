# ð¤â¡ð(ææºå¨äººç»æ)

> åæï¼<https://dev.to/charlesdlandau/teach-bots-to-land-on-the-moon-27m9>

[å¨ Google Colab ä¸­ä¿®è¡¥è¿æ¬¾ç¬è®°æ¬](https://colab.research.google.com/github/CharlesDLandau/rlNotebooks/blob/master/botsToTheMoon.ipynb)

[GitHub](https://github.com/CharlesDLandau/rlNotebooks)

å¨è¿å»çå ééï¼æä»¬åé¡¾äºå¼ºåå­¦ä¹ çè®¾è®¡ååï¼ä»ç»äº OpenAI Gym çå¼ºåå­¦ä¹ ï¼å¹¶è®­ç»äºä¸ä¸ªæ·±åº¦å­¦ä¹ æ¨¡åæ¥å¯¼èª OpenAI Gym ç¯å¢ãä¸ºäºå®ç°è¿ä¸ç¹ï¼æä»¬å©ç¨äºä»£ç-ç¯å¢å³ç³»:

*   ä»£çå¨ç¯å¢ä¸­éåè¡å¨ãè¿äºå¯ä»¥æ¯éæºçï¼æèç±å¯åæ³å¼å¯¼ï¼æèç±æ¨¡åå¼å¯¼ï¼æèç±ä¸äºç»åå¼å¯¼ãå®æ¥æ¶å³äºç¯å¢çæ°æ®ï¼æ¯å¦ç¯å¢çå¾çæå­ç¬¦ä¸²ï¼ç¶åéåè¡å¨ãä¹åï¼å®ä»ç¯å¢ä¸­è·å¾å¥å±ï¼è¿ç¸å½äºå¯¹æéè¡ä¸ºçç§¯æææ¶æçâå¼ºåâã

*   ç¯å¢è·è¸ªç¶æãå¯è½çå¨ä½ãå¥å±å**å¥å±å½æ°**ï¼å¹¶ä¸å¯éå°å°ç¯å¢æ¸²æå°æ¾ç¤ºå¨ä¸ãå®æ¥æ¶æ¥èªä»£ççå¨ä½ï¼å¹¶ä¸å®å¯ä»¥è¢«éç½®ä¸ºä¿å­æ°æ®(ä¾å¦ï¼ä»£çå¨ç¯å¢ä¸­æ§è¡çè§é¢ã)

#### æ´å¥½çè¯å

å¨åå ééï¼æä»¬è¿å®ç°äºä¸äºç®åçå¼ºåå­¦ä¹ ãæ²¡æå·ç§¯å±æç­ç¥å¼æï¼ç½ç»åªè½å­¦ä¹ ç®åçç­ç¥(å¦[å·¦è½¬](https://dev.to/charlesdlandau/drive-a-tank-with-python-3caf))ã

ä¸ºäºè§£å³è¿ä¸ªé®é¢ï¼æä»¬å¯ä»¥ä½¿ç¨å [Tensorflow Agents](https://github.com/tensorflow/agents) æ [OpenAI Baselines](https://github.com/openai/baselines) è¿æ ·çåºï¼å®ä»¬æ¥æä¸äºç»è¿ææèéªçææåä»£ççå®ç°ãæå·²ç»åå¶ä¸­çä¸äºåºåä½è¿äºï¼æå¾å°äºåè¿ç¯ç± Thomas Simonini æ°åç[ä¼ç§æç« ](https://medium.com/data-from-the-trenches/choosing-a-deep-reinforcement-learning-library-890fb0307092)ç¸åçç»è®ºãä¹å°±æ¯è¯´ï¼æåæ¬¢[ç¨³å®åºçº¿](https://github.com/hill-a/stable-baselines)ï¼OpenAI åºçº¿åºçä¸ä¸ªåæ¯ã

#### åå¥½åå¤

[![Getting Ready](img/28a31e59020d331126d7073e177354ea.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xigxxTP_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thumbs.gfycat.com/GorgeousUnfortunateAndalusianhorse-size_restricted.gif)

è®¾ç½® Google Colab çç¨³å®åºçº¿åå¶ä¾èµé¡¹ï¼å¹¶å¼æ¸æ¥å¦ä½è®© gif å·¥ä½ï¼è±äºææ¯ææ³è±¡ä¸­æ´é¿çæ¶é´ãä½ å¯ä»¥ç¨ä¸é¢å è¡ä»£ç æ¥å

```
# see: colab.research.google.com/drive/1GLlB53gvZaUyqMYv8GmZQJmshRUzV_tg !apt-get -qq -y install libcusparse8.0 libnvrtc8.0 libnvtoolsext1 > /dev/null
!ln -snf /usr/lib/x86_64-linux-gnu/libnvrtc-builtins.so.8.0 /usr/lib/x86_64-linux-gnu/libnvrtc-builtins.so
!apt-get -qq -y install xvfb freeglut3-dev ffmpeg> /dev/null

!pip install stable-baselines==2.7.0

# box2d fork until SWIG dependency is replaced !pip install box2d-py

# pyglet later versions break gym apparently !pip install pyglet==1.3.2

# Set up display; otherwise rendering will fail for
# classic control type envs, apparently import os
import Box2D
import pyglet
os.system("Xvfb :1 -screen 0 1024x768x24 &")
os.environ['DISPLAY'] = ':1'

# See https://github.com/ipython/ipython/issues/10045#issuecomment-522697219 from IPython.core.interactiveshell import InteractiveShell
InteractiveShell.ast_node_interactivity = "all"
from IPython import display 
```

Enter fullscreen mode Exit fullscreen mode

æäºè¿ä¸ªæ¹æ³ï¼ä½ å¯ä»¥ç¨ä¸¤è¡ä»£ç å®ç°ä¸ä¸ªé«è´¨éçä¼å¿æ¼åè¯è®ºå®¶(A2C)æ¨¡åãç®ååºçº¿å¾æ£ï¼å ä¸ºå®æç´è§çæ¹æ³æ¥è®­ç»åé¢æµãæ´å¥½çæ¯ï¼å®å°æä»¬çç¯å¢å­å¨ä¸ºä¸ä¸ª`model.env`æåï¼å æ­¤å¯¹äºå¸åçå·¥ä½æµï¼æä»¬æéè¦çåªæ¯ä¸ä¸ª`model`å®ä¾ã

è®©æä»¬å¼å§è®­ç»å§ââä¸é¢çåå®¹è¦è±ç¸å½å¤çæ¶é´æ¥è¿è¡:

```
from stable_baselines import A2C
import time
start = time.time()

# Train a multilayer perceptron in the lunar lander. model = A2C('MlpPolicy', "LunarLander-v2").learn(900000)

# Mine finished in about 10 minutes. print(f"Finished in {time.time()-start}s") 
```

Enter fullscreen mode Exit fullscreen mode

### ä¸ä¸ªæ¼ååä¸ä¸ªè¯è®ºå®¶èµ°è¿ä¸å®¶éå§ã

è¯´å°è¿éï¼æä»¬æ¥è°è°æ¼å-è¯è®ºå®¶èå¼ãæ¼åè¯è®ºå®¶è§£å³çé®é¢åªæ¶åæä»¬çå¥å±åè½ãå¨è®¸å¤æåµä¸ï¼å¥å±åªåçå¨âä¸éâæâæ¸¸æâçç»å°¾èèäºå­æ¸¸æçå¥å±å½æ°:

*   æ­æ¾æ¶è¿å 0
*   å¦ææ¸¸æä»¥å¹³å±ç»æï¼è¿å 0
*   å¦ææ¸¸æä»¥ä»£çäººå¤±è´¥åç»ï¼åè¿å-1.0
*   å¦ææ¸¸æä»¥ä»£çè·èèç»æï¼åè¿å 1.0

æ­£å¦ä½ æçå°çï¼æä»¬çä»£çæ¯éé½éåäºå ä¸ªæ­¥éª¤ï¼èè¿äºæ­¥éª¤ä»å¥å±åè½ä¸­æ ¹æ¬å¾ä¸å°ä»»ä½åé¦ï¼è¿åç¼äºè®­ç»çéåº¦ï¼å¹¶ä¸ä½¿å¾å¾é¾åæä»¬çä»£çæä¾å³äºå·ä½è¡å¨çç²¾ç»åé¦ãä¸ºäºè§£å³è¿ä¸ªé®é¢ï¼æ¼åè¯è®ºå®¶å®éä¸å®ç°äºä¸¤ä¸ªæ¨¡å:

*   ä¸ä¸ªæ¹è¯å®¶æ¨¡åï¼è¯å¾é¢æµç»å®è¡ä¸ºå°è·å¾çå¥å±å¼ï¼ç»å®å¯¹ç¯å¢çè§å¯ãè¡å¨-è§å¯å¯¹çé¢æµå¥å±å¼è¡¨ææ¶è¢«ç§°ä¸ºâQ è¡¨â
*   ä¸ä¸ªæ¼åæ¨¡åï¼ä»ç¯å¢ä¸­è·åè§å¯ç»æï¼ä½ä¸è·åå¥å±ãå®å­¦ä¹ å¨ç¯å¢ä¸­ç©èçç­ç¥ï¼ä¹å°±æ¯âæ¿ç­âè¿ä¸ªæ¨¡åä¸æ¯ç´æ¥ä¼åç¯å¢çåæ¥ï¼èæ¯ä¼åæ¥èªæ¹è¯å®¶æ¨¡åç*åé¦ãå½æä»¬åé¢æµæ¶ï¼åªéè¦è°ç¨æ¼åæ¨¡åã*

æä»¬å¯ä»¥å¾å®¹æå°æ³è±¡æä»¬çæ¼åæ¨¡åå¨æççéå¨æ¨¡åä¸­ï¼èè¯è®ºå®¶åå¨ææ¥ä¸­å¿è§å¯çéå¨çè¡å¨ã

[![the critic](img/19e59e33c0eeeeffdd74b8ec50b38bd4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--c-RxG-Ra--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thumbs.gfycat.com/FlawlessWhichAdamsstaghornedbeetle-size_restricted.gif)

æ¯å½ä¸ä¸ªå¨ä½è¢«éåæ¶ï¼æ¹è¯å®¶ä¼æ¥éå¨ä½ãè§åº¦ãéåº¦ç­ç­çè¡¨æ ¼ãççè¿ä¸ªå¨ä½æ¯å¦æå©äºæåçéï¼è¯è®ºå®¶éè¿æ çº¿çµåæ¼ååé¦è¿ä¸ªå¨ä½ï¼æ¼åä»åé¦ä¸­å­¦ä¹ æä¸ºä¸ä¸ªæ´å¥½çé£è¡åãå¨æ°åæ¬¡çéçè¿ç¨ä¸­,âæ¹è¯å®¶âæ ¹æ®å æ¯ãæååæå¤é¢ç¹å°æ´æ°è¡¨æ ¼ãæäºæ¥å­ï¼æ¼åå³çæ¶é³æºé£è¡ï¼ä½å®ä»ä¹ä¹å­¦ä¸å°ã

### è®°å½çéç GIF

ç°å¨ä½ çæ¨¡åå¯è½å·²ç»å®æäºè®­ç»ãæ ¹æ®è¿äºæä»¶ï¼ä¸é¢æ¯æä»¬å¦ä½è®°å½æ¨¡åè¿è¡ç gif:

```
import imageio
import numpy as np

# Number of images to capture n_images = 1200

images = []

# init a new episode obs = model.env.reset()
# init the img var with the starting state of the env img = model.env.render(mode='rgb_array')

for i in range(n_images):
  # At each step, append an image to list
  images.append(img)

  # Advance a step and render a new image
  action, _ = model.predict(obs)
  obs, _, _ ,_ = model.env.step(action)
  img = model.env.render(mode='rgb_array')

imageio.mimwrite('./lander.gif',
                [np.array(img) for i, img in enumerate(images) if i%2 == 0],
                fps=29) 
```

Enter fullscreen mode Exit fullscreen mode

å¦æä½ æåäºï¼é£ä¹`lander.gif`åºè¯¥æ¯è¿æ ·ç:

[![lander.gif](img/1518f744773db27e524027bd2250335b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MFfzzSgq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://serve-md.charlesdlandau.net/img/lander.gif)

å¨ Jupyter ä¸­æ¾ç¤º gif æç¹è¿åç´è§ï¼ææ¯è¿æ ·åç:

```
from pathlib import Path
gifPath = Path("lander.gif")
# Display GIF in Jupyter, CoLab, IPython with open(gifPath,'rb') as f:
    display.Image(data=f.read(), format='png') 
```

Enter fullscreen mode Exit fullscreen mode

### åè£å®æ¯...

å¨è¿ä¸ééï¼ä½ :

*   äºè§£æ¼åå¼è¯è®ºå®¶
*   äºè§£ç®ååºçº¿
*   çå°äºå¦ä½å¨ç¬è®°æ¬ä¸­è®¾ç½® giffing ç¯å¢
*   ç»éæçï¼

[![goodjob.gif](img/54bff219b6fec612a6365307a8e8542e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NjLwgo7R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thumbs.gfycat.com/PerfumedBelatedBlowfish-size_restricted.gif)

å¥½æ ·çã

å¨æªæ¥çå ééï¼æä»¬å°æ´ä»ç»å°çç RL å¦ä½ä¸ºç¨æ·ãä¼ä¸åæºå¨äººç»æ²»èåé ä»·å¼ãæ¬è¯·å³æ³¨ï¼æè°¢éè¯»ã