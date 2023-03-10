# 基于属性的测试:从 Erlang/Elixir 到 Clojure

> 原文：<https://dev.to/shaolang/property-based-testing-from-erlang-elixir-to-clojure-57nm>

阅读[PropEr、Erlang 和 Elixir](https://pragprog.com/book/fhproper/property-based-testing-with-proper-erlang-and-elixir) 的基于属性的测试并跟随示例帮助我学习了这种令人兴奋的测试方法；但这也让我感到疑惑:我真的通过跟随而吸收并内化了*吗？*

因此，我联系了 [Fred](https://twitter.com/mononcqc) ，得到了他的同意，并开始用 [test.check](https://github.com/clojure/test.check) 将代码从 Erlang/Elixir 翻译成 Clojure。所有[到目前为止]完成的代码都托管在 https://github.com/shaolang/pbtic 的我正在使用 [test.check](https://github.com/clojure/test.check) 作为 Clojure 中基于属性的测试工具。

## 生日问候形

该书将 kata 分解为 4 个部分: [CSV 解析](#csv-parsing)、[记录过滤](#records-filtering)、[员工模块](#employee-module)(桥接 CSV 解析和记录过滤)、以及[邮件模板](#email-templating)。

### CSV 解析

```
(ns  pbtic.birthday.csv-test  (:require  [clojure.test  :refer  [deftest  is]]  [clojure.test.check.clojure-test  :refer  [defspec]]  [clojure.test.check.generators  :as  gen]  [clojure.test.check.properties  :refer  [for-all]]  [pbtic.birthday.csv  :as  csv]))  ;;;;;;;  ;; defs  (def  ^:private  text-data  (str  "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789"  ":;<=>?@ !#$%&'()*+-./[\\]^_`{|}~"))  ;;;;;;;;;;;;;  ;; generators  (defn-  text  [cs]  (gen/let  [xs  (gen/list  (gen/elements  cs))]  (apply  str  xs)))  (def  unquoted-text  (text  text-data))  (def  quotable-text  (text  (str  text-data  "\r\n\",")))  (def  field  (gen/one-of  [unquoted-text,  quotable-text]))  (def  header  (partial  gen/vector  field))  (def  record  (partial  gen/vector  field))  (defn  entry  [size  ks]  (gen/let  [vs  (record  size)]  (zipmap  ks  vs)))  (def  csv-source  (gen/let  [size  gen/pos-int  ks  (header  (inc  size))]  (gen/list  (entry  (inc  size)  ks)))) 
```

与[中的`?LET` / `let`不同，适当的](https://github.com/proper-testing/proper) / [PropCheck](https://github.com/alfert/propcheck) ，
[`clojure.test.check.generators/let`](http://clojure.github.io/test.check/clojure.test.check.generators.html#var-let) 可以有多个绑定，其中绑定之后可以引用前一个的值，如第 39 和 40 行所示。

`csv-source`生成一个映射列表，其中每个映射是来自 CSV 源的一个记录，每个映射的键是头记录。虽然 Clojure maps 习惯使用关键字作为映射的键，但该书建议将 CSV 解析集中在编码/解码上，不要引入任何业务需求来保持模块/名称空间的可维护性。

```
;;;;;;;;;;;;;  ;; properties  (defspec  roundtrip-encoding-decoding  (for-all  [maps  csv-source]  (=  maps  (csv/decode  (csv/encode  maps)))))  ;;;;;;;;  ;; tests  (deftest  one-column-csv-files-are-inherently-ambiguous  (is  (=  "\r\n\r\n\r\n"  (csv/encode  [{""  ""},  {""  ""}])))  (is  (=  [{""  ""}]  (csv/decode  "\r\n\r\n"))))  (deftest  one-record-per-line  (is  (=  [{"aaa"  "zzz",  "bbb"  "yyy",  "ccc"  "xxx"}]  (csv/decode  "aaa,bbb,ccc\r\nzzz,yyy,xxx\r\n"))))  (deftest  optional-trailing-crlf  (is  (=  [{"aaa"  "zzz",  "bbb"  "yyy",  "ccc"  "xxx"}]  (csv/decode  "aaa,bbb,ccc\r\nzzz,yyy,xxx"))))  (deftest  double-quotes  (is  (=  [{"aaa"  "zzz",  "bbb"  "yyy",  "ccc"  "xxx"}]  (csv/decode  "\"aaa\",\"bbb\",\"ccc\"\r\n\"zzz\",\"yyy\",\"xxx\""))))  (deftest  escape-crlf  (is  (=  [{"aaa"  "zzz",  "b\r\nbb"  "yyy",  "ccc"  "xxx"}]  (csv/decode  "\"aaa\",\"b\r\nbb\",\"ccc\"\r\nzzz,yyy,xxx"))))  (deftest  double-quotes-escaping  (is  (=  [{"aaa"  "",  "b\"bb"  "",  "ccc"  ""}]  (csv/decode  "\"aaa\",\"b\"\"bb\",\"ccc\"\r\n,,"))))  (deftest  dupe-keys-unsupported  (let  [csv  (str  "field_name,field_name,field_name\r\n"  "aaa,bbb,ccc\r\n"  "zzz,yyy,xxx\r\n")  [m1  m2]  (csv/decode  csv)]  (is  (=  ["field_name"]  (keys  m1)))  (is  (=  ["field_name"]  (keys  m2))))) 
```

属性和测试直接反映了书中的原始代码。但是实现呢？哈哈，我走了捷径，用 [data.csv](https://github.com/clojure/data.csv) 代替，因为我的主要目的是学习如何编写属性测试，而不是实现 csv 解析器。

```
(ns  pbtic.birthday.csv  (:require  [clojure.data.csv  :as  csv]  [clojure.string  :as  str]))  (defn  encode  [ms]  (let  [ks  (->  ms  first  keys)  vs  (map  vals  ms)  out  (java.io.StringWriter.)]  (csv/write-csv  out  (conj  vs  ks)  :newline  :cr+lf)  (.toString  out)))  (defn  decode  [s]  (let  [[header  &  body]  (csv/read-csv  (java.io.StringReader.  s))]  (map  (partial  zipmap  header)  body))) 
```

## 记录过滤

记录过滤模块/名称空间过滤生日在给定日期的雇员。虽然属性是新发现的力量，但是在这个实例中使用它们可能并不合适，因为属性测试的核心是概率性的。“传统的”单元测试可以更好地探索这个问题空间。即便如此，Fred 建议运行一个详尽的搜索来覆盖所有可能的案例，在基于属性的测试中使用生成器作为“生成”案例的灵感。

```
(ns  pbtic.birthday.bday-filter-test  (:require  [pbtic.birthday.bday-filter  :as  filter  :refer  [month-day]]  [clojure.set  :as  set]  [clojure.test  :refer  [deftest  is]])  (:import  [java.time  DateTimeException  LocalDate]))  ;;;;;;;;;;  ;; helpers  (defn  find-birthdays-for-year  [people  yeardata]  (when  (seq  yeardata)  (let  [[day  &  year]  yeardata  found  (filter/birthday  people  day)]  ;; <- function being tested  (assoc  (find-birthdays-for-year  people  year)  day  found))))  (defn  generate-year-data  [start]  (let  [start-date  (LocalDate/of  start  1  1)  end-date  (LocalDate/of  (inc  start)  1  1)]  (into  []  (..  start-date  (datesUntil  end-date)  toArray))))  (defn  generate-years-data  [start  end]  (mapv  generate-year-data  (range  start  (inc  end))))  (defn  rand-name  []  (apply  str  (repeatedly  30  #(rand-nth  "abcdefghijklmnopqrstuvwxyz"))))  (defn  people-for-date  [date]  (try  (let  [[month  day]  (month-day  date)  rand-year  (+  1900  (rand-int  100))]  {:name  (rand-name)  :date-of-birth  (LocalDate/of  rand-year  month  day)})  (catch  Exception  _  (people-for-date  date))))  (defn  people-for-year  [year]  (map  people-for-date  year))  (defn  generate-people-for-year  [n]  (let  [year-seed  (generate-year-data  2016)]  ;; leap year so all days are covered  (mapcat  (fn  [_]  (people-for-year  year-seed))  (range  n))))  ;;;;;;;;;;;;;  ;; assertions  (defn  every-birthday-once  [people  birthdays]  (let  [found  (mapcat  second  birthdays)  not-found  (set/difference  (set  people)  (set  found))]  (is  (empty?  not-found))  (is  (zero?  (-  (count  found)  (count  (set  found)))))))  (defn  on-right-date  [people  birthdays]  (doseq  [[date  found]  birthdays  {:keys  [date-of-birth]}  found]  (let  [[dob-month  dob-day]  (month-day  date-of-birth)]  (try  (LocalDate/of  (.getYear  date)  dob-month  dob-day)  (is  (=  (month-day  date)  (month-day  date-of-birth)))  (catch  DateTimeException  _  true)))))  ;;;;;;;  ;; test  (deftest  property-style-filtering  (let  [years  (generate-years-data  2018  2038)  people  (generate-people-for-year  3)]  (doseq  [yeardata  years]  (let  [birthdays  (find-birthdays-for-year  people  yeardata)]  (every-birthday-once  people  birthdays)  (on-right-date  people  birthdays))))) 
```

这个和原来的区别是:

*   `generate-year-data`(第 17-20 行)明显比原来的要短，因为它使用了 [`java.time.LocalDate#datesUntil`](https://docs.oracle.com/javase/9/docs/api/java/time/LocalDate.html#datesUntil-java.time.LocalDate-) 方法来生成日期。书中的 Elixir 版本很长，可能是因为 Fred 想让它与 Erlang 版本保持一致；如果他使用仙丹的 Date.range/2 功能(Erlang 中没有类似的功能😭)，代码会更短，如下所示:

```
defp generate_year_data(year) do
  {:ok, start_date} = Date.new(year, 1, 1)
  {:ok, end_date} = Date.new(year, 12, 31)

  Date.range(start_date, end_date)
  |> Enum.into([])
end 
```

*   `rand-name`(第 27-28 行)是 Erlang 的`make-ref/0`的简单替身。
*   `every-birthday-once`(第 51-55 行)使用集合数据结构确定未找到的人员集合。
*   `on-right-date`(第 58-66 行)捕捉无效的日期，并在 catch 子句中返回`true`以表示“跳过”，因为 Clojure 不像 Erlang/Elixir 那样频繁地使用模式匹配。

`pbtic.birthday.bday-filter`的实现如下:

```
(ns  pbtic.birthday.bday-filter)  (def  month-day  (juxt  #(.getMonthValue  %)  #(.getDayOfMonth  %)))  (defn  birthday-no-leap-year-handling  [people  date]  (let  [md  (month-day  date)]  (filter  #(=  (month-day  (:date-of-birth  %))  md)  people)))  (defn  filter-dob  [people  month  day]  (filter  #(=  (month-day  (:date-of-birth  %))  [month  day])  people))  (defn  birthday  [people  date]  (let  [[month  day]  (month-day  date)]  (if  (and  (=  [month  day]  [2  28])  (not  (.isLeapYear  date)))  (concat  (filter-dob  people  2  28)  (filter-dob  people  2  29))  (filter-dob  people  month  day)))) 
```

`birthday-no-leap-year-handling`(第 7-9 行)显示了处理非闰年的 2 月 29 日生日之前的代码(公司不应该每四年才祝他们一次生日快乐，不是吗？).

### 员工模块

命名空间来统治他们所有人💍；它将`pbtic.birthday.csv`和`pbtic.birthday.bday-filter`联系在一起。这个名称空间中的函数实现了其他名称空间可能还没有的业务需求，例如`pbtic.birthday.csv`中的 CSV 解析器。

```
(ns  pbtic.birthday.employee-test  (:require  [clojure.string  :as  str]  [clojure.test.check.clojure-test  :refer  [defspec]]  [clojure.test.check.generators  :as  gen]  [clojure.test.check.properties  :refer  [for-all]]  [pbtic.birthday.csv-test  :as  csv-test]  [pbtic.birthday.employee  :as  employee])  (:import  [java.time  LocalDate]))  ;;;;;;;  ;; defs  (def  start-date  (LocalDate/of  1900  1  1))  (def  max-days  (..  start-date  (until  (LocalDate/of  2021  1  1))  getDays))  ;;;;;;;;;;;;;  ;; generators  (def  text-date  (gen/let  [days-to-add  (gen/choose  0  max-days)]  (let  [date  (.plusDays  start-date  days-to-add)]  (format  " %4d/%02d/%02d"  (.getYear  date)  (.getMonthValue  date)  (.getDayOfMonth  date)))))  (def  whitespaced-text  (gen/let  [txt  csv-test/field]  (str  " "  txt)))  (def  raw-employee-map  (gen/let  [val-list  (gen/tuple  csv-test/field  whitespaced-text  text-date  whitespaced-text)]  (zipmap  ["last_name",  " first_name",  " date_of_birth",  " email"]  val-list)))  ;;;;;;;;;;;;;  ;; properties  (defspec  check-that-leading-space-is-fixed  (for-all  [m  raw-employee-map]  (let  [emp  (employee/adapt-csv-result  m)]  (every?  #(not  (str/starts-with?  (name  %)  " "))  (concat  (keys  emp)  (filter  string?  (vals  emp)))))))  (defspec  check-that-date-is-formatted-right  (for-all  [m  raw-employee-map]  (let  [m  (employee/adapt-csv-result  m)]  (=  (type  (get  m  :date-of-birth))  LocalDate)))) 
```

根据 Fred 的建议，在创建自定义生成器时，将一个限制重新转换为一个转换(参考本书第 4 章基本自定义生成器下的强加限制小节)，`text-date`(第 22-28 行)抛弃`gen/such-that`并进行一个将天数添加到已知开始日期(1900-01-01)的转换。虽然这不是一个纯粹的代码移植，但它表明重新想象手头的问题并不那么困难。

```
(ns  pbtic.birthday.employee  (:require  [clojure.string  :as  str]  [pbtic.birthday.bday-filter  :as  bday-filter]  [pbtic.birthday.csv  :as  csv])  (:import  [java.time  LocalDate]  [java.time.format  DateTimeFormatter]))  ;;;;;;;  ;; defs  (def  ^:private  datetime-formatter  (DateTimeFormatter/ofPattern  "yyyy/MM/dd"))  ;;;;;;;;;;;;;  ;; public API  (defn  adapt-csv-result  [m]  (let  [ks  (sequence  (comp  (map  str/triml)  (map  #(str/replace  %  #"_"  "-"))  (map  keyword))  (keys  m))  m  (zipmap  ks  (map  str/triml  (vals  m)))  dob  (:date-of-birth  m)]  (assoc  m  :date-of-birth  (LocalDate/parse  dob  datetime-formatter))))  (defn  from-csv  [s]  (map  adapt-csv-result  (csv/decode  s)))  ;; parameter order is different from the book's  (defn  filter-birthday  [date  employees]  (bday-filter/birthday  employees  date)) 
```

名称空间没有实现“访问器”,因为第 19 行的关键字化是因为 Clojure 的习语使用关键字从映射中检索值(第 22 行显示了一个这样的例子)。另一个变化是交换了`filter-birthday`中的参数(第 31-32 行)；这使得该功能更容易与 [`->>`](https://clojure.github.io/clojure/clojure.core-api.html#clojure.core/-%3E%3E) 线程宏一起使用；Elixir [`|>`](https://hexdocs.pm/elixir/Kernel.html#%7C%3E/2) 期望集合是第一个参数，但是 Clojure `->>`期望它是最后一个)。这样的省略使得 Clojure 的实现/端口更短。

### 模板化

`pbtic.birthday.mail-tpl/body`功能创建插入电子邮件的消息。用传统的单元测试方法来测试是相对容易的...

```
(ns  pbtic.birthday.mail-tpl-test  (:require  [clojure.string  :as  str]  [clojure.test.check.clojure-test  :refer  [defspec]]  [clojure.test.check.generators  :as  gen]  [clojure.test.check.properties  :refer  [for-all]]  [pbtic.birthday.csv-test  :as  csv-test]  [pbtic.birthday.mail-tpl  :as  mail-tpl])  (:import  [java.time  LocalDate]))  ;;;;;;;;;;;;;  ;; generators  (def  date  (gen/let  [days-to-add  gen/nat]  (.plusDays  (LocalDate/of  1900  1  1)  days-to-add)))  (def  employee-map  (gen/let  [vs  (gen/tuple  (gen/not-empty  csv-test/field)  (gen/not-empty  csv-test/field)  date  (gen/not-empty  csv-test/field))]  (zipmap  [:last-name  :first-name  :date-of-birth  :email]  vs)))  ;;;;;;;;;;;;;  ;; properties  (defspec  email-template-has-first-name  (for-all  [employee  employee-map]  (str/includes?  (mail-tpl/body  employee)  (:first-name  employee)))) 
```

实现很简单:

```
(ns  pbtic.birthday.mail-tpl)  (defn  body  [{:keys  [first-name]}]  (format  "Happy birthday, dear %s!"  first-name))  (defn  full  [{:keys  [email]  :as  employee}]  [email,  "Happy birthday!",  (body  employee)]) 
```

### 把所有东西都装在一起

函数将所有这些捆绑在一起(没有编写任何集成测试)。

```
(ns  pbtic.birthday  (:require  [pbtic.birthday.csv  :as  csv]  [pbtic.birthday.employee  :as  employee]  [pbtic.birthday.mail-tpl  :as  mail-tpl])  (:import  [java.time  LocalDate]))  ;;;;;;;;;  ;; helper  (defn-  send-email  [[to,  _topic,  _body]]  (println  "sent birthday email to"  to))  ;;;;;;;;;;;;;  ;; public api  (defn  run  [path  &  {:keys  [curr-date]  :or  {curr-date  (LocalDate/now)}}]  (doseq  [employee  (->>  (slurp  path)  employee/from-csv  (employee/filter-birthday  curr-date))]  (send-email  (mail-tpl/full  employee)))) 
```

为了测试它，传递雇员记录 CSV 的文件名和可选的“当前日期”(默认为今天)。例如，在 Clojure REPL 运行以下命令:

```
user=> (require '[pbtic.birthday :as bday])
user=> (import '[java.time LocalDate])
user=> (bday/run "resources/birthday/employees.csv" :curr-date (LocalDate/of 2019 8 10))
sent birthday email to john.doe@foobar.com
nil 
```

## 总结

将代码从 Erlang/Elixir 移植到 Clojure 是一件非常激动人心的事情。在下一篇文章中，我将介绍从第 6 章“属性驱动的开发”移植过来的代码

*本帖[基于属性的测试:从 Erlang/Elixir 到 Clojure](https://shaolang.github.io/posts/2019-08-10-property-based-testing-from-elixir-to-clojure/) 最先出现在[上一点一点的](https://shaolang.github.io)T5】*