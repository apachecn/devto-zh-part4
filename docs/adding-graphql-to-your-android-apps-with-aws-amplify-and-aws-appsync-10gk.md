# 使用 AWS Amplify 和 AWS AppSync 将 GraphQL 添加到您的 Android 应用程序中

> 原文：<https://dev.to/theworstdev/adding-graphql-to-your-android-apps-with-aws-amplify-and-aws-appsync-10gk>

AWS AppSync 可帮助您构建具有实时和离线功能的数据驱动型应用程序。AppSync Android SDK 使您能够将您的应用程序与 AppSync 服务集成在一起，它基于此处的 Apollo 项目。SDK 支持多种授权模式，处理订阅握手协议以实时更新数据，并具有内置的离线支持功能，可以轻松集成到您的应用中。

AWS Amplify 让您可以快速地向应用程序添加后端功能，这样您就可以专注于您的应用程序代码。在这种情况下，您可以使用 Amplify 快速设置一个 GraphQL API 以及一个后备数据库来管理您的数据。

## 设置项目

### 创建新的 Android 项目

首先，我们需要创建一个新的 Android 项目。

如果您已经安装并配置了 Android Studio，那么继续创建一个新的 Android 项目。

在**添加活动到移动**屏幕中，选择**清空活动**。点击**下一步**，保留默认值，点击**完成**完成项目设置。

### 导入 AWS AppSync SDK 和配置

为了在我们新的 Android 项目中使用 AWS AppSync，修改**项目**的`build.gradle`文件，并在构建脚本中添加以下依赖项:

```
classpath 'com.amazonaws:aws-android-sdk-appsync-gradle-plugin:2.6.+' 
```

Enter fullscreen mode Exit fullscreen mode

接下来，在**应用**的`build.gradle`中添加一个`apply plugin: 'com.amazonaws.appsync'`插件以及 AWS AppSync 和 MqttServices 的依赖项。

因此，您的`build.gradle`应该看起来像:

```
apply plugin: 'com.android.application'
apply plugin: 'com.amazonaws.appsync'
android {
    // ... typical items
}
dependencies {
    // ... typical dependencies

    implementation 'com.amazonaws:aws-android-sdk-appsync:2.6.+'
    implementation 'org.eclipse.paho:org.eclipse.paho.client.mqttv3:1.2.0'
    implementation 'org.eclipse.paho:org.eclipse.paho.android.service:1.1.1'
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，将您的`AndroidManifest.xml`更新为`<uses-permissions>`的网络通话和离线状态。另外，在 MqttService 的`<application>`下添加一个`<service>`条目，这样我们就可以使用订阅:

```
<uses-permission android:name="android.permission.INTERNET"/>
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
<uses-permission android:name="android.permission.WAKE_LOCK" />
<uses-permission android:name="android.permission.READ_PHONE_STATE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/>

        <!--other code-->

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">

        <service android:name="org.eclipse.paho.android.service.MqttService" />

        <!--other code-->
    </application> 
```

Enter fullscreen mode Exit fullscreen mode

> 现在是构建项目的好时机，确保没有问题。

## 安装 AWS Amplify CLI 并初始化新的 AWS Amplify 项目

### 安装 AWS Amplify CLI

为了充分利用 AWS Amplify CLI 工具链，让我们安装 AWS Amplify CLI。打开您的终端，在命令行中运行以下命令:

```
npm install -g @aws-amplify/cli 
```

Enter fullscreen mode Exit fullscreen mode

### 初始化 AWS Amplify 项目

接下来，让我们为您的 Android 应用程序初始化一个新的 AWS Amplify 项目。

`cd`在终端窗口中进入 Android Studio 项目根目录，并运行以下命令:

```
amplify init 
```

Enter fullscreen mode Exit fullscreen mode

*   选择你的默认编辑器: **Visual Studio 代码(或者你最喜欢的编辑器)**
*   请选择您正在构建的应用类型: **android**
*   你的 Res 目录在哪里:(app/src/main/res): **回车接受默认**
*   是否要使用 AWS 配置文件？ **Y**
*   请选择您想要使用的个人资料:**您的个人资料**

CloudFormation 现在将为您的 AWS 帐户运行，以配置初始基础架构来支持您的应用程序。完成后，AWS Amplify CLI 工具链将初始化一个新项目，您将在应用程序的项目目录中看到几个新文件和文件夹: **amplify** 和**。amplifyrc** 。这些文件包含项目的配置。

## 添加 GraphQL API，认证并生成客户端代码

AWS Amplify 工具链为我们提供了 API 创建、认证和客户端代码生成的简化流程。让我们首先在你的应用程序的**根目录**下运行以下命令:

```
amplify add api 
```

Enter fullscreen mode Exit fullscreen mode

回答以下问题:

*   请从上述服务中选择一项**图表 QL**
*   提供 API 名称: **AmplifyAndroidExample**
*   为 API 选择一个授权类型: **Amazon Cognito 用户池**
*   您想使用默认的身份验证和安全配置吗？**是的，使用默认配置。**
*   你有带注释的 GraphQL 模式吗？ **N**
*   您想要引导模式创建吗？ **Y**
*   什么最能描述您的项目:**带有字段的单个对象(例如，带有 ID、名称、描述的“Todo”)**
*   您想现在编辑模式吗？(Y/n) **Y**

出现提示时，将模式更新为以下内容:

```
 type  Todo  @model  {  id:  ID!  title:  String!  description:  String  } 
