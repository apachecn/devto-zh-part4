# 生锈故障

> 原文：<https://dev.to/x1957/rust-failure-3a0m>

Everyone suggested that error handling use the library [T0】 failure 【T1], and looked at it (:

Paste the code
first

```
use failure::{Error, Fail};
use reqwest;

#[derive(Fail, Debug)]
enum MyError {
    #[fail(display = "filed {} not exist", _0)]
    FileNotExist(String),
    // #[fail(display = "http error: {}", _0)]
    // HttpError(#[fail(cause)] reqwest::Error),
}

fn http_get(url: Option<String>) -> std::result::Result<(), Error> {
    if url == None {
        // just test
        return Err(MyError::FileNotExist("url".to_string()).into());
    }
    let client = reqwest::Client::new();
    let result = client.get(&url.unwrap()).send()?.text()?;
    println!("{}", result);
    Ok(())
}

fn main() {
    http_get(Some("https://www.baidu.com".to_string())).unwrap();
} 
```

We can use Failure::Error to replace std::error::Error, and our own error will be implemented to fail this trait. Here, we have used his procedure macro and let it be implemented for us by default. We cargo expand Cargo to see

```
#[allow(non_upper_case_globals)]
#[doc(hidden)]
const _DERIVE_failure_Fail_FOR_MyError: () = {
    impl ::failure::Fail for MyError {
        fn name(&self) -> Option<&str> {
            Some("fail::MyError")
        }
        #[allow(unreachable_code)]
        fn cause(&self) -> ::failure::_core::option::Option<&dyn ::failure::Fail> {
            match *self {
                MyError::FileNotExist(ref __binding_0) => return None,
            }
            None
        }
        #[allow(unreachable_code)]
        fn backtrace(&self) -> ::failure::_core::option::Option<&::failure::Backtrace> {
            match *self {
                MyError::FileNotExist(ref __binding_0) => return None,
            }
            None
        }
    }
}; 
```

Actually, the biggest problem I encountered was not this one, but that when I

`return Err(MyError::FileNotExist("url".to_string()));`

, I had to report an Error, saying that I wanted a Failure::failure::Error, and what I returned was this error that I defined, but didn't I just say that I had implemented the fail interface? I didn't write the document either, but I found it in an issue again, saying that it was either into () or?

So it has to be written as

```
return Err(MyError::FileNotExist("url".to_string()).into()); 
```

Or

```
Err(MyError::FileNotExist("url".to_string()))?; 
```