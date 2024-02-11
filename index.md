---
category: "[[index]]"
up: 
related: 
for: "[[MOC]]"
aliases:
  - Home
---
Hola Celia

> [!info]+ # Atlas
> > *Where would you like to go?*
> ---
> > [!example]- ### 🗺️ MOCs
> > ```dataview
> > LIST 
> > FROM "Atlas"
> > WHERE category = this.for AND !contains(file.name, "TEMPLATE")
> > SORT file.name ASC
> > ```
> 
> > [!danger]- ### 🏷️ Existing categories
> > ```dataview
> > TABLE length(rows) AS Count
> > FROM "Atlas"
> > GROUP BY category AS "Category"
> > SORT length(rows) DESC
> > ```
> 
> > [!tip]- ### 🆕 Recently updated
> > ```dataview
> > TABLE file.mtime AS "Last Edited"
> > FROM "Atlas/Notes"
> > LIMIT 5
> > SORT file.mtime DESC
> > ```

> [!quote]- # Efforts
> > *What can you work on?* 
> 
> For a concentrated view, go to [[Efforts Map]].
> 
> Use this to keep priorities in order and the quickly adjust your bandwidth as needed. 
> 
> > [!Box]+ ### 🔥 On
> > ``` dataview
> > TABLE WITHOUT ID
>  > file.link as "",
>  > rank as "Rank"
> > FROM "Efforts/On"
> > SORT rank desc
> > ```
> 
> > [!Box]+ ### ♻️ Ongoing
> > ``` dataview
> > TABLE WITHOUT ID
> > file.link as "",
> > rank as "Rank"
> > FROM "Efforts/Ongoing"
> > SORT rank desc
> > ```
> 
> > [!Box]- ### 〰️ Simmering
> > Efforts can easily move from `on` to `simmering` in the background.
> > 
> > ``` dataview
> > TABLE WITHOUT ID
> > file.link as "",
> > rank as "Rank"
> > FROM "Efforts/Simmering"
> > SORT rank desc
> > ```
