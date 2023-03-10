# 鸽子组织者实验室

> 原文：<https://dev.to/harlessmark/pigeon-organizer-lab-46n6>

## 问题

我得到了一个信息散列，我需要将它们重新排列成一个以不同方式排序的新散列。

从这里开始:

```
pigeon_data = {
  :color => {
    :purple => ["Theo", "Peter Jr.", "Lucky"],
    :grey => ["Theo", "Peter Jr.", "Ms. K"],
    :white => ["Queenie", "Andrew", "Ms. K", "Alex"],
    :brown => ["Queenie", "Alex"]
  },
  :gender => {
    :male => ["Alex", "Theo", "Peter Jr.", "Andrew", "Lucky"],
    :female => ["Queenie", "Ms. K"]
  },
  :lives => {
    "Subway" => ["Theo", "Queenie"],
    "Central Park" => ["Alex", "Ms. K", "Lucky"],
    "Library" => ["Peter Jr."],
    "City Hall" => ["Andrew"]
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

对此:

```
pigeon_list = {
  "Theo" => {
    :color => ["purple", "grey"],
    :gender => ["male"],
    :lives => ["Subway"]
  },
  "Peter Jr." => {
    :color => ["purple", "grey"],
    :gender => ["male"],
    :lives => ["Library"]
  },
  "Lucky" => {
    :color => ["purple"],
    :gender => ["male"],
    :lives => ["Central Park"]
  },
  "Ms. K" => {
    :color => ["grey", "white"],
    :gender => ["female"],
    :lives => ["Central Park"]
  },
  "Queenie" => {
    :color => ["white", "brown"],
    :gender => ["female"],
    :lives => ["Subway"]
  },
  "Andrew" => {
    :color => ["white"],
    :gender => ["male"],
    :lives => ["City Hall"]
  },
  "Alex" => {
    :color => ["white", "brown"],
    :gender => ["male"],
    :lives => ["Central Park"]
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 我学到了什么

虽然这个实验很有挑战性，但迄今为止我从这个实验中学到了最多的东西。

1.  如何使用`.each`遍历一个散列，并给键和值分配不同的名称。

2.  我发现创建一个新的散列需要检查它是否存在，我对此很感兴趣。我认为我能用一行做的事，我必须用三行来做。后来我发现我可以用`pigeon_list[:name] || = {}`在一行中完成，但是我还不确定如何使用它。

3.  我重新学习了如何用`.to_s`把一些东西变成一个字符串

## 最终迭代

```
def nyc_pigeon_organizer(data)
  pigeon_list = {}
  data.each do |color_gender_lives, value|
    value.each do |stats, all_names|
      all_names.each do |name|
        if pigeon_list[name] == nil
          pigeon_list[name] = {}
        end
        if pigeon_list[name][color_gender_lives] == nil
          pigeon_list[name][color_gender_lives] = []
        end
        pigeon_list[name][color_gender_lives].push(stats.to_s)
      end
    end 
  end
  pigeon_list
end 
```

Enter fullscreen mode Exit fullscreen mode