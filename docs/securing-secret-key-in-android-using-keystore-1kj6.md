# 使用密钥库保护 Android 中的密钥

> 原文：<https://dev.to/varundwarkani10/securing-secret-key-in-android-using-keystore-1kj6>

在某个时候，我们都想保护我们的数据安全，防止被黑客攻击/逆向工程。基本的安全机制，

a.ProGuard，shrink resources & minify enabled
b .隐藏在清单中
c .隐藏在构建中。Gradle
d .存储在 MySQL DB/Room DB/shared preference
e .隐藏在 Strings.xml 中

所有这些方法都不能提供最大的安全性。最常见和已知的保护密钥的逻辑可以通过从 dex->jar 文件中反转并获取密钥，或者通过找到设备并访问设备存储来绕过。但是，还有一种方法可以打败它们。应用程序通常使用这种机制来存储非常敏感的数据，如信用卡详细信息、银行帐户等。

密钥库与通常用于存储加密密钥的硬件安全绑定在一起。由于密钥库对于每个应用程序来说都是非常特定的，所以黑客很难访问它。做了更多的介绍，现在让我们跳到代码-

在**Cryptor.java**声明几个变量

私有静态最终字符串**转换**= " AES/GCM/no padding "；
私有静态最终字符串**ANDROID _ KEY _ STORE**= " ANDROID keystore "；
私有字节[]**iv**；
私有密钥库**密钥库**；
私有静态最终字符串**SAMPLE _ ALIAS**= " my ALIAS "；

a.转换用于设置将用于编码的算法。iv 被称为初始化向量，它是一个与密钥一起使用的任意数字，用于加密。(可以存储在 SharedPreference、Room DB 或 MySQL DB 之类的公共存储中)。c. SAMPLE_ALIAS 用于访问存储在密钥库中的实体。

# 加密数据

为了用密钥库加密一个值，我们可以这样做，

a.创建一个 Cryptor 类的对象。b .使用 setIv 方法，使用 Cryptor 类中的密钥初始化密码。使用 Cryptor 类中定义的加密函数加密文本。
d .在 SharedPreference 或房间数据库中存储 Iv 和加密文本(Iv 可以公开，不会引起任何问题)。

在**RegistrationActivity.java**T2】

```
Cryptor cryptor = new Cryptor();
try {
    cryptor.setIv();
    prefs.edit().putString("encryptedKey", cryptor.encryptText("text_to_be_encrypted")).apply();
    prefs.edit().putString("keyIv", cryptor.getIv_string()).apply();
    Intent intent = new Intent(RegistrationActivity.this, HomeScreen.class);
    startActivity(intent);
    finish();
} catch (NoSuchPaddingException e) {
    unexpectedError();
    e.printStackTrace();
} catch (NoSuchAlgorithmException e) {
    unexpectedError();
    e.printStackTrace();
} catch (NoSuchProviderException e) {
    unexpectedError();
    e.printStackTrace();
} catch (InvalidAlgorithmParameterException e) {
    unexpectedError();
    e.printStackTrace();
} catch (InvalidKeyException e) {
    unexpectedError();
    e.printStackTrace();
} 
```

在 Cryptor.java，我们定义了以下功能

**1。setIv()方法:**

```
public void setIv() throws NoSuchPaddingException, NoSuchAlgorithmException, NoSuchProviderException, InvalidAlgorithmParameterException, InvalidKeyException {
    Cipher cipher = Cipher.getInstance(TRANSFORMATION);
    cipher.init(Cipher.ENCRYPT_MODE, getSecretKey_en());
    iv = cipher.getIV();
} 
```

**2。getSecretKey_en()方法:**

```
@NonNull
private SecretKey getSecretKey_en() throws NoSuchAlgorithmException,
        NoSuchProviderException, InvalidAlgorithmParameterException {
    final KeyGenerator keyGenerator;
    if (android.os.Build.VERSION.SDK_INT >= android.os.Build.VERSION_CODES.M) {
        keyGenerator = KeyGenerator.getInstance(KeyProperties.KEY_ALGORITHM_AES, ANDROID_KEY_STORE);
        keyGenerator.init(new KeyGenParameterSpec.Builder(Cryptor.SAMPLE_ALIAS,
                KeyProperties.PURPOSE_ENCRYPT | KeyProperties.PURPOSE_DECRYPT)
                .setBlockModes(KeyProperties.BLOCK_MODE_GCM)
                .setEncryptionPaddings(KeyProperties.ENCRYPTION_PADDING_NONE)
                .build());
        return keyGenerator.generateKey();
    } else {
        keyGenerator = KeyGenerator.getInstance(KeyProperties.KEY_ALGORITHM_AES, ANDROID_KEY_STORE);
        keyGenerator.init(new KeyGenParameterSpec.Builder(Cryptor.SAMPLE_ALIAS,
                KeyProperties.PURPOSE_ENCRYPT | KeyProperties.PURPOSE_DECRYPT)
                .setBlockModes(KeyProperties.BLOCK_MODE_GCM)
                .setEncryptionPaddings(KeyProperties.ENCRYPTION_PADDING_NONE)
                .build());
        return keyGenerator.generateKey();
    }
} 
```

**3。encrypt text(String String _ to _ encrypt):**

