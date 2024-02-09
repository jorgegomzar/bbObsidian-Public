---
category: "[[Efforts]]"
up: "[[Efforts Map]]"
related:
  - "[[Obsidian]]"
  - "[[Quartz]]"
  - "[[GitHub]]"
  - "[[Commander]]"
  - "[[Git]]"
  - "[[Docker]]"
  - "[[Cronjob]]"
aliases: 
created: 2024-02-09
rank: 3
---
## Context
The idea is to set up a workflow so every change is synced between my Obsidian Vault and the web using quartz and GitHub.
## Investigation
- obsidian-git --> Obsidian plugin to sync local Obsidian Vault to a remote git server.
- commander --> Obsidian plugin to bundle all Git sync commands in 1 button.
- quartz --> Open source NodeJS project to render Markdown files and serve them as web pages.
- docker + docker-compose --> Software tools to run apps inside containers.
- cronjob --> GNU software to create cron tasks in Linux systems.
## Implementation
Using obsidian-git I configured my vault to pull changes from the git repo every minute. This way, if someone ever adds content to this repo, we won't be creating merge conflicts too often.
![[obsidian_git_settings.png|500]]
Using commander I added a new Macro to the ribbon. This new macro is called `Git Sync` and is composed of 3 other commands:
- `Git: Pull`
- `Git: Commit all changes`
- `Git: Push`
![[commander_git_sync.png|350]]
This way, whenever I want to publish changes to the repo I just need to click on 1 button.

In a Linux server I've configured 2 cronjobs:
1. To pull changes from the same git repo every 5 minutes. --> to bring changes to already existing files.
2. To restart the Quartz Docker container every 30 minutes. --> to re-render new files and links.

The directory where the repo is living in this server is mounted as a volume in the `/app/src/content` directory in a Docker image running quartz. This way, every time the repo is updated, quartz will render the files as web pages.
## Notes
Updates on already processed notes will work out of the box, but adding new Notes or links will require restarting the app to render all files again.