# 从您的 shell 中轻松跟踪清晰的 Linux 版本

> 原文：<https://dev.to/antoniomeireles/easily-track-clear-linux-releases-from-your-shell-1bi7>

*   运行 **[Clear Linux](https://clearlinux.org)** ，禁用 [swupd](https://clearlinux.org/documentation/clear-linux/guides/maintenance/swupd-guide) 的自动更新？
*   想在更新前知道发生了什么还是只是好奇？
*   想要节省时间并避免浏览[发布的](https://download.clearlinux.org/releases/)网页？

如果以上所有的*的**都是**，那么*就加到你的*上。bashrc* (或*)。zshrc* 以下片段:

```
function clr-releases {
  TAIL=${TAIL:-10}
  upstream="https://download.clearlinux.org"
  latest=$(curl -Ls ${upstream}/latest)
  last=()

  while IFS='' read -r line; do last+=("${line}")
  done < <(curl -Ls ${upstream}/releases | sed -e 's/<[^>]*>//g' | \
           awk '/^[0-9]/ {print gensub(/\//,"",1,$1)"_"$2"_"$3}' | \
           sort -hk1 -t _ | tail -n "${TAIL}")

  for r in "${last[@]}"; do
    [[ "${r}" == ^${latest}* ]] && echo "${r//_/ }" || echo "${r//_/ } (current)"
  done
}

function clr-log {
  upstream="https://download.clearlinux.org"
  [ -z "$1" ] && rev=current || rev=$1
  {
    [[ ${rev} != "current" ]] && printf "=> current is %s\n" "$(curl -Ls ${upstream}/latest)"
    curl -Ls "${upstream}/releases/${rev}/clear/RELEASENOTES" && echo
  } | less
} 
```

默认情况下，`clr-releases`将显示最近发布的 10 个版本(可以通过`TAIL` *env* 变量进行调整)

如果没有提供参数或者提供的版本号之一，`clr-log`将显示`current` ClearLinux 版本的*变更日志*