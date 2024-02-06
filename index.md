---
category: "[[index]]"
up: 
related: 
for: "[[MOC]]"
---

> [!example]+ # 🗺️ MOCs
> ```dataview
> TABLE file.ctime AS "Created", file.mtime AS "Last Edited"
> WHERE category = this.for AND !contains(file.name, "TEMPLATE")
> SORT file.mtime DESC
> ```

> [!danger]+ # 🏷️ Existing categories
> ```dataview
> TABLE length(rows) AS Count
> FROM "Atlas"
> GROUP BY category AS "Category"
> SORT length(rows) DESC
> ```

> [!tip]+ # 🆕 Recently updated
> ```dataview
> TABLE file.mtime AS "Last Edited"
> FROM "Atlas"
> LIMIT 5
> SORT file.mtime DESC
> ```
