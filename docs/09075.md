# 如何在一个下午搭建自己的亚马逊戒指安全系统

> 原文：<https://dev.to/pubnub/how-to-build-your-own-amazon-ring-security-system-in-an-afternoon-3hm2>

在本教程中，我们将探索机器学习人工智能中最前沿的技术之一…计算机视觉！为了展示它的功能，这篇循序渐进的文章将带您通过面部识别机器学习算法构建您自己的桌面安全系统。

通过一个简单的网络摄像头，你的程序将能够识别你选择允许进入系统的人的面孔。如果任何未识别的面孔出现在您的网络摄像头前，它将触发一个文本、电子邮件和快照图像警报系统。我们还将使用 Cloudinary 和 PubNub 构建一个 React 本机应用程序，该应用程序可以接收“入侵者”面部的快照图像。如果您愿意，它还允许将用户添加到系统中。

[![](img/62670909e0ad9f27b3e31b1a42812b0e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2Lnzv18Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f7fn2ywml21pye294kml.gif)

## 什么是计算机视觉？

计算机视觉是人工智能中的一个特定领域，它处理训练机器学习模型以理解和解释视觉世界。通过从相机和视频的图像和帧中学习，计算机视觉人工智能可以准确地对它看到的对象进行分类，并随后像我们人类一样执行反应性任务。

[![](img/02bcdaabf7e73edf3c500c50e01a7325.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eXgyonqc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/iv8q69rtde1h4vfu4ihl.gif)

[来源](https://towardsdatascience.com/how-to-do-everything-in-computer-vision-2b442c469928)访问于 2019 年 7 月 31 日

## 用于面部追踪警报系统的 Python 代码

在开始编写代码之前，请确保您注册了一个免费的 PubNub 帐户，这样我们以后就不会遇到任何问题。

要从头开始构建项目，请使用您电脑的命令行应用程序创建您的项目目录:

```
mkdir faceTrackingApp
cd faceTrackingApp
```

Then create a new Python file called **facetracker.py**.

## Libraries and Dependencies for Computer Vision

### OpenCV 和人脸识别

First, let's import some machine learning libraries for our app's face tracking capabilities. The main libraries we are going to use are OpenCV and face_recognition.

```
import face_recognition # Machine Learning Library for Face Recognition
import cv2 # OpenCV
import numpy as np # Handling data
import time
import os,sys
```

[OpenCV](https://opencv.org/) is the most popular machine learning library for realtime Computer Vision. The library has useful tools such as webcam control as well as models to train a face tracking app from scratch. However, our project will primarily use [ageitgey's face_recognition python library](https://github.com/ageitgey/face_recognition) as it already comes with a face recognition model out of the box, making it extremely quick and easy to use.

### PubNub

Next, we're going to setup PubNub as our Data Stream Network to handle all of the data between our Python script and mobile application. After you've retrieved your free [PubNub API keys](https://dashboard.pubnub.com/signup), install the [PubNub Python SDK](https://www.pubnub.com/docs/python/pubnub-python-sdk).

```
pip install 'pubnub>=4.1.4'
```

Then, import the library in your python file,

```
from pubnub.callbacks import SubscribeCallback
from pubnub.pnconfiguration import PNConfiguration
from pubnub.pubnub import PubNub
from pubnub.enums import PNOperationType, PNStatusCategory
```

and configure a PubNub instance with your API keys.

```
# PubNub Config
pnconfig = PNConfiguration()
pnconfig.subscribe_key = "YOUR_SUBSCRIBE_KEY"
pnconfig.publish_key = "YOUR_PUBLISH_KEY"
pnconfig.ssl = False
pubnub = PubNub(pnconfig)
```

### 云雾缭绕

Lastly, we will setup [Cloudinary](https://cloudinary.com/) as our Content Delivery Network to store images of intruders' faces. This will work beautifully with PubNub as our python script can upload the image to Cloudinary, get the URL from the response, then PubNub will send that URL to our Client app to render. First, [sign up for a free Cloudinary account](https://cloudinary.com/users/register/free) and then install the Cloudinary Python SDK with:

```
pip install cloudinary
```

Setup the `CLOUDINARY_URL` environment variable by copying it from the [Management Console](https://cloudinary.com/console). Using zsh/bash/sh:

```
export CLOUDINARY_URL=cloudinary://API-Key:API-Secret@Cloud-name
```

Import the library in your Python script,

```
from cloudinary.api import delete_resources_by_tag, resources_by_tag
from cloudinary.uploader import upload
from cloudinary.utils import cloudinary_url
```

and configure a Cloudinary instance.

```
# Cloudinary Config
os.chdir(os.path.join(os.path.dirname(sys.argv[0]), '.'))
if os.path.exists('settings.py'):
    exec(open('settings.py').read())
DEFAULT_TAG = "python_sample_basic"
```

## 机器学习人脸跟踪算法

Before we begin building our face recognition machine learning model, we'll need to declare some global variables:

```
# Setup some Global Variables
video_capture = cv2.VideoCapture(0) # Webcam instance
known_face_names = [] # Names of faces
known_face_encodings = [] # Encodings of Faces
count = 0 # Counter for Number of Unknown Users
flag = 0 # Flag for Setting/Unsetting "Intruder Mode"
```

[NOTE: We create a count variable for the unknown users because we are going to dynamically save the user's snapshot image in a file path.] We're  going to append the count to the file name like an ID tag. In order to find this snapshot image later, we need to pull up that user's count variable, so we can find the image in the file path. To start training our face recognizer model, we’ll begin with two sample images of faces. You will need two images of two different people's faces in your project directory.

```
# Load a sample picture and learn how to recognize it.
sample_face_1 = face_recognition.load_image_file("sample_1.jpeg")
sample_face_1_encoding = face_recognition.face_encodings(sample_face_1)[0]

# Load a second sample picture and learn how to recognize it.
sample_face_2 = face_recognition.load_image_file("17.png")
sample_face_2_encoding = face_recognition.face_encodings(sample_face_2)[0]

# Create arrays of known face encodings and their names
known_face_encodings = [
    sample_face_1_encoding,
    sample_face_2_encoding
]

# Create Names for Sample Face encodings
known_face_names = [
    "sample_1",
    "sample_2"
]

# Initialize some variables
face_locations = []
face_encodings = []
face_names = []
process_this_frame = True
```

Next, we will declare a while loop that will run continuously for the duration of the app. The loop will be responsible for the main functions of our app:

*   打开并显示网络摄像机源
*   跟踪出现在网络摄像头前的人脸，并实时在人脸周围画一个红框
*   在已知用户的面孔下显示姓名，对于尚未添加到数据库中的面孔显示“未知”
*   当屏幕上出现“未知”面孔时，调用一系列警报和函数进行处理

```
while(True):

    video_capture = cv2.VideoCapture(0)
    # Grab a single frame of video
    ret, frame = video_capture.read()

    # Resize frame of video to 1/4 size for faster face recognition processing
    small_frame = cv2.resize(frame, (0, 0), fx=0.25, fy=0.25)

    # Convert the image from BGR color (which OpenCV uses) to RGB color (which face_recognition uses)
    rgb_small_frame = small_frame[:, :, ::-1]

    # Only process every other frame of video to save time
    if process_this_frame:
        # Find all the faces and face encodings in the current frame of video
        face_locations = face_recognition.face_locations(rgb_small_frame)
        face_encodings = face_recognition.face_encodings(rgb_small_frame, face_locations)

        face_names = []
        for face_encoding in face_encodings:
            # See if the face is a match for the known face(s)
            matches = face_recognition.compare_faces(known_face_encodings, face_encoding)
            name = "Unknown"

            # # If a match was found in known_face_encodings, just use the first one.
            # if True in matches:
            #     first_match_index = matches.index(True)
            #     name = known_face_names[first_match_index]

            # Or instead, use the known face with the smallest distance to the new face
            face_distances = face_recognition.face_distance(known_face_encodings, face_encoding)
            best_match_index = np.argmin(face_distances)
            if matches[best_match_index]:
                name = known_face_names[best_match_index]

            face_names.append(name)

            #---------------------See next section for this code block's explanation---------------------#

            ## Set Unknown User Flag and Send Alerts
            #global flag
            #if(name=='Unknown' and flag==0):
            #    flag = 1
            #    Alert()
            #
            #--------------------------------------------------------------------------------------------#

    process_this_frame = not process_this_frame

    # Display the results
    for (top, right, bottom, left), name in zip(face_locations, face_names):
        # Scale back up face locations since the frame we detected in was scaled to 1/4 size
        top *= 4
        right *= 4
        bottom *= 4
        left *= 4

        # Draw a box around the face
        cv2.rectangle(frame, (left, top), (right, bottom), (0, 0, 255), 2)

        # Draw a label with a name below the face
        cv2.rectangle(frame, (left, bottom - 35), (right, bottom), (0, 0, 255), cv2.FILLED)
        font = cv2.FONT_HERSHEY_DUPLEX
        cv2.putText(frame, name, (left + 6, bottom - 6), font, 1.0, (255, 255, 255), 1)

    # Display the resulting image
    cv2.imshow('Video', frame)

    # Hit 'q' on the keyboard to quit!
    if cv2.waitKey(10) & 0xFF == ord('q'):
        break

# Release handle to the webcam
video_capture.release()
cv2.destroyAllWindows()
```

## 发送警报

We shall take care of the case when an unregistered face appears in front of our webcam. We want our program to trigger an alert system the moment it sees an "Unknown" face. Luckily, all we need to do is add a few lines of code to our main while loop at the end of the ***for face_encoding in face_encodings:*** loop.

```
# Set Unknown User Flag and Send Alerts
global flag
if(name=='Unknown' and flag==0):
    flag = 1 # Stop repeated calls of Alerts until after the Unknown User is dealt with
    Alert() # Trigger Alert System
```

When the alert is triggered, we can then define a function to take a snapshot of the unknown user's face, call a function to upload the snapshot to Cloudinary, and finally call our Text/Email alert function.

```
def Alert():
    global count
    video_capture = cv2.VideoCapture(0) # Create Open CV Webcam Instance
    path = './' # Specify where you want the snapshot to be stored
    name = 'Unknown_User' + str(count) # Append User ID to File Path

    # Wait for 3 seconds
    print('Taking picture in 3')
    time.sleep(1)
    print('Taking picture in 2')
    time.sleep(1)
    print('Taking picture in 1')
    time.sleep(1)

    # Take Picture
    ret, frame = video_capture.read()

    # Grayscale Image to save memory space
    gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)

    # Save Image in File Path
    status = cv2.imwrite('% s/% s.jpg' % (path, name),gray)
    print('Unknown User Saved to Database', status)

    # Upload Snapshot to Cloudinary 
    upload_files('% s/% s.jpg' % (path,name))

    # Send Out Email and Text Alerts
    sendAlerts()
```

[NOTE: We grayscale the image because the face recognizer doesn't need color to determine facial features. We also store the snapshot image locally so we can add the face to the recognizer if the client wants to add the user later.] When defining our **upload_files()** function, we're passing in the snapshot's file path so Cloudinary knows where to upload the file from. We then get the response URL of where the image lives in the cloud. We send this url along with a user ID (count of Unknown user) over PubNub to our client application. The client application can then render the image of the snapshot from the Cloudinary URL.

```
def upload_files(msg):
    global count # Make global changes to count
    response = upload(msg, tags=DEFAULT_TAG) # Upload Image to Cloudinary
    url, options = cloudinary_url( 
        response['public_id'],
        format=response['format'],
        width=200,
        height=150,
        crop="fill"
    )
    dictionary = {"url": url, "ID": count}
    pubnub.publish().channel('global').message(dictionary).pn_async(publish_callback)
    count+=1 # Increment Unknown User Count
```

In order to publish with PubNub, we need to define a publish callback.

```
def publish_callback(result, status):
    pass
    # Handle PNPublishResult and PNStatus
```

  To setup your Text and Email alerts, you'll need to sign up for a [free ClickSend account](https://www.clicksend.com/us/) as well as a [free SendGrid account](https://sendgrid.com/marketing/sendgrid-services-cro/?extProvId=5&extPu=49397-gaw&extLi=115523142&sem_adg=8368110942&extCr=8368110942-299384208935&extSi=&extTg=&keyword=sendgrid&extAP=1t1&extMT=e&gclid=EAIaIQobChMIrtuHkIjg4wIVeSCtBh0q6A7hEAAYASAAEgKArvD_BwE) to get your API keys. Now you get to see the power and beauty behind [PubNub Functions](https://www.pubnub.com/products/functions/) with our [Partnered Blocks](https://www.pubnub.com/docs/blocks-catalog). Go ahead and visit both our [ClickSend Block](https://www.pubnub.com/docs/blocks-catalog/clicksend-sms) as well as our [SendGrid Block](https://www.pubnub.com/docs/blocks-catalog/sendgrid). Through those links, PubNub will automatically generate a customizable PubNub Function. The serverless, open-source code will completely handle the APIs for you. All you need to do is put in your API keys and you're good to go! Once you've set up your PubNub Functions, you can define a **sendAlerts()** function to publish a message, implementing your Text and Email alerts:

```
def sendAlerts():
    dictionary = {
    "to" : 'RECEIVING PHONE NUMBER',
    "body": "There is an unregistered user at your desk!"
    }
    pubnub.publish().channel('clicksend-text').message(dictionary).pn_async(publish_callback)

    dictionary = {
    "to": "EMAIL RECEIVER",
    "toname": "EMAIL SENDER",
    "subject": "INTRUDER ALERT",
    "text": "THERE IS AN UNREGISTERED USER AT YOUR DESK"
    }   
    pubnub.publish().channel('email-sendgrid-channel').message(dictionary).pn_async(publish_callback)
```

NOTE: In order to properly use a PubNub block, you need to publish over the same channel specified in your block (you can check this in your blocks Functions dashboard) as well as formatting the message payload properly (according to the block's documentation).

## 将用户添加到我们的 Facetracker

When an Unregistered face is detected on our webcam, our Python script sends an email/text alert as well as a snapshot image to our client application. We now want to add the ability to add a user's face to our app's "**known_faces**" database, so the user will no longer trigger our alert system. To do this, the client application must publish a message over PubNub. To receive this message in our python application, we must subscribe to the channel the client is publishing from, and create a **Subscriber Callback** to handle the incoming message.

```
class MySubscribeCallback(SubscribeCallback):
    def status(self, pubnub, status):
        pass
        # The status object returned is always related to subscribe but could contain
        # information about subscribe, heartbeat, or errors
        # use the operationType to switch on different options
        if status.operation == PNOperationType.PNSubscribeOperation \
                or status.operation == PNOperationType.PNUnsubscribeOperation:
            if status.category == PNStatusCategory.PNConnectedCategory:
                pass
                # This is expected for a subscribe, this means there is no error or issue whatsoever
            elif status.category == PNStatusCategory.PNReconnectedCategory:
                pass
                # This usually occurs if subscribe temporarily fails but reconnects. This means
                # there was an error but there is no longer any issue
            elif status.category == PNStatusCategory.PNDisconnectedCategory:
                pass
                # This is the expected category for an unsubscribe. This means here
                # was no error in unsubscribing from everything
            elif status.category == PNStatusCategory.PNUnexpectedDisconnectCategory:
                pass
                # This is usually an issue with the internet connection, this is an error, handle
                # appropriately retry will be called automatically
            elif status.category == PNStatusCategory.PNAccessDeniedCategory:
                pass
                # This means that PAM does not allow this client to subscribe to this
                # channel and channel group configuration. This is another explicit error
            else:
                pass
                # This is usually an issue with the internet connection, this is an error, handle appropriately
                # retry will be called automatically
        elif status.operation == PNOperationType.PNSubscribeOperation:
            # Heartbeat operations can in fact have errors, so it is important to check first for an error.
            # For more information on how to configure heartbeat notifications through the status
            if status.is_error():
                pass
                # There was an error with the heartbeat operation, handle here
            else:
                pass
                # Heartbeat operation was successful
        else:
            pass
            # Encountered unknown status type

    def presence(self, pubnub, presence):
        pass  # handle incoming presence data
    def message(self, pubnub, message):
        addUser(message.message["ID"], message.message["name"])

pubnub.add_listener(MySubscribeCallback())
pubnub.subscribe().channels('ch1').execute()
```

NOTE: Above we assume the client is publishing the ID of the unknown user (for image file path) as well as the name of the user (to display below the user's face). With the parameters in hand, we can add the new user to our database.

```
def addUser(ID, name):
    global known_face_encodings, known_face_names, flag
    path = './Unknown_User' + str(ID) # Append User ID to File Path
    # Load User's picture and learn how to recognize it.
    user_image = face_recognition.load_image_file('% s.jpg' % (path)) # Load Image
    user_face_encoding = face_recognition.face_encodings(user_image)[0] # Encode Image
    known_face_encodings.append(user_face_encoding) # Add Encoded Image to 'Known Faces' Array
    known_face_names.append(name) # Append New User's Name to Database
    flag = 0 # Reset Unknown User Flag
```

## 为我们的客户端应用程序反应本机代码

## 设置我们的实时反应本地环境

Install [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12) so we can build and simulate our app for IOS and [Android Studio](https://developer.android.com/studio?gclid=EAIaIQobChMI1LzfwrDi4wIVAspkCh3lNwvsEAAYASAAEgLXGvD_BwE) for Android. Then install [Node.js](https://nodejs.org/en/) and watchman using [Homebrew](https://brew.sh/):

```
brew install node
brew install watchman
```

Install the React Native CLI with NPM:

```
npm install -g react-native-cli
```

To create a React Native App template, enter the React Native CLI command in your project's directory:

```
react-native init client
cd client
```

Since we're going to be using PubNub in our Client app to send and receive messages, we'll need to install the PubNub React SDK,

```
npm install --save pubnub pubnub-react
```

and then link the library like so:

```
react-native link pubnub-react
```

## 设置实时发布/订阅消息

To start sending and receiving messages in realtime in our app, first import the [PubNub React SDK](https://www.pubnub.com/docs/react-native-javascript/pubnub-javascript-sdk).

```
import PubNubReact from 'pubnub-react';
```

Then import the **TouchableOpacity** and **Image** components from React Native,

```
import {
  StyleSheet,
  View,
  Text,
  TextInput,
  TouchableOpacity,
  Image,
} from 'react-native';

```

现在，我们在应用程序组件的顶部添加一个构造函数。构造函数将负责用我们的发布/订阅键建立一个 PubNub 实例，并初始化以下状态变量:

*   **image**——来自未知用户警报的快照图像(我们用占位符图像初始化它，直到快照警报到达)。
*   **消息**——来自人脸追踪应用的传入提醒消息。
*   **文本** -客户端用户的输入，用于键入用户的名称。
*   **计数**——跟踪我们从哪个未知用户那里收到警报。

```
export default class App extends React.Component {

  constructor(props) {
    super(props)

    this.pubnub = new PubNubReact({
      publishKey: "YOUR PUBLISH KEY",
      subscribeKey: "YOUR SUBSCRIBE KEY"
    })

    //Base State
    this.state = {
      image: require('./assets/PLACEHOLDER_IMAGE.jpg'),
      message: '',
      text: '',
      count: 0,
    }

    this.pubnub.init(this);
  }

/// .......VVV REST OF THE CODE VVV.......///
```

When our client app first fires up, we declare an asynchronous function that will subscribe to our face tracking alert channel and handle message events. In this case, we receive the ID (count of unknown user) as well as the snapshot image URL (from Cloudinary) of the unknown user.

```
async componentDidMount() {
  this.setUpApp()    
}

async setUpApp(){
  this.pubnub.getMessage("global", msg => {
    this.setState({count: msg.message.ID})
    this.setState({image: msg.message.url})
  })

  this.pubnub.subscribe({
    channels: ["global"],
    withPresence: false
  });
}
```

Once that image is received by the mobile app, the client user should then be able to add the unknown user to the face tracker's "**known_faces**" database. We can define a function to set the state of the client user's input for the unknown user's name.

```
 handleText = (name) => {
   this.setState({ text: name })
}

```

我们还可以编写一个函数来发布添加的用户名和 ID。

```
 publishName = (text) => {
  this.pubnub.publish({
    message: {
      ID: this.state.count,
      name: text,
    },
    channel: "ch1"
  });
}

```

## 创建和呈现应用程序组件

在屏幕顶部，我们将呈现来自“未知用户”警报的快照图像。该图像的来源将是我们从保存到 state 的警报消息中获取的 URI。

```
` <Image
  source={{uri: this.state.image}}
  style={{width: 250, height: 250}}/>                  `

```

在那下面，我们可以显示一个合适的标题。

```
<Text>{'Do You Know This Person?'}</Text>
```

然后，我们创建一个文本输入组件来存储要添加到人脸追踪器中的用户名，如果客户端决定这样做的话。

```
<TextInput style = {styles.input}
         underlineColorAndroid = "transparent"
         placeholder = "Name"
         placeholderTextColor = "#9a73ef"
         autoCapitalize = "none"
         onChangeText = {this.handleText}/>
```

最后，我们创建一个带有 TouchableOpacity 的 submit 按钮来发布添加的用户名，以便我们的面部跟踪器添加到系统中:

```
<TouchableOpacity
    style = {styles.submitButton}
    onPress = {
      () => this.publishName(this.state.text)
    }>
      <Text>"SUBMIT"</Text>
</TouchableOpacity>
```

将所有这些组件包装在一个 **<视图></视图>** 中，就可以了！

## 运行程序

首先，通过在客户端应用程序的目录中打开终端，在 Android 或 iOS 上启动 React Native 客户端应用程序。

```
react-native run-ios

or

react-native run-android
```

然后，在另一个终端窗口中，运行 Python 人脸追踪器。

```
python facetracker.py
```

## 如果你还想吃更多...

欢迎在[devrel@pubnub.com](mailto:devrel@pubnub.com)向我们发送您的任何问题、疑虑或评论。

如果你仍然渴望更多的 PubNub 机器学习内容，这里有一些你可能感兴趣的其他文章:

*   [实时机器学习:用 PubNub 在线学习](https://www.pubnub.com/blog/realtime-machine-learning-online-learning-with-pubnub/)
*   [pub nub 如何加速你的机器学习算法](https://www.pubnub.com/blog/pubnub-and-machine-learning-algorithm/)
*   [认知时代:大数据、实时和边缘(计算)](https://www.pubnub.com/blog/cognitive-era-big-data-realtime-edge-computing/)