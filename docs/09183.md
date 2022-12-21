# 权限相机滚动错误反应本机

> 原文：<https://dev.to/aoreclause/permission-camera-roll-error-react-native-4a93>

我试图在我的 react 本机应用程序上访问相机胶卷，但它没有反馈

这是我的代码

`onImageUpload = async () => {
const {status: cameraRollPerm} = await PermissionsAndroid.askAsync(
PermissionsAndroid.PERMISSIONS.CAMERA_ROLL,
try{
if (cameraRollPerm === 'granted') {
let pickerResult = await ImagePicker.lauchImageAsync({
allowsEditing: true,
aspect:[4,3]
});
console.log(
'ready to upload... pickerResult json:'+ JSON.stringify(pickerResult)
);`

我试过了

`await PermissionsAndroid.request(
PermissionsAndroid.PERMISSIONS.CAMERA,`