# 使用 Octokit 删除存储库

> 原文：<https://dev.to/ankitchachada/deleting-repositories-using-octokit-4dpp>

我试图从我的 GitHub 帐户中删除一些有问题的存储库，但是一个一个地删除是很麻烦的。此外，每次当您必须删除存储库时，您都必须键入存储库名称。所以我在谷歌上搜索，发现了一个神奇的红宝石 [octokit](https://github.com/octokit/octokit.rb) ，它帮助我删除了选定的存储库。我真的很想得到关于我的代码的反馈。

```
require 'octokit'

client = Octokit::Client.new(:login => 'USERNAME',
                             :password => 'PASSWORD')
repo_hash = {}
client.repositories('USERNAME').each do |i|
 repo_hash[i["id"]] = i["name"] #collecting ID as key and name as value
end

#array of repositories I wanted to delete
repos = ["ABC","XYZ","PQR"]

del_repos = []
repos.each do |repo|
  del_repos << repo_hash.key(repo) #delete_repository needs ID 
end

del_repos.each do |i|
  client.delete_repository(i)
end 
```