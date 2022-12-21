# 捕捉网络摄像头的图片。网

> 原文：<https://dev.to/documentednerd/capturing-web-cam-pictures-with-net-1n5e>

因此，我最近在做一个项目，我们需要能够让笔记本电脑上的网络摄像头拍照，然后通过 web api 端点发送这些图像。

基本上，这背后的用例很多，但这是围绕支持使用微软认知服务所做的工作。该项目本身是微软智能信息亭的精简版。

所以说实话，我原以为这个问题会比实际困难得多。有一个很棒的图书馆制作了这个作品，叫做“一座桥”。视频，我可以从 nuget 安装，从那里这是所需的代码:

```
static void Main(string[] args) { // enumerate video devices var videoDevices = new FilterInfoCollection( FilterCategory.VideoInputDevice); // create video source VideoCaptureDevice videoSource = new VideoCaptureDevice( videoDevices[0].MonikerString); // set NewFrame event handler videoSource.NewFrame += new NewFrameEventHandler(video\_NewFrame); videoSource.ProvideSnapshots = true; // start the video source videoSource.Start(); //videoSource.SignalToStop(); Console.ReadLine(); } 
```

上面的代码只是识别这台机器上可用的视频/照片捕获设备，并利用第一个。然后连接一个事件来处理新的帧捕获。

从那里，一旦我打开“视频源”。start()；"应用程序开始执行 NewFrameEventHandler 来处理它。

```
private static void video\_NewFrame(object sender, NewFrameEventArgs eventArgs) { // get new frame Bitmap bitmap = eventArgs.Frame; var fileName = @"C:\temp\camera\File\_Frame.jpg"; //bitmap.Save(string.Format(fileName)); EncoderParameters encoderParameters = new EncoderParameters(1); encoderParameters.Param[0] = new EncoderParameter(System.Drawing.Imaging.Encoder.Quality, 100L); bitmap.Save(fileName, GetEncoder(ImageFormat.Jpeg), encoderParameters); var bytes = bitmap.ToByteArray(ImageFormat.Bmp); Thread.Sleep(1000); }public static ImageCodecInfo GetEncoder(ImageFormat format) { ImageCodecInfo[] codecs = ImageCodecInfo.GetImageDecoders(); foreach (ImageCodecInfo codec in codecs) { if (codec.FormatID == format.Guid) { return codec; } } return null; } 
```

现在，上面的事件处理程序将获取代码，提取 bitmp，将其转换为 Jpg 并保存文件。但是另外，我在末尾添加了将它转换成字节数组的逻辑。这将允许您将它推送到 HTTP 端点，由您需要的任何服务进行处理。对于 74 行代码来说非常简单。