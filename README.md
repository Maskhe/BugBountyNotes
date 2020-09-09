# BugBountyNotes
简单记录下自己在挖掘SRC的一些技巧，备忘

#### 快速查找ssrf/open redirect

gau 目标网站 -s  | head -n 5000 > 目标.txt; cat 目标.txt | sort -u | grep -a -i \=http > redirects.txt

