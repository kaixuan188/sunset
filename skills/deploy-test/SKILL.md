---
name: Deploy to test environment
description: 使用gh+github action将当前分支最新改动部署到测试环境
---

# 定位当前分支对应测试环境的workflow, 并且运行

0. 首先如果工作区/暂存区存在变动, commit并且push到远程, commit信息你自己简短总结
1. 通过gh查看是否存在已经运行过的当前分支对应workflows获取其ID, title里会包含分支名 `gh run ls --workflow="metaknow-preview-manual.yml" --user kaixuan188`
2. 如果存在, 通过rerun命令重新 `gh run rerun {{ID}}`
3. 如果不存在已运行workflow, 则通过命令运行一个新的, pr_number用一个和分支相关的名称, 比如分支feat/aiconfig可以用aiconfig `gh workflow run metaknow-preview-manual.yml --ref develop -f branch=$(git rev-parse --abbrev-ref HEAD) -f pr_number={{代表分支的字符串}}`
4. 最后使用gh run watch监控运行状态,但重定向输出避免刷屏,等watch完成后再查看最终结果:
   ```bash
   RUN_ID={{ID}}
   echo "正在监控 workflow $RUN_ID,请稍候..."
   # 使用watch但丢弃输出,只等待完成
   gh run watch $RUN_ID > /dev/null 2>&1
   # watch完成后查看最终结果
   echo ""
   gh run view $RUN_ID
   ```
