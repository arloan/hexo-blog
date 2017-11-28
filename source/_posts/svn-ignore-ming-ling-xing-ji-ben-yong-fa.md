title: 'svn:ignore 命令行基本用法'
id: 4
categories:
  - 技术
  - 软件
date: 2013-12-04 10:38:00
tags:
  - svn
---

查看当前忽略的项目：
`svn propget svn:ignore`

例如：
```
$ svn propget svn:ignore apps/MobilePortal/android/native/
gen
bin
```

设置忽略项目：
`svn propset svn:ignore <ignore-pattern> <path>`

例如：
```
$ svn propset svn:ignore "*.pyo" foo/bar
```

注意：如果指定目录（`foo/bar`）之前已经设置过忽略项目，那么以上命令就将其覆盖了。如果不想覆盖，那么需要使用“编辑忽略项目”，见下。

编辑忽略项目：
`svn propedit svn:ignore <path>`

此时需要环境变量中指定了编辑器，否则会报错：
```
svn: E205007: None of the environment variables SVN_EDITOR, VISUAL or EDITOR are set, and no 'editor-cmd' run-time configuration option was found
```

可以使用以下命令临时指定编辑器：
`EDITOR=vim svn propedit svn:ignore foo/bar`

删除忽略项目：
`svn propdel svn:ignore <path>`
