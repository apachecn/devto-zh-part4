# 未找到类- org/slf4j/LoggerFactory Gradle

> 原文：<https://dev.to/zillerium/class-not-found-org-slf4j-loggerfactory-gradle-3d2b>

我用的是 Intellij 和 Gradle。当我运行 jar 文件时-

Trevor @ Trevor-Lenovo-YOGA-510-14 ast:~/deadline/master 5/out/artifacts/master 5 _ main _ jar $ Java-jar master 5 . main . jar 线程中出现异常" main " Java . lang . noclassdeffounderror:org/slf4j/logger factory at block get connection。(BlockgetConnection.java:12)在 block getaccount . getaccount by name(block getaccount . Java:31)在 block getstart . main(block getstart . Java:8)原因:Java . lang . classnotfoundexception:org . slf4j . logger factory 在 Java . net . URL class loader . find class(URL class loader . Java:382)在 Java . lang . class loader . load class(class loader . Java:424)在 sun . misc . launcher＄appclass loader . load class(launcher . Java:349)在...3 更

我没有明确地记录日志，并且我假设在合理的逻辑中，需要以某种方式记录日志。我包含了我的 build.gradle，我包含了一些日志依赖项。有什么解决办法吗？

代码(主类)

导入 ch .正派. SDK . model . account；

公共类 BlockgetStart {

```
public static void main(String[] args) {
    BlockgetAccount anAccount = new BlockgetAccount();
    Account myAcct = anAccount.getAccountByName("trevor3");
} 
```

}
使用了此类的实例-

导入 ch .正派. SDK . dcore API；
导入 ch .正派. SDK . crypto . address；
导入 ch . delicate . SDK . crypto . credentials；
导入 ch .正派. SDK . model . account；
导入 ch . delicate . SDK . model . asset amount；
导入 ch . delicate . SDK . model . fee；
导入 ch . delicate . SDK . model . transaction confirmation；
导入 org . spring framework . beans . factory . annotation . auto wired；
导入 org . spring framework . stereotype . component；
导入 org . Apache . logging . log4j . logger；
导入 org . Apache . logging . log4j . log manager；

@ Component
public class block get account {
//连接账户
private static final Long AMOUNT _ OF _ DCT _ REQUIRED _ FOR _ CREATION = 100000 l；

```
@Autowired
private BlockgetConnection connectionExample;
@Autowired
private BlockgetLogin loginExample;
@Autowired
private BlockgetGenerateKeys generateKeys;

/**
 * Example of getting any account by its name.
 *
 * @param accountName name of the account e.g. dw-account
 * @return Account instance for given account name
 */
public Account getAccountByName(String accountName) {

    connectionExample = new BlockgetConnection();
    final DCoreApi dcoreApi = connectionExample.connect();

    return dcoreApi
            .getAccountApi()
            .getByName(accountName)
            .blockingGet();
}

/**
 * Example of account creation with initial fee.
 *
 * @param newAccountName Unique account name that you wish to create.
 * @return Confirmation about transaction
 */
public TransactionConfirmation createAccount(String newAccountName) {

    final DCoreApi dcoreApi = connectionExample.connect();
    final Credentials credentials = loginExample.login();
    final Address newAccountPublicKey = generateKeys.generateKeys();
    final AssetAmount dctAssetAmount = new AssetAmount(AMOUNT_OF_DCT_REQUIRED_FOR_CREATION);
    final Fee initialFee = new Fee(dctAssetAmount.getAssetId(), AMOUNT_OF_DCT_REQUIRED_FOR_CREATION);

    return dcoreApi.getAccountApi().create(
            credentials,
            newAccountName,
            newAccountPublicKey,
            initialFee
    ).blockingGet();
} 
```

}

Build.gradle -

插件{
id ' Java '
}

group 'com.blockget.Master5'
版本' 1.0-快照'

sourceCompatibility = 1.8

jar { manifest { attributes ' Main-Class ':' block getstart ' } }

all projects {
repositories {
maven central()
maven { URL '[https://JIT pack . io](https://jitpack.io)' }
}
}

依赖关系{
//[https://mvn repository . com/artifact/org.springframework/spring-context](https://mvnrepository.com/artifact/org.springframework/spring-context)T3】//[https://mvn repository . com/artifact/org . spring framework/spring-context](https://mvnrepository.com/artifact/org.springframework/spring-context)
编译组:' org . spring framework '，名称:' spring-context '，版本:' 5 . 1 . 8 . release '
/[https://mvnrepository.com/artifact/org.slf4j/slf4j-ap](https://mvnrepository.com/artifact/org.slf4j/slf4j-ap)
编译组:' org.slf4j '，名称:' slf4j

```
testCompile group: 'junit', name: 'junit', version: '4.12'
implementation 'com.github.DECENTfoundation:DCoreKt-SDK:2.3.1' 
```

}
我得到这个是因为罐子破了——

```
trevor@trevor-Lenovo-YOGA-510 
14AST:~/decent/Master5/out/artifacts/Master5_main_jar$ jar tf 
Master5.main.jar 
META-INF/MANIFEST.MF
META-INF/
BlockgetNftMgr.class
BlockgetLogin.class
BlockgetConnection.class
BlockgetNft.class
BlockgetStart.class
BlockgetAccount.class
BlockgetGenerateKeys.class 
```

有什么解决的办法吗？