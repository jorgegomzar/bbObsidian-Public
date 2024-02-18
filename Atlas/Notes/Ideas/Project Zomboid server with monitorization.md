---
category: "[[Guide]]"
up: "[[Guides Map]]"
related:
  - "[[Games]]"
  - "[[Project Zomboid]]"
  - "[[ELK]]"
---
## Resources
- [Server quick-start and manager](https://github.com/openzomboid/pzlsm)
- [Log Extender](https://github.com/openzomboid/log-extender)
- [Server Messages for login and logout](https://steamcommunity.com/sharedfiles/filedetails/?id=2759895539)
- [Admin commands for Project Zomboid](https://help.akliz.net/docs/admin-commands-for-project-zomboid)
- 1 real life hour = 1 in-game day
## Business Continuity Plan
### [[Project Zomboid]] game server
Instructions:

> [!bug]+ - Create steam user and install SteamCMD server
> 
> ```bash
> # As root
> useradd -m steam && su - steam
> cd && mkdir pz1 && cd pz1
> 
> # Download the manager
> wget -O server.sh https://raw.githubusercontent.com/openzomboid/pzlsm/master/server.sh && chmod +x server.sh
> 
> ./server.sh install
> ```

- Copy [[pzlsm.cfg]] to `/home/steam/pz1/config/pzlsm.cfg`
	- Update `SERVER_NAME` with your desired server name
	- Update `FIRST_RUN_ADMIN_PASSWORD` with your desired admin password
- Start the server once to generate all configurations for the PZ server `./server.sh start`
	- Monitor with `screen -R`
	- Detach with `Ctrl+A,Ctrl+D`
- Once the server is fully up, stop it `./server.sh stop now` --> will stop the server while giving a warning of 10 seconds (without the `now` argument it will stop the server in 5 minutes)
- Copy  [[servertest.ini]] to `/home/steam/pz1/projectzomboid/Zomboid/Server/<SERVER_NAME>.ini`
	- `SERVER_NAME` comes from your [[pzlsm.cfg]] file
	- Fill all variables you'll need to use for your server
- Run `./server.sh start` to start the server again.
- Profit!

> [!warning] NOTE
> Project Zomboid server should always be ran by a non sudo user.
### [[ELK]] monitoring
You neeed:
- docker
- docker-compose
- git

Instructions:
-  Clone the repo `git clone https://github.com/deviantony/docker-elk`
-  Copy [[logstash.yml]] to `docker-elk/logstash/pipeline/logstash.yml`
- Setup ELK in docker
	- Run `docker-compose up -d`
	-  Wait for elasticsearch to be ready `docker-compose logs -f elasticsearch`
	- (OPTIONAL) Change passwords, see `docker-elk`'s README.md
		- I had an issue when changing the passwords, but using `--url` parameter fixed it for me ([here](https://github.com/deviantony/docker-elk/issues/956#issue-2094478592)) 
	- Run `docker-compose up setup` to create the users using `.env` passwords
	- Restart logstash to use the new users `docker-compose restart logstash`
- Log into `<server_ip>:5601` with the `elastic` user
- Set up queries and dashboard in Kibana for these stats:

| Metric | How |
| ---- | ---- |
| Total deaths | Count unique players for `pz_event: "player_death"` and display it. |
| Total players this {time period} | Count unique players for `pz_event: "player_connection"` and display it. |
| Peak users per {time period} | Count unique players for `pz_event: "player_stats"` in a time range and display in a graph. |
| Alive time scoreboard (1h rt = 1d in-game) | Table for players, split by unique player, and add metric for max `hours_alive`. |
| Zombies killed scoreboard | Table for players, split by unique player, and add metric for max `kills`. |
| Death scoreboard | Table for players, split by unique player, and add metric for count of `pz_event: "player_death"`. |
| Profession popularity (how many players have X profession) | Table for players, split by unique player, and add latest profession. |
| Mean play time (play session) | TBD |
#### Log files
All PZ server log files have `%d-%m-%yy_` prepended to their filenames and use txt extension
- Base log files
	- `DebugLog-server.txt`
	- `chat.txt`
	- `user.txt` **--> we'll get death, connection and disconnection events**
	- `pvp.txt`
	- `cmd.txt`
	- `PerkLog.txt`
	- `item.txt`
-  Extended by log-extender
	- Updated with more lines
		- `cmd.txt`
		- `pvp.txt`
	- New
		- `vehicle.txt`
		- `player.txt` **--> we'll get perks, traits, stats, health, profession, kills, hours alive, hp and infected info**
		- `safehouse.txt`
		- `map.txt`
		- `admin.txt`
		- `map_alternative.txt`
		- `craft.txt`
