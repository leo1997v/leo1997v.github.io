---
layout:     post   				    # 使用的布局（不需要改）
title:      How to Write Gitignore			# 标题 
subtitle:   gitignore patterns #副标题
date:       2021-03-18			# 时间
author:     Leo 						# 作者
header-img: img/post-bg-github-cup.jpg 	#这篇文章标题背景图片
catalog: true 						# 是否归档
tags:								#标签
    - Git
---

>Notes about writing the file `.gitignore`.

# How to Write Gitignore

Ignored files are tracked in a special file named `.gitignore` that is checked in at the root of your repository. There is no explicit git ignore command: instead the `.gitignore` file must be edited and committed by hand when you have new files that you wish to ignore. `.gitignore `files contain patterns that are matched against file names in your repository to determine whether or not they should be ignored.

## Git ignore patterns

`.gitignore` uses globbing patterns to match against file names. You can construct your patterns using various symbols:

Pattern|Example matches|Explanation
:--|:--|:--
**/logs|logs/debug.log<br>logs/monday/foo.bar<br>build/logs/debug.log|You can prepend a pattern with a double asterisk to match directories anywhere in the repository.
**/logs/debug.log|logs/debug.log<br>build/logs/debug.log<br>*but not*<br>logs/build/debug.log|You can also use a double asterisk to match files based on their name and the name of their parent directory.
*.log|debug.log<br>foo.log<br>.log<br>logs/debug.log|An asterisk is a wildcard that matches zero or more characters.
*.log<br>!important.log|debug.log<br>trace.log<br>*but not*<br>important.log<br>logs/important.log|Prepending an exclamation mark to a pattern negates it. If a file matches a pattern, but also matches a negating pattern defined later in the file, it will not be ignored.
*.log<br>!important/*.log<br>trace.*|debug.log<br>important/trace.log<br>*but not*<br>important/debug.log|Patterns defined after a negating pattern will re-ignore any previously negated files.
/debug.log|debug.log<br>*but not*<br>logs/debug.log|Prepending a slash matches files only in the repository root.
debug.log|debug.log<br>logs/debug.log|By default, patterns match files in any directory.
debug?.log|debug0.log<br>debugg.log<br>*but not*<br>debug10.log|A question mark matches exactly one character.
debug[0-9].log|debug0.log<br>debug1.log<br>*but not*<br>debug10.log|Square brackets can also be used to match a single character from a specified range.
debug[01].log|debug0.log<br>debug1.log<br>*but not*<br>debug2.log<br>debug01.log|Square brackets match a single character form the specified set.
debug[!01].log|debug2.log<br>*but not*<br>debug0.log<br>debug1.log<br>debug01.log|An exclamation mark can be used to match any character except one from the specified set.
debug[a-z].log|debuga.log<br>debugb.log<br>*but not*<br>debug1.log|Ranges can be numeric or alphabetic.
logs|logs<br>logs/debug.log<br>logs/latest/foo.bar<br>build/logs<br>build/logs/debug.log|If you don't append a slash, the pattern will match both files and the contents of directories with that name. In the example matches on the left, both directories and files named logs are ignored.
logs/|logs/debug.log<br>logs/latest/foo.bar<br>build/logs/foo.bar<br>build/logs/latest/debug.log|Appending a slash indicates the pattern is a directory. The entire contents of any directory in the repository matching that name – including all of its files and subdirectories – will be ignored.
logs/<br>!logs/important.log|logs/debug.log<br>logs/important.log| `logs/important.log` will still be negated in the example on the left.<br>Due to a performance-related quirk in Git, you *can not* negate a file that is ignored due to a pattern matching a directory.
logs/**/debug.log|logs/debug.log<br>logs/monday/debug.log<br>logs/monday/pm/debug.log|A double asterisk matches zero or more directories.
logs/*day/debug.log|logs/monday/debug.log<br>logs/tuesday/debug.log<br>*but not*<br>logs/latest/debug.log|Wildcards can be used in directory names as well.
logs/debug.log|logs/debug.log<br>*but not*<br>debug.log<br>build/logs/debug.log|Patterns specifying a file in a particular directory are relative to the repository root. (You can prepend a slash if you like, but it doesn't do anything special.)

- Using `#` to include comments in `.gitignore` file.
- Using `\` to escape `.gitignore` pattern characters if files or directories containing them.

