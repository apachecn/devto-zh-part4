# Rust ProcMacro 101

> 原文：<https://dev.to/takaakifuruse/rust-macro-101-52dg>

# [T1】简介](#intro)

你知道这行得通....

```
fn greetings(var: String) {
    println!("{}", var)
}

greetings("Valar Morghulis!".to_string()); 
```

你也知道这是行不通的....如果成功的话。这是一种魔力。

```
fn greetings_generator(var1: String, var2: String) {
    fn var2(var: String){
     var1("{}", var)
   }
}

greeetings_generator(println!, greetings1)
greetings1("Valar Morghulis!".to_string!) 
```

有一种方法可以让它工作。
使用 Rust 宏。
宏可以让你像处理函数一样写 Rust 代码。

```
macro_rules! greetings_generator {
    ($var1:ident, $var2:ident) => {
        fn $var2(var: String) {
            $var1!("{}", var)
        }
    };
}

greetings_generator!(print, greetings2);
greetings2("Valar Morghulis from greeting2\n".to_string());

greetings_generator!(println, greetings3);
greetings3("Valar Morghulis from greeting3".to_string()); 
```

[锈操场上的代码](https://play.rust-lang.org/?version=stable&mode=debug&edition=2018&gist=ca8bd2c71eed20d047132c4349b98602)

# 正常接近

现在，我们随便点我们的七神吧。

```
#[derive(Debug)]
pub enum Gods {
    Father,
    Mother,
    Maiden,
    Crone,
    Warrior,
    Smith,
    Stranger,
}

fn my_gods_order(god:&Gods) -> u32{
    match god {
        Gods::Father => 1,
        Gods::Mother => 2,
        Gods::Maiden => 3,
        Gods::Crone => 4,
        Gods::Warrior => 5,
        Gods::Smith => 6,
        Gods::Stranger => 7,
    }
}

fn your_gods_order(god:&Gods) -> u32{
    match god {
        Gods::Father => 7,
        Gods::Mother => 6,
        Gods::Maiden => 5,
        Gods::Crone => 4,
        Gods::Warrior => 3,
        Gods::Smith => 2,
        Gods::Stranger => 1,
    }
}

fn main() {
    let mut gods = vec![Gods::Stranger, Gods::Father, Gods::Mother];
    gods.sort_by(|a, b| my_gods_order(a).partial_cmp(&my_gods_order(b)).unwrap());
    println!("{:?}", gods);

    let mut gods = vec![Gods::Smith, Gods::Crone, Gods::Stranger];
    gods.sort_by(|a, b| your_gods_order(a).partial_cmp(&your_gods_order(b)).unwrap());
    println!("{:?}", gods);

} 
```

# 使用宏

你的订单越多，你会得到越来越多的匹配状态...
这很有效，但是真的很烦人...

你做什么....？

宏指令...

那更好...

```
macro_rules! gods_order1 {
    ($a:ident, $b:ident,  $c:ident) => {
        fn my_gods_order1(var: &Gods) -> u32 {
            match var {
                Gods::$a => 1,
                Gods::$b => 2,
                Gods::$c => 3,
                _ => 100,
            }
        }
    };
}

fn main() {
    gods_order1!(my_gods_order1, Stranger, Father, Mother);
    let mut gods = vec![Gods::Mother, Gods::Father, Gods::Stranger];
    gods.sort_by(|a, b| my_gods_order1(a).partial_cmp(&my_gods_order1(&b)).unwrap());
    println!("{:?}", gods);

    gods_order1!(your_gods_order1, Mother, Father, Stranger);
    let mut gods = vec![Gods::Mother, Gods::Father, Gods::Stranger];
    gods.sort_by(|a, b| your_gods_order1(a).partial_cmp(&your_gods_order1(&b)).unwrap());
    println!("{:?}", gods);

} 
```

甚至更好...

```
macro_rules! gods_order2 {
    ($func:ident, [$(($elm:tt, $i:expr)),*]) => {
        fn $func(var: &Gods) -> u32 {
            match var {
                $(Gods::$elm => $i,)*
                _ => 0,
            }
        }
    };
}

fn main() {

    gods_order2!(my_gods_order2, [(Stranger, 3), (Father, 2), (Mother, 1)]);
    let mut gods = vec![Gods::Mother, Gods::Father, Gods::Stranger];
    gods.sort_by(|a, b| my_gods_order2(a).partial_cmp(&my_gods_order2(&b)).unwrap());
    println!("{:?}", gods);

    gods_order2!(your_gods_order2, [(Mother, 1), (Father, 2), (Stranger, 3)]);
    let mut gods = vec![Gods::Mother, Gods::Father, Gods::Stranger];
    gods.sort_by(|a, b| your_gods_order2(a).partial_cmp(&your_gods_order2(&b)).unwrap());
    println!("{:?}", gods);

} 
```

[锈操场上的代码](https://play.rust-lang.org/?version=stable&mode=debug&edition=2018&gist=8e299ba94c2030feb24870c21869e18b)

# 使用 Proc 宏

现在，如果我们可以从字符串中定义顺序。

假设顺序在 config.toml 中预定义，宏解析顺序并返回定义的顺序。

先做`cargo new enum_builder`。

在 src/config.toml 中

```
order="Stranger,Smith,Warrior,Crone,Maiden,Mother,Father" 
```

由于我们使用 proc 宏，我们在 cargo.toml 中添加了以下内容

```
[lib]
proc-macro = true

[dependencies]
syn = { version =  "0.15.42", features = ["extra-traits", "full"] }
proc-macro2 = "0.4.30"
quote = "0.6.12"
toml = "0.4.2" 
```

然后，src/main.rs 将成为...

```
pub mod enum_builder {
    use enum_from_string::order;

    #[derive(Debug, order)]
    pub enum Gods {
        Father,
        Mother,
        Maiden,
        Crone,
        Warrior,
        Smith,
        Stranger,
    }
}

fn main() {
    assert_eq!(enum_builder::Gods::Father.order(), 6);
    assert_eq!(enum_builder::Gods::Stranger.order(), 0);
} 
```

最后，lib.rs 是..

```
extern crate proc_macro;
extern crate toml;

use proc_macro::TokenStream;
use quote::quote;
use std::fs;
use std::io::{BufReader, Read};
use toml::Value;

use syn::{parse_macro_input, DeriveInput};

fn file_open(path: String) -> Result<String, String> {
    let mut file_content = String::new();

    let mut fr = fs::File::open(path)
        .map(|f| BufReader::new(f))
        .map_err(|e| e.to_string())?;

    fr.read_to_string(&mut file_content)
        .map_err(|e| e.to_string())?;

    Ok(file_content)
}

#[proc_macro_derive(order)]
pub fn order_builder(item: TokenStream) -> TokenStream {
    let ast = parse_macro_input!(item as DeriveInput);
    let name = &ast.ident;
    let enum_variants = if let syn::Data::Enum(syn::DataEnum { variants, .. }) = ast.data {
        variants
    } else {
        unimplemented!();
    };
    let enum_len = &enum_variants.len();

    let order_config: toml::Value = file_open("src/config.toml".to_string())
        .unwrap()
        .parse::<Value>()
        .unwrap();

    let orders: Vec<&str> = order_config["order"].as_str().unwrap().split(",").collect();

    assert_eq!(&orders.len(), enum_len);

    let enum_fields = orders.iter().enumerate().map(|(i, elm)| {
        let elm_ident = syn::Ident::new(&elm, name.span());
        let arm = quote! {
            #name::#elm_ident => #i as i32,
        };
        arm
    });

    let order_func = quote! {
        impl #name {
            pub fn order(&self) -> i32{
                match self {
                    #(#enum_fields)*
                }
            }
        }
    };
    order_func.into()
} 
```

# 参

对于过程宏基本...
[https://github.com/azriel91/proc_macro_rules](https://github.com/azriel91/proc_macro_rules)

对于高级 proc 宏学习者
[https://github.com/dtolnay/proc-macro-workshop](https://github.com/dtolnay/proc-macro-workshop)

proc-macro-workshop 有许多 fork repos，您可以找到好的解决方案，例如...
[https://github.com/ELD/proc-macro-workshop](https://github.com/ELD/proc-macro-workshop)
[https://github.com/jonhoo/proc-macro-workshop](https://github.com/jonhoo/proc-macro-workshop)
[https://github.com/gobanos/proc-macro-workshop](https://github.com/gobanos/proc-macro-workshop)