```
public String encryptText(String string_to_encrypt) {
    try {
        final byte[] encryptedText = encryptData(string_to_encrypt);
        return Base64.encodeToString(encryptedText, Base64.DEFAULT);
    } catch (NoSuchAlgorithmException | NoSuchProviderException |
            NoSuchPaddingException | InvalidKeyException e) {
        e.printStackTrace();
    } catch (InvalidAlgorithmParameterException |
            IllegalBlockSizeException | BadPaddingException e) {
        e.printStackTrace();
    }
    return "";
} 
```

**4。encrypt data(String text _ to _ encrypt):**

```
private byte[] encryptData(final String textToEncrypt)
        throws NoSuchAlgorithmException,
        NoSuchProviderException, NoSuchPaddingException, InvalidKeyException,
        InvalidAlgorithmParameterException, BadPaddingException,
        IllegalBlockSizeException {
    final Cipher cipher = Cipher.getInstance(TRANSFORMATION);
    cipher.init(Cipher.ENCRYPT_MODE, getSecretKey_en());
    iv = cipher.getIV();
    return (cipher.doFinal(textToEncrypt.getBytes(StandardCharsets.UTF_8)));
} 
```

**5。getIv_string() :**

```
public String getIv_string() {
    return Base64.encodeToString(iv, Base64.DEFAULT);
} 
```

**解释:**我们使用特定算法的密钥库和别名生成密钥。生成的密钥用于初始化密码并获得 IV。加密文本函数使用文本和 iv 来加密密钥库中的文本，并给出可以存储在任何通用存储介质中的加密文本。

# 解密数据

为了用密钥库解密一个值，我们可以通过创建一个 Cryptor 类的对象来完成。
b .初始化密钥库实例。
c .通过传递加密文本和 iv(存储在 SharedPreference 或房间数据库中)来使用 decrypt 函数。

在**HomeScreen.java**T2】

```
String iv = prefs.getString("keyIv", "null");
String encrypted = prefs.getString("encryptedKey", "");
try {
    Cryptor cryptor = new Cryptor();
    cryptor.initKeyStore();
    String decrypted = cryptor.decryptText(encrypted, iv);
} catch (KeyStoreException e) {
    e.printStackTrace();
} catch (CertificateException e) {
    e.printStackTrace();
} catch (NoSuchAlgorithmException e) {
    e.printStackTrace();
} catch (IOException e) {
    e.printStackTrace();
} 
```

在**Cryptor.java**中，增加以下功能

1 . init keystore():T2]

```
public void initKeyStore() throws KeyStoreException, CertificateException,
        NoSuchAlgorithmException, IOException {
    keyStore = KeyStore.getInstance(ANDROID_KEY_STORE);
    keyStore.load(null);
} 
```

**2。decrypt text(String encrypted _ String，String iv) :**

```
public String decryptText(String encrypted, String iv) {
    try {
        return decryptData(encrypted, Base64.decode(iv,Base64.DEFAULT));
    } catch (UnrecoverableEntryException | NoSuchAlgorithmException |
            KeyStoreException | NoSuchPaddingException | InvalidKeyException e) {
        e.printStackTrace();
    } catch (IllegalBlockSizeException | BadPaddingException | InvalidAlgorithmParameterException e) {
        e.printStackTrace();
    }
    return "";
} 
```

**3。decrypt data(String encrypted _ String，byte[] Iv) :**

```
private String decryptData(String encrypted, final byte[] encryptionIv)
        throws UnrecoverableEntryException, NoSuchAlgorithmException, KeyStoreException,
        NoSuchPaddingException, InvalidKeyException,
        BadPaddingException, IllegalBlockSizeException, InvalidAlgorithmParameterException {
    final Cipher cipher = Cipher.getInstance(TRANSFORMATION);
    final GCMParameterSpec spec = new GCMParameterSpec(128, encryptionIv);
    cipher.init(Cipher.DECRYPT_MODE, getSecretKey_de(), spec);
    byte[] encryptedData = Base64.decode(encrypted,Base64.DEFAULT);
    return new String(cipher.doFinal(encryptedData), StandardCharsets.UTF_8);
} 
```

**说明:**解密时，我们得到存储在我们其中一个存储介质中的 Iv 和加密文本。我们使用 ANDROID_KEY_STORE 初始化密钥库，并使用 Iv 和密码的 init 和 doFinal 方法解密文本。

# 结论:

因此，通过上面的实现，密钥现在在密钥库中是安全的。为什么它可能是最好的方法是因为 KeyStore 是特定于应用程序的。它不能被检索，因此没有它就不能解密文本。许多存储用户的信用卡和其他敏感数据的应用程序使用这种加密方法来保证安全。关于完整的代码，你可以查看我的 GitHub 库。
为了解释代码，我定义了 Cryptor.java、RegistrationActivity.java 和 HomeScreen.java 这样的文件。我还使用了房间数据库(由 Google 在 I/O 2018 中推出)，该数据库提供了比 SQLLite(如果设备是根设备，则可以访问)更高级别的安全性，以存储用户名和密码来验证注册用户。

[https://github.com/varundwarkani/KeystoreEncryption-Android](https://github.com/varundwarkani/KeystoreEncryption-Android)