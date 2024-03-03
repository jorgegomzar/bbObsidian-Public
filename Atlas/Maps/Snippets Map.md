---
category: "[[MOC]]"
up: 
related: 
aliases: 
for: "[[Snippets]]"
---

> [!example] Index
> ```dataview
> TABLE file.ctime AS "Created", file.mtime AS "Last Edited"
> WHERE category = this.for AND !contains(file.name, "TEMPLATE")
> SORT file.mtime DESC
> ```