```

Enter fullscreen mode Exit fullscreen mode

*   回到终端，按 enter 键继续。

接下来，让我们通过运行:
将配置推送到您的 AWS 帐户

```
amplify push 
```

Enter fullscreen mode Exit fullscreen mode

将提示您添加的更改:

```
| Category | Resource name          | Operation | Provider plugin   |
| -------- | ---------------------- | --------- | ----------------- |
| Auth     | cognito12345678        | Create    | awscloudformation |
| Api      | AmplifyAndroidWorkshop | Create    | awscloudformation | 
```

Enter fullscreen mode Exit fullscreen mode

*   您确定要继续吗？(Y/n) **Y**

现在将提示您为全新的 API 生成代码:

*   是否要为新创建的 GraphQL API (Y/n) **Y** 生成代码
*   输入 graphql 查询、突变和订阅的文件名模式(app/src/main/graphql/* */* )。graphql): **按回车键接受默认值**
*   您想要生成/更新所有可能的 GraphQL 操作-查询、突变和订阅(Y/n) **Y**

CloudFormation 将再次运行，以便为您的 AWS 帐户更新新创建的 API 和身份验证机制。这个过程可能需要几分钟。

> 要在新的 AWS AppSync API 创建后随时查看它，请访问位于[https://console.aws.amazon.com/appsync](https://console.aws.amazon.com/appsync)的仪表板。还要确保您的区域设置正确。
> 
> 要在创建后的任何时候查看新的 Cognito 认证服务，请转到位于[https://console.aws.amazon.com/cognito/](https://console.aws.amazon.com/cognito/)的仪表板。还要确保您的区域设置正确。

在 CloudFormation 完成更新云中的资源之后，您将获得一个 GraphQL API 端点，并且生成的 GraphQL 语句将在您的项目中可用。

> 尽管它对您来说是透明的，并且我们可以立即开始使用 API，但是您总是可以在 Android Studio 的`app/src/main/graphql/com/amazonaws/amplify/generated/graphql`下检查新生成的 GraphQL 查询、变异和订阅。

## 构建 Android 应用程序

我们的后端准备好了。让我们开始在我们的 Android 应用程序中使用它吧！

在开始之前，如果您还没有，您应该打开自动导入。我们正在使用许多库！为此，打开**首选项**->-**编辑器**->-**常规**->-**自动导入** - >检查**动态添加明确的导入**。

**构建**您的项目，开始客户端代码生成过程。这个 gradle 构建过程将创建您可以立即使用的所有本机对象类型。

> 如果您很好奇，可以切换到`Project`视图，并浏览到`app/build/generated/source/appsync/com/amazonaws/amplify/generated/graphql/`来检查所有生成的对象类型、查询、变异和订阅 Java 类。

### 添加认证

由于我们之前已经配置了使用`Cognito User Pool`进行身份验证，我们需要将身份验证集成到我们的应用程序中。为简单起见，我们将利用 AWS Mobile library 的内置 SignInUI 进行 Cognito 认证。

打开您的应用程序的`build.gradle`，并添加以下依赖项:

```
 // Mobile Client for initializing the SDK
    implementation('com.amazonaws:aws-android-sdk-mobile-client:2.7.+@aar') { transitive = true }

    // Cognito UserPools for SignIn
    implementation('com.amazonaws:aws-android-sdk-auth-userpools:2.7.+@aar') { transitive = true }

    // Sign in UI Library
    implementation('com.amazonaws:aws-android-sdk-auth-ui:2.7.+@aar') { transitive = true } 
```

Enter fullscreen mode Exit fullscreen mode

右键点击你的应用目录，选择**新建**->T3】活动->T5】清空活动。将您的活动命名为`AuthenticationActivity`，选中复选框**启动器活动**，然后单击**完成**。

在`AuthenticationActivity.java`类中，将类修改如下:

```
 public class AuthenticationActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_authentication);

        // Add a call to initialize AWSMobileClient
        AWSMobileClient.getInstance().initialize(this, new AWSStartupHandler() {
            @Override
            public void onComplete(AWSStartupResult awsStartupResult) {
                SignInUI signin = (SignInUI) AWSMobileClient.getInstance().getClient(
                        AuthenticationActivity.this,
                        SignInUI.class);
                signin.login(
                        AuthenticationActivity.this,
                        MainActivity.class).execute();
            }
        }).execute();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们确保身份验证活动是我们的启动器活动。打开`AndroidManifest.xml`，确保`<intent-filter>`块被指定用于`AuthenticationActivity`，如下所示。也可以去掉`MainActivity`的`<intent-filter>`和`android:theme`。

