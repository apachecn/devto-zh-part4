# 如何创建一个 Android 应用程序:Android Room 第 2 部分

> 原文：<https://dev.to/edrome/how-to-create-an-android-app-android-room-part-2-3bfm>

### 我们还有一周的时间来继续之前的帖子

在上一篇文章中，我们设计了一个实体数据库来存储用户配置文件。这次我们要好好利用它。

让我们开始添加一个类型转换器文件，上周没有添加。这是存储日期数据类型所必需的。

```
import androidx.room.TypeConverter;

import java.util.Date;

public class Converters {

    @TypeConverter
    public static Date fromTimestamp(Long value) {
        return value == null ? null : new Date(value);
    }

    @TypeConverter
    public static Long dateToTimestamp(Date date) {
        return date == null ? null : date.getTime();
    }

} 
```

然后，在 room 文件上添加类型转换器注释，如果已经编译了项目，则更改版本。

```
@Database(entities = {profileModel.class}, version = 2, exportSchema = false)
@TypeConverters({Converters.class})
public abstract class databaseRoom extends RoomDatabase {
    ...
} 
```

修改调用 POJO 实例和调用方法`getProfile`的主活动。

> *   The constructor takes application as a parameter.
> *   The getProfile method takes an ID as a parameter.

```
profilePojo = new profilePOJO(getApplication());
user = profilePojo.getProfile(1); 
```

`getProfile`方法将用于检索有关用户的信息。一旦应用程序被启动，第一个动作将是在数据库中寻找信息，如果它没有信息，所有编辑文本将显示为空；否则，将显示带有信息的编辑文本。

下面描述的逻辑如下所示。

```
if (user != null) {
    username.setText(user.getUsername());
    byte[] bitmapdata = user.getPhoto();
    Glide.with(this).load(byteArray2Bitmap(bitmapdata)).into(imageview);
    switch (user.getGender()) {
        case "male":
            male.setChecked(true);
            female.setChecked(false);
            break;
        case "female":
            female.setChecked(true);
            male.setChecked(false);
           break;
      }
      birthday.setText(parseDate(user.getBirthday()));
} 
```

现在是按下保存按钮时的插入或更新逻辑，

```
save.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View view) {
        byte[] img;
        String gender;
        if (bitmap == null) {
            img = user.getPhoto();
        } else {
            img = convertBitmap2byte(bitmap);
        }

        if (female.isChecked()) {
            gender = "female";
        } else {
            gender = "male";
        }

        if (user == null) {
            user = new profileModel(
                        username.getText().toString(),
                        gender,
                        birthdate,
                        img);

            profilePojo.insert(user);
         } else {
             user.setGender(gender);
             user.setPhoto(img);
             user.setUsername(username.getText().toString());
             if (birthdate != null) {
                 user.setBirthday(birthdate);
             }
             profilePojo.update(user);
         }
     }
}); 
```

在验证用户为空之前，从主活动中收集数据，并将它们设置到全局变量中。之后，如果用户为空，则创建用户并执行插入操作，如果用户不为空，则执行更新操作。

注意，我们又创建了两个方法`parseDate`和`convertBitmap2byte`。第一个用于将日期变量转换成字符串。

```
private String parseDate(Date date) {
    Calendar cal = Calendar.getInstance();
    cal.setTime(date);
    int day = cal.get(Calendar.DAY_OF_MONTH);
    int month = cal.get(Calendar.MONTH);
    int year = cal.get(Calendar.YEAR);

    String sDay = String.valueOf(day);
    String sMonth = getMonth(month);
    String sYear = String.valueOf(year);

    sDay = addLeftZero(sDay);
    return (sMonth + " " + sDay + ", " + sYear);
} 
```

第二个函数用于将位图图像转换为字节数组并存储到数据库中，而不是在图库中创建新文件夹。

```
private Bitmap byteArray2Bitmap(byte[] bitmapdata) {
    return BitmapFactory.decodeByteArray(bitmapdata, 0, bitmapdata.length);
} 
```

Aaaand...就是这样。现在我们有一个功能配置文件活动，下一个帖子将是这个系列的最后一个，检查一点设计。我不太擅长，但我会尽力带些有趣的东西。

和往常一样，这个项目可以按照你想要的目的在 [github](https://github.com/EdRome/AppProfile) 上分叉。