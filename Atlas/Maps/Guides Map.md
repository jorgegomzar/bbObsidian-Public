---
category: "[[MOC]]"
up: 
related: 
for: "[[Guide]]"
---

> [!example] Index
> ```dataview
> TABLE file.ctime AS "Created", file.mtime AS "Last Edited"
> WHERE category = this.for AND !contains(file.name, "TEMPLATE")
> SORT file.mtime DESC
> ```
