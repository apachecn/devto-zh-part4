# Hashketball 实验室

> 原文：<https://dev.to/harlessmark/hashketball-lab-1521>

## 问题

我得到了一个很长的方法(见下文),两个不同的球队和每个球员的一些统计数据。我需要想出不同的方法来回答几个问题。

1.  构建一个方法，`num_points_scored`,它接受一个玩家名字的参数，并返回该玩家的分数。

2.  构建一个方法`shoe_size`，它接受一个球员姓名的参数，并返回该球员的鞋号。

3.  构建一个方法`team_colors`，它接受球队名称的参数并返回该球队颜色的数组。

4.  构建一个方法`team_names`，该方法对游戏散列进行操作以返回球队名称的数组。

5.  构建一个方法`player_numbers`，它接受一个球队名称的参数，并返回该队球衣号码的数组。

6.  构建一个方法`player_stats`，它接受一个关于玩家名字的参数，并返回该玩家的统计数据。

```
def game_hash
  {
    away: { team_name: 'Charlotte Hornets',
      colors: %w[Turquoise Purple],
      players: [
        { player_name: 'Jeff Adrien',
          number: 4,
          shoe: 18,
          points: 10,
          rebounds: 1,
          assists: 1,
          steals: 2,
          blocks: 7,
          slam_dunks: 2 },
        { player_name: 'Bismack Biyombo',
          number: 0,
          shoe: 16,
          points: 12,
          rebounds: 4,
          assists: 7,
          steals: 22,
          blocks: 15,
          slam_dunks: 10 },
        { player_name: 'DeSagna Diop',
          number: 2,
          shoe: 14,
          points: 24,
          rebounds: 12,
          assists: 12,
          steals: 4,
          blocks: 5,
          slam_dunks: 5 },
        { player_name: 'Ben Gordon',
          number: 8,
          shoe: 15,
          points: 33,
          rebounds: 3,
          assists: 2,
          steals: 1,
          blocks: 1,
          slam_dunks: 0 },
        { player_name: 'Kemba Walker',
          number: 33,
          shoe: 15,
          points: 6,
          rebounds: 12,
          assists: 12,
          steals: 7,
          blocks: 5,
          slam_dunks: 12 }
            ] },
    home: { team_name: 'Brooklyn Nets',
      colors: %w[Black White],
      players: [
        { player_name: 'Alan Anderson',
          number: 0,
          shoe: 16,
          points: 22,
          rebounds: 12,
          assists: 12,
          steals: 3,
          blocks: 1,
          slam_dunks: 1 },
        { player_name: 'Reggie Evans',
          number: 30,
          shoe: 14,
          points: 12,
          rebounds: 12,
          assists: 12,
          steals: 12,
          blocks: 12,
          slam_dunks: 7 },
        { player_name: 'Brook Lopez',
          number: 11,
          shoe: 17,
          points: 17,
          rebounds: 19,
          assists: 10,
          steals: 3,
          blocks: 1,
          slam_dunks: 15 },
        { player_name: 'Mason Plumlee',
          number: 1,
          shoe: 19,
          points: 26,
          rebounds: 11,
          assists: 6,
          steals: 3,
          blocks: 8,
          slam_dunks: 5 },
        { player_name: 'Jason Terry',
          number: 31,
          shoe: 15,
          points: 19,
          rebounds: 2,
          assists: 2,
          steals: 4,
          blocks: 11,
          slam_dunks: 1 }
      ] }
  }
end 
```

Enter fullscreen mode Exit fullscreen mode

## 我学到了什么

这个实验室对我来说更容易理解，更有趣，而且我比上周的蔬菜水果商实验室学到了更多。

## 文章不再可用

1.  如何接受参数并从嵌套的哈希或数组中返回字符串。

2.  如何使用`.map`枚举从嵌套的散列或数组中创建一个新的数组。

3.  以及几乎所有接受参数并返回散列中任何一行的东西。

## 最终迭代

```
require 'pry'

def num_points_scored(player_search)
  game_hash.each do |team, team_info|
    team_info[:players].each do |player|
      if player[:player_name] == player_search
        return player[:points]
      end
    end
  end
end

def shoe_size(name)
  game_hash.each do |team, team_info|
    team_info[:players].each do |player|
      if player[:player_name] == name
        return player[:shoe]
      end
    end
  end
end

def team_colors(team_input)
  if team_input.downcase == "charlotte hornets" 
    return game_hash[:away][:colors]
  else return game_hash[:home][:colors]
  end
end

def team_names
  game_hash.map do |team, team_info|
    team_info[:team_name]
  end
end

def player_numbers(input)
  output = []
  game_hash.each do |team, team_info|
    if team_info[:team_name] == input 
      team_info.each do |key, value|
        if key == :players
          value.each do |player|
          output.push(player[:number])
          end
        end
      end
    end
  end
  return output
end

def player_stats(input)
  game_hash.each do |team, team_info|
    team_info.each do |key, value|
      if key == :players
        value.each do |player|
          if input == player[:player_name]
            player.delete(:player_name) # having player name inside the hash was a bad idea!
            return player
          end
        end
      end
    end
  end
end

def big_shoe_rebounds
  big_shoe = 0
  rebounds = 0
  game_hash.each do |team, team_info|
    team_info[:players].each do |player|
      if player[:shoe] > big_shoe
        big_shoe = player[:shoe]
        rebounds = player[:rebounds]
      end
    end
  end
  return rebounds
end 
```

Enter fullscreen mode Exit fullscreen mode