```
<!-- ... Other Code... -->

<activity
    android:name=".MainActivity"
    android:label="@string/app_name">
</activity>
<activity
    android:name=".AuthenticationActivity">
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
</activity> 
```

Enter fullscreen mode Exit fullscreen mode

在仿真器中构建并启动您的应用程序。登录界面显示如下:

[![](img/8a5786517ddd43a196031464c45aa126.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fN21w47R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/practicaldev/image/fetch/s--Gw_SBLBl--/c_limit%252Cf_auto%252Cfl_progressive%252Cq_auto%252Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wa9vmnlf3onpzaag634o.png)

现在让我们添加一个用户。在您的模拟器中，单击`Create New Account`。输入用户名，选择复杂密码(最小长度为 8，包括大写字母、小写字母、特殊字符和数字)。请输入有效的电子邮件地址，以便接收验证码。

您的确认码将很快到达您指定的电子邮件收件箱。在下一个屏幕中输入该代码以完成注册过程。

成功登录后，应该会看到一个空白的屏幕，就是我们的`MainActivity`。

> 要查看在 Cognito 用户池中创建的新用户，请返回位于[https://console.aws.amazon.com/cognito/](https://console.aws.amazon.com/cognito/)的仪表板。还要确保您的区域设置正确。

### 创建 AppSync 客户端

我们现在需要创建一个`AWSAppSyncClient`来执行 API 调用。在您的包中添加一个新的`ClientFactory.java`类:

```
 public class ClientFactory {
    private static volatile AWSAppSyncClient client;

    public static synchronized void init(final Context context) {
        if (client == null) {
            final AWSConfiguration awsConfiguration = new AWSConfiguration(context);

            CognitoUserPoolsSignInProvider cognitoUserPoolsSignInProvider =
                    (CognitoUserPoolsSignInProvider) IdentityManager.getDefaultIdentityManager().getCurrentIdentityProvider();
            BasicCognitoUserPoolsAuthProvider basicCognitoUserPoolsAuthProvider =
                    new BasicCognitoUserPoolsAuthProvider(cognitoUserPoolsSignInProvider.getCognitoUserPool());

            client = AWSAppSyncClient.builder()
                    .context(context)
                    .awsConfiguration(awsConfiguration)
                    .cognitoUserPoolsAuthProvider(basicCognitoUserPoolsAuthProvider)
                    .build();
        }
    }

    public static synchronized AWSAppSyncClient appSyncClient() {
        return client;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个`ClientFactory`类将提供一个 AppSync 客户端，我们可以利用它来执行数据访问活动。

### 查询数据

我们的列表中还没有任何数据，但是一旦有了，就让我们建立显示它们的能力。

#### 添加一个 RecyclerView 来显示项目列表

现在让我们开始构建允许显示项目的应用程序！

我们将使用`RecyclerView`来显示数据。打开`src/res/layout/activity_main.xml`，切换到`Text`视图，将`<TextView>`替换为

```
<android.support.v7.widget.RecyclerView
            android:id="@+id/recycler_view"
            android:layout_width="match_parent"
            android:layout_height="wrap_content" /> 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们定义列表中的每一项是什么样子的。右击你的`res/layout`文件夹，添加一个新的`Layout resource file`。姑且称之为`recyclerview_row.xml`。将`Root element`改为`LinearLayout`，其余默认，按`OK`。

切换到`recyclerview_row.xml`的`Text`视图，修改布局如下:

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical"
    android:padding="10dp">

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="10dp"
        android:paddingLeft="10dp"
        android:textSize="15dp"
        android:id="@+id/txt_title"
        />
    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="10dp"
        android:paddingLeft="10dp"
        android:textSize="15dp"
        android:id="@+id/txt_description"
        />

</LinearLayout> 
```

Enter fullscreen mode Exit fullscreen mode

由于我们使用的是 RecyclerView，我们需要为它提供一个适配器。添加一个新的 Java 类`MyAdapter.java`，如下所示，它扩展了`RecyclerView.Adapter` :

```
 public class MyAdapter extends RecyclerView.Adapter<MyAdapter.ViewHolder> {

    private List<ListTodosQuery.Item> mData = new ArrayList<>();;
    private LayoutInflater mInflater;

    // data is passed into the constructor
    MyAdapter(Context context) {
        this.mInflater = LayoutInflater.from(context);
    }

    // inflates the row layout from xml when needed
    @Override
    public ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        View view = mInflater.inflate(R.layout.recyclerview_row, parent, false);
        return new ViewHolder(view);
    }

    // binds the data to the TextView in each row
    @Override
    public void onBindViewHolder(ViewHolder holder, int position) {
        ListTodosQuery.Item item = mData.get(position);
        holder.txt_title.setText(item.title());
        holder.txt_description.setText(item.description());
    }

    // total number of rows
    @Override
    public int getItemCount() {
        return mData.size();
    }

    // resets the list with a new set of data
    public void setItems(List<ListTodosQuery.Item> items) {
        mData = items;
    }

    // stores and recycles views as they are scrolled off screen
    class ViewHolder extends RecyclerView.ViewHolder {
        TextView txt_title;
        TextView txt_description;

        ViewHolder(View itemView) {
            super(itemView);
            txt_title = itemView.findViewById(R.id.txt_title);
            txt_description = itemView.findViewById(R.id.txt_description);
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

注意类级别变量`mData`。这是一个类型为`ListTodosQuery.Item`的列表，它是基于我们的模式生成的 GraphQL 类型。

我们还公开了一个`setItems`方法，允许在外部重新设置我们的数据集。

#### 构建屏幕以填充 RecyclerView

打开`MainActivity.java`，修改类实现一个`query`方法，并填充`RecyclerView` :

```
 public class MainActivity extends AppCompatActivity {

    RecyclerView mRecyclerView;
    MyAdapter mAdapter;

    private ArrayList<ListTodosQuery.Item> mTodos;
    private final String TAG = MainActivity.class.getSimpleName();

    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mRecyclerView = findViewById(R.id.recycler_view);

        // use a linear layout manager
        mRecyclerView.setLayoutManager(new LinearLayoutManager(this));

        // specify an adapter (see also next example)
        mAdapter = new MyAdapter(this);
        mRecyclerView.setAdapter(mAdapter);

        ClientFactory.init(this);
    }

    @Override
    public void onResume() {
        super.onResume();

        // Query list data when we return to the screen
        query();
    }

    public void query(){
        ClientFactory.appSyncClient().query(ListTodosQuery.builder().build())
                .responseFetcher(AppSyncResponseFetchers.CACHE_AND_NETWORK)
                .enqueue(queryCallback);
    }

    private GraphQLCall.Callback<ListTodosQuery.Data> queryCallback = new GraphQLCall.Callback<ListTodosQuery.Data>() {
        @Override
        public void onResponse(@Nonnull Response<ListTodosQuery.Data> response) {

            mTodos = new ArrayList<>(response.data().listTodos().items());

            Log.i(TAG, "Retrieved list items: " + mTodos.toString());

            runOnUiThread(new Runnable() {
                @Override
                public void run() {
                    mAdapter.setItems(mTodos);
                    mAdapter.notifyDataSetChanged();
                }
            });
        }

        @Override
        public void onFailure(@Nonnull ApolloException e) {
            Log.e(TAG, e.toString());
        }
    };
} 
```

Enter fullscreen mode Exit fullscreen mode

`appSyncClient`负责查询 AppSync GraphQL 端点。我们选择使用`CACHE_AND_NETWORK`模式，因为它将首先检索本地缓存中的数据，同时通过网络获取最新数据。一旦获取完成，`queryCallback`再次被调用，我们的数据集用最新的数据更新。根据不同的应用程序数据获取需求，可以使用其他缓存或仅网络/优先模式。

**再次构建**你的应用程序，确保没有错误。仍然会显示一个空白屏幕，但是您应该能够在 Logcat 窗口中看到日志，指示查询已成功完成，如下所示:

`09-28 10:32:16.789 11605-11699/com.example.demo.amplifyandroidexample I/MainActivity: Retrieved list items: []`

### 修改数据:添加待办事项

在 [AppSync 控制台](https://console.aws.amazon.com/appsync/home)中，导航到`Queries`部分并创建几个待办事项:

[![](img/f401b31724d0d0418d94b3576f0360af.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WbBbH4Cp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tw15qw82cissltgt7ogm.png)

然后刷新你的应用程序，你现在应该有 Todos 显示在你的主活动中！

有关 GraphQL with Amplify 和 AppSync 的更多信息，请点击此处查看文档。

> 此外，您应该查看 AWS Device Farm，以便在各种设备和操作系统上测试您的应用程序([https://dev . to/kk emple/how-to-set-up-end-to-end-tests-for-Android-with zero-code-1ka](https://dev.to/kkemple/how-to-set-up-end-to-end-tests-for-android-with-zero-code-1ka))。

最后，你可以在这里找到更多关于 Android 的信息:

*   [https://aws-amplify.github.io/docs/android/start](https://aws-amplify.github.io/docs/android/start)
*   [https://amplify.aws/community/posts](https://amplify.aws/community/posts)