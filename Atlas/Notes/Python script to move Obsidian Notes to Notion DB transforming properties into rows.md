---
category: "[[Snippets]]"
up: "[[Snippets Map]]"
related: 
aliases:
---

```python
import io
from datetime import date as date_class
from notion_client import Client
from pathlib import Path
from pprint import pprint
from pydantic import BaseModel, Field
from typing import Optional


class NotionRow(BaseModel):
    anime: str
    aliases: Optional[list[str]] = Field(default_factory=list)
    links: Optional[str]
    episodes_total: Optional[int]
    episodes_seen: Optional[int] = 0
    tags: list[str]
    grade: Optional[int]
    companions: Optional[list[str]]
    status: str

    def to_notion_properties(self) -> dict:
        return {
            "anime": {"title": [{"text": {"content": self.anime}}]},
            "aliases": {"multi_select": [{"name": alias} for alias in self.aliases]},
            "links": {"url": self.links},
            "episodes_total": {"number": self.episodes_total},
            "episodes_seen": {"number": self.episodes_seen},
            "tags": {"multi_select": [{"name": tag} for tag in self.tags]},
            "grade": {"number": self.grade},
            "companions": {"multi_select": [{"name": companion} for companion in self.companions]},
            "status": {"status": {"name": self.status}},
        }


def write_row(client: Client, database_id: str, row: NotionRow):
    client.pages.create(**{
        "parent": {"database_id": database_id},
        "properties": row.to_notion_properties(),
    })


def clean_item(v: str) -> str:
    return v.replace("[[", "").replace("]]", "").replace('"', '').strip()


def get_row(anime: str, data: io.BytesIO) -> NotionRow:
    row = {"anime": anime}
    data = [_ for _ in data.read().decode().splitlines() if _ != "---"]
    reading_list = False
    reading_links = False
    list_name = None
    list_values = []
    for line in data:
        if any(prop in line for prop in {"category", "up", "related"}):
            continue

        if reading_links is True:
            reading_links = False
            if line.startswith("  - "):
                row["links"] = line.replace("  - ", "")
                continue
            row["links"] = None

        if reading_list is True:
            if line.startswith("  - "):
                list_values.append(clean_item(line.replace("  - ", "")))
                continue

            reading_list = False
            row[list_name] = list_values
            list_values = []

        if any(prop in line for prop in {"aliases", "tags", "companions"}):
            reading_list = True
            list_name = line.replace(":", "").strip()
            list_values = []
            continue

        if "links" in line:
            reading_links = True
            continue

        key, value = [clean_item(_) for _ in line.split(":")]
        row[key] = value if value not in ["", None] else None

    return NotionRow.model_validate(row)


def main():
    notion_token = ''
    notion_database_id = ''

    client = Client(auth=notion_token)

    anime_dir = Path(__file__).parent
    for anime_file in anime_dir.iterdir():
        if anime_file.name == Path(__file__).name:
            continue
        with anime_file.open("rb") as f:
            row = get_row(anime_file.name.replace(".md", ""), io.BytesIO(f.read()))
        write_row(client, notion_database_id, row)


if __name__ == '__main__':
    main()


```