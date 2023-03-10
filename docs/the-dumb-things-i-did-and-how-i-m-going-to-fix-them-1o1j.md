# 我做过的蠢事以及我将如何弥补

> 原文：<https://dev.to/deciduously/the-dumb-things-i-did-and-how-i-m-going-to-fix-them-1o1j>

我在这个网站上的第一个帖子是关于我制作的处理考勤的 webapp:

[![deciduously](img/54016c09920ad1fb112de787e6ec12ae.png)](/deciduously) [## Rust + ReasonML -初学者的爱情故事

### 本·洛夫

#react #rust #reason #beginners](/deciduously/rust--reasonml---a-beginners-love-story-45a2)

当我写这篇文章的时候，已经有一段时间没有写完应用程序了。它也很有效，自从我离开那份工作以来，这个中心每天都使用它已经快一年了，我的干预很少。在很长一段时间没有接触它之后，我准备坐下来润色它，我决定“润色”我的意思是重新编写大部分后端和大部分前端。记录刮痕，定格，我打赌你在想我是怎么来的。

编写这个应用程序的原因...好吧，傻瓜，现在回想起来，并不是因为这是我当时最大的能力，而且我已经学会了。不，这是一个绝望的申请，我写这个帖子主要是为了我自己的利益。如果我们不记得未来的错误，我们注定会重复第一次或什么的。

我*完全清楚*对我在这里概述的每一个问题都有更好的了解，为了产生有用的东西，我选择做懒惰、快速的事情。我对过去的本有点生气。让这个帖子成为一个警告，未来的本。

应用程序驻留在这里。

## 背景

我在一家非营利性幼儿园的行政办公室工作。有点忙乱。这很可爱，是的，但有时是不可预测的，关于孩子，老师，家长，你知道。幼儿园的元素等等。行政管理可能会轻视我的职责，但“杂项”并不是一个很好的招聘标题。由于各种原因，我在工厂里到处跑，电话打得很频繁，在任何给定的任务上不间断地工作 15 分钟是罕见的。因此，时间是宝贵的，组织的每一个碎片都值得珍惜。

那时我已经爱好编程有一段时间了，我不喜欢做我认为是“计算机”的任务，尽管有时出于需要，一支铅笔和一张纸是做某事最简单和最便宜的方法。然而，在一种情况下，显然不是这样。

## 问题

我的日常职责之一是收集学校的出勤情况，并将其记录在电子表格中。然后，在记录任何缺席之后，我会计算“延长日花名册”并通过电子邮件发送给每个人，这样下午的人员安排就可以得到巩固。

下午 4 点，大多数孩子回家了。这就是基础合同中包含的内容。父母可以选择让他们的孩子留在那里，直到下午 6 点，而不是以不同的小时费率。由于只有一小部分家庭选择这种方式，白天的 15 个教室减少到只有 5 个。为了确保我们保持适当的师生比例，主管(和老师)需要知道在这一天的延长部分会有多少孩子，包括任何缺勤。你能安全裁减的员工越多越好！

这些都是在一个小螺旋笔记本上完成的。教室分布在大楼的四周，所以如果有人需要我，我会打电话给每个人，通过电话问老师是否有人缺席，而不是绕着圈子远离桌子。我会记下每个教室的缺席情况。然后，在我设法与所有 15 个孩子联系上之后(说起来容易做起来难)，我会将它与那些签约加班的孩子和那些临时签约在那个特定日期加班的孩子进行协调，从预期人数中增加或减少。然后，我会在一封格式化的电子邮件中输入结果，包括任何添加和删除的名称。

这占据了我一天中相当大的一部分时间。有些日子，由于糟糕的电话时机和各种各样的干扰，从第一个电话到最后一封电子邮件可能要过整整两个小时。

## 解

我构建的应用程序为用户提供了一系列按钮，每个孩子一个，按类组织。点击孩子的名字将会切换他们的出席或缺席，每个没有被安排在延长日的孩子也会得到一个按钮来添加他们。实际的延长工作日员工人数电子邮件只是谁在谁在的函数，因此它会在页面顶部保留一个可供复制/粘贴更新的版本，或作为文本文件下载:

[![app screenshot](img/62c415d85b2a03ea1fbca4f1d75806cc.png)](https://camo.githubusercontent.com/44596acec59b2793fc5f773271a6cc355249ad68/68747470733a2f2f692e696d6775722e636f6d2f7a6777706e6b512e706e67)

临时注册表格打印在粉色复印纸上，即“粉色表格”。用户可以通过简单的用户界面指定核心房间如何连接到扩展房间:

[![room picker](img/8cc2a085c6c7cb17804e3df6b075eec4.png)](https://camo.githubusercontent.com/ad48c887994c5276c32dd5962bdd7f9ac2b0865e/68747470733a2f2f692e696d6775722e636f6d2f494d5133356d632e706e67)

“下载”按钮只是将文本编码为 base64，并将其嵌入链接:

```
 let make = (~school, ~refreshClicked, ~resetClicked, _children) => {
  ...component,
  render: _self => {
    let dload =
      "data:application/octet-stream;charset=utf8;base64,"
      ++ btoa(Report.school(school));
    <div>
      // ..
      <a href=dload> <button> {ReasonReact.string("Download")} </button> </a>
      // ..
    </div>;
  },
}; 
```

Enter fullscreen mode Exit fullscreen mode

## 乱七八糟

每天执行这项任务真的磨我的齿轮。我讨厌这样做，所以我急于推出一个可以减少花费时间的解决方案。因此，为了去工作，我尽可能地走捷径。

### 该刮了，噢该刮了

第一个问题是用当天的花名册填充应用程序。不幸的是，尽管我进行了游说，但我无法直接查询该组织的数据库，必须使用预先创建的水晶报告来提取任何数据。现在，这些报告并不完全是为数据搜索而设计的，我对它们具体提取的内容没有任何控制权。它们是供人消费的，格式都很好，设计成原样打印或导出为 PDF 格式。Crystal Reports 确实提供了导出到 Excel 的选项，这已经是最好的了。你最终得到的表格很奇怪，有奇怪的行和行中奇怪的数据，就像报告想要创建的格式良好的 PDF 中的工件一样。该报告旨在打印并发给教室，作为他们的每日考勤表，但确实包含了我填充该应用程序所需的所有信息。

从这个电子表格中得到一个干净整洁的数据结构并不复杂，但是我很着急:

```
pub fn scrape_enrollment(
    day: Weekday,
    extended_config: ExtendedDayConfig,
    config: &Config,
) -> Result<School> {
    lazy_static! {
        // Define patterns to match
        static ref KID_RE: Regex =
            Regex::new(r"((@|#|&) )?(?P<last>[A-Z]+), (?P<first>[A-Z]+)").unwrap();
        static ref CLASS_RE: Regex = Regex::new(r"CLASSROOM: ([A-Z])").unwrap();
        static ref CAPACITY_RE: Regex = Regex::new(r"CLASS MAXIMUM: (\d+)").unwrap();
    }

    info!("Loading {:?} from {:?}", day, &config.roster);
    let mut school = School::new(day, extended_config);

    // Use calamine to read in the input sheet
    let mut excel: Xls<_> = open_workbook(&config.roster).unwrap();

    let mut headcount = 0;
    let mut classcount = 0;

    // Try to get "Sheet1" as `r` - it should always exist
    if let Some(Ok(r)) = excel.worksheet_range("Sheet1") {
        // Process each row
        for row in r.rows() {
            use calamine::DataType::*;
            // Column A is either a Class or a Kid
            let column_a = &row[0];
            match column_a {
                String(s) => {
                    // If it's a class, open up a new class
                    // If its a kid, push it to the open class
                    // If it's anything else, ignore it.
                    if CLASS_RE.is_match(&s) {
                        debug!("MATCH CLASS: {}", &s);
                        let caps = CLASS_RE.captures(&s).unwrap();
                        // the capacity is found in Column B
                        let capacity: u8;
                        match &row[1] {
                            String(s2) => {
                                let capacity_caps = CAPACITY_RE.captures(&s2).unwrap();
                                capacity = (&capacity_caps[1])
                                    .parse::<u8>()
                                    .chain_err(|| "Unable to parse capacity as u8")?;
                            }
                            _ => {
                                bail!("Column B of Classroom declaration contained unexpected data")
                            }
                        }

                        // Display the previous class headcount  -this needs to happen once againa the end, and not the first time
                        if !school.classrooms.is_empty() {
                            let last_class = school.classrooms[school.classrooms.len() - 1].clone();
                            let prev_headcount = last_class.kids.len();
                            debug!("Room {} headcount: {}", last_class.letter, prev_headcount);
                        }

                        // create a new Classroom and push it to the school
                        let new_class = Classroom::new(classcount, caps[1].to_string(), capacity);
                        debug!(
                            "FOUND CLASS: {} (max {})",
                            &new_class.letter, &new_class.capacity
                        );
                        school.classrooms.push(new_class);
                        classcount += 1;
                    } else if KID_RE.is_match(&s) {
                        let caps = KID_RE.captures(&s).unwrap();

                        // Reformat name from LAST, FIRST to FIRST LAST
                        let mut name = ::std::string::String::from(&caps["first"]);
                        name.push_str(" ");
                        name.push_str(&caps["last"]);

                        // init Kid datatype

                        // Add schedule day
                        let sched_idx = match day {
                            schema::Weekday::Monday => 6,
                            schema::Weekday::Tuesday => 7,
                            schema::Weekday::Wednesday => 8,
                            schema::Weekday::Thursday => 9,
                            schema::Weekday::Friday => 10,
                        };
                        let sched = &row[sched_idx];
                        let new_kid = Kid::new(headcount, name, &format!("{}", sched));
                        debug!(
                            "FOUND KID: {} - {} ({:?})",
                            new_kid.name, sched, new_kid.schedule.expected
                        );
                        // If the kid is scheduled, push the kid to the latest open class
                        if new_kid.schedule.expected == Expected::Unscheduled {
                            debug!(
                                "{} not scheduled on {:?} - omitting from roster",
                                &new_kid.name, day
                            );
                        } else {
                            let mut classroom = school.classrooms.pop().expect(
                                "Kid found before classroom declaration - input file malformed",
                            );
                            classroom.push_kid(new_kid);
                            school.classrooms.push(classroom);
                            headcount += 1;
                            debug!("Adding to response");
                        }
                    }
                }
                _ => continue,
            }
        }
    }

    // Print out the status info
    let last_class = school.classrooms[school.classrooms.len() - 1].clone();
    info!(
        "Room {} headcount: {}",
        last_class.letter,
        last_class.kids.len(),
    );
    warn!(
        "Successfully loaded {:?} enrollment from {:?} - total headcount {}, total classcount {}",
        day, config.roster, headcount, classcount
    );

    Ok(school)
} 
```

Enter fullscreen mode Exit fullscreen mode

光是看着*就让我心烦*。这个讨厌的东西是在一个下午组装完成的，而且不太容易维护。如果格式在我身上发生变化，因为它很容易在没有警告的情况下随时发生，这是*而不是*一个有趣的重构。其中的每一部分都可以分解成可以独立编辑的辅助函数。

### 发射器

这个应用程序被设计成你的标准网络聚会。有一个后端应用程序提供一个网页，并提供一个 HTTP API 来处理应用程序状态。用户通过他们的 web 浏览器导航到这个页面，并使用 UI 元素与这些 API 端点进行交互。

不过，我从来没有抽出时间来集中管理它，部分原因是过度劳累的 it 部门几乎没有时间做我的爱好实验。他们忙着做“真正的工作”或其他什么。因此，为了使用这个程序，用户必须首先自己启动网络服务器。然后在`localhost`上菜。为了更容易理解，我把一个很小的批处理文件放在一起，并把它叫做“启动器”:

```
:: Suppress command output
ECHO OFF
:: Launch server
start mifkad.exe
:: Launch client
start chrome http://127.0.0.1:8080 
```

Enter fullscreen mode Exit fullscreen mode

所以，是的。它工作，但是，谈论扬基。这也意味着每个使用它的人都在运行一个本地的、孤立的应用实例。由这个实例操作的持久数据存储确实是共享的，但是每个应用程序都独立地访问它。更愚蠢的是，该应用程序实际上设计得很好，可以避免写冲突——只要有多个连接指向同一个实例！像这样运行它完全绕过了内置的安全保证。双 oof。下面是实际调整应用程序状态的代码:

```
pub fn adjust_school(
    (path, state): (Path<(String, u32)>, State<AppState>),
) -> Box<Future<Item = Json<School>, Error = actix_web::Error>> {
    use self::Action::*;
    let action = Action::from_str(&path.0).unwrap();
    let id = path.1;

    {
        // Grab a blocking write lock inside inner scope
        let mut a = state.school.write().unwrap();

        // Perform the mutation
        match action {
            Toggle => (*a).toggle_kid(id),
            AddExt => (*a).addext_kid(id),
            Collect => (*a).collect_room(id),
            Reset => {
                reset_db(&state.config).unwrap();
                (*a) = init_db(&state.config).unwrap();
            }
        }
        // blocking lock is dropped here
    }

    // grab a new non-blocking reader
    let a = state.school.read().unwrap();

    // Sync the on-disk DB and return the json
    write_db(&*a).unwrap();
    result(Ok(Json((*a).clone()))).responder()
} 
```

Enter fullscreen mode Exit fullscreen mode

所有这些都是为了安全地处理并发写入，并且这个应用程序只由一个到本地实例的连接使用。干得好，本。

### JSON

另一个愚蠢的捷径是数据持久性。因为多个用户将与来自不同工作站(运行应用程序的不同实例)的相同数据进行交互，所以我决定将每个更改写入共享的网络驱动器。当应用程序启动时，它首先检查是否有已经创建的应用程序状态，并从中填充，而不是重新读取花名册电子表格。这很可能已经使用关系数据库实现了，但是没有。我只是将应用程序状态序列化到 JSON，并将其存储为带时间戳的文本文件，例如“20190621.json”。该应用程序只是查找日期，看看是否有相应的文件。

它有点工作，但这些文件不是很有帮助。创建它们的文件夹会莫名其妙地增加，一天过后，这些文件夹通常就没用了。我可以清理它们，但它们也是孩子们在他们的 AR 线之外逗留一整天的唯一记录。有额外的备份是有帮助的，但是挖掘它意味着筛选一堆没有空白的 JSON。这一点都不好玩，而且对于下面那些不是我的人来说也不会发生。

它也很脆弱——如果当天的文件消失或被修改，它就无法阅读，你必须重新开始。一个更加高度管理的数据接口可以防止这样的问题。

## 复兴

我现在回到这个项目，因为我想尝试将其推广到这个网络中的其他位置。我想我可能会对当前的化身进行概括，但是再次挖掘代码让我觉得我需要洗个澡，我不能就这样离开。

此外，自从我写这篇文章以来，利基和新的后端框架以及利基和新的前端框架都进行了重大更新。通常我不会太担心这个，但是后端框架，`actix_web`在 0.7 的基础上稳定到了 1.0。这很好，考虑到我希望最终(大部分情况下)远离这一切，让它无限期地为他们工作。ReasonReact 还稳定了一个更整洁的组件语法，我觉得这使代码更具可读性——对于我希望最多几个月接触一次的项目来说是完美的。

前端我不希望是一个复杂的迁移。实际上，我或多或少对它感到满意，尽管我认为还有一些清理的机会。例如，该应用程序有用性的全部关键是查看 15 个核心教室并提取 5 个缩减教室的能力。这是通过一个相当不透明的文件夹来处理的，原因:

```
let add_extended_room = (school, classroom) => {
  /* This is our folding fn from get_extended_rooms below.
     It should take a room and a school and either add the new room
     or if a room already exists with the same letter, just add those kids */
  let target = ref(school.classrooms);

  if (Array.length(target^) == 0) {
    target := Array.append(target^, Array.make(1, classroom));
  } else {
    let already_included =
      Array.map((oldr: classroom) => oldr.letter, school.classrooms);
    let found = ref(false);
    let idx = ref(0) /* This will only be read later if found is toggled to true*/;
    Array.iteri(
      (i, l) =>
        if (classroom.letter == l) {
          found := true;
          idx := i;
        },
      already_included,
    );
    if (found^) {
      /* We've already seen this letter - mash the new kid list into the matching existing kid list */
      let old_classroom = school.classrooms[idx^];
      let new_classroom = {
        ...old_classroom,
        capacity:
          get_extended_capacity(classroom.letter, school.extended_day_config)
          |> int_of_string,
        kids: ref(Array.append(old_classroom.kids^, classroom.kids^)),
      };
      target^[idx^] = new_classroom;
    } else {
      /* This is a new extended day room - add it as-is, grabbing the extended day capacity */
      target :=
        Array.append(
          target^,
          Array.make(
            1,
            {
              ...classroom,
              capacity:
                get_extended_capacity(
                  classroom.letter,
                  school.extended_day_config,
                )
                |> int_of_string,
            },
          ),
        );
    };
  };

  {...school, classrooms: target^};
};

let get_extended_rooms = school => {
  /* Returns a `school` of the extended kids */
  let s = get_extended_kids(school);
  Array.fold_left(
    add_extended_room,
    {...school, classrooms: [||]},
    s.classrooms,
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

老实说，我对它的可靠性印象深刻。这只是尖叫“我只认为我知道函数式编程是如何工作的”。我真的不记得我是如何得出这个特殊的解决方案的，我有点害怕去碰它。那可不行。

后端是更大的优先权。新的稳定版本`actix_web`是我现在使用的一个重大的突破性改变，所以我很可能至少会从头开始重写我的处理程序。当我这么做的时候，我将拆除 JSON 序列化设备，支持 SQLite 存储。这仍然是可移植的，因为它可以以一种非常独立的方式存在于操作系统的一个文件中，但是很难被意外破坏。它还有一个额外的好处，就是可以查询——如果你想知道艾伯特·戈尔在 1 月 2 日到 2 月 12 日之间是什么时候报名加班的，你可以用 SQL 查询。在确保基础应用程序按预期工作后，我想添加一个用户界面来展示这些功能。

另一个可以建立在更坚实基础上的新功能是允许教室自己进入。如果这个应用程序是按预期使用的，也就是说，它是一个始终运行的过程，用户可以远程连接到它，而不是在本地启动它，那么教师没有理由不能直接在它上面考勤。这样他们就不需要分别收集，然后再用电话联系。如果他们可以从教室里的 iPad 上导航到一个专为他们的学生设计的网页，仅仅是为了标记谁进了谁出了，那么网站的出席率基本上就会自动上升。不过，这整个概念要求我转向集中式应用模型。

我也想充实测试的故事。对于后端的各个部分有一些测试，但是我不认为这是一个测试良好的应用程序。我对该产品的日常维护自动化做得越多，就越好。

最后，很明显 UI 是由一个对任何 CSS 都极度恐惧的人构建的。不需要这么丑。我也许应该抓住机会练习这些技能。

听起来好像过了这么长时间，我还有很多工作要做。当我第一次有尝试的想法时，我一点也不知道。我想我最好走了！