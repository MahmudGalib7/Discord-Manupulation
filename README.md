# Discord Manipulation

Discord Manipulation is a tool that allows you to enable hidden Developer Settings on Discord and manipulate your status to make it appear like you're performing certain activities without actually doing them. Join a voice channel with a bot or a friend, and you're good to go!

## Features

- **Enable Developer Settings**: Unlock Discord's hidden Developer Settings for advanced control.
- **Access Console Dev Tools**: Use JavaScript code directly in Discord's console to alter status and behavior.
- **Fake Discord Status**: Simulate activities like:
  - Playing a game
  - Streaming
  - Listening to music
  - Coding, etc.
  
  All of this without actually performing those activities!

## Requirements

- A Discord account
- A bot or friend in a voice channel

## How to Use

### Enable Developer Settings:

---

### ✅ For Windows:

1. Press `Win + R` and type `%appdata%`.
2. Go to the **Discord** folder and open `settings.json`.
3. Paste the following code into the file:

```json
"DANGEROUS_ENABLE_DEVTOOLS_ONLY_ENABLE_IF_YOU_KNOW_WHAT_YOURE_DOING": true
```

> [!NOTE]
> Make sure to:
> - Add a comma before the line if it's not the last entry.
> - Save and close the file.

4. Open the **Discord desktop app**.
5. Open the Developer Console using `Ctrl + Shift + I`.
6. Run the JavaScript code (if provided) in the console to unlock Developer Settings.

---

### ✅ For Linux (Universal for Arch, Flatpak, Snap)

#### 1. **Locate the Discord Configuration Directory**

Depending on your installation method:

<details>
<summary><strong>🧩 Pacman (official Arch repo):</strong></summary>

```bash
cd ~/.config/discord/
```

</details>

<details>
<summary><strong>📦 Snap (less common on Arch):</strong></summary>

```bash
cd ~/snap/discord/current/.config/discord/
```

</details>

<details>
<summary><strong>🧊 Flatpak:</strong></summary>

```bash
cd ~/.var/app/com.discordapp.Discord/config/discord/
```

</details>

#### 2. **Edit `settings.json` with your preferred editor**

Example with `nvim`:

```bash
nvim settings.json
```

Or with `nano`:

```bash
nano settings.json
```

#### 3. **Add the following line inside the JSON object:**

```json
"DANGEROUS_ENABLE_DEVTOOLS_ONLY_ENABLE_IF_YOU_KNOW_WHAT_YOURE_DOING": true
```

> [!NOTE]
> Make sure to:
> - Add a comma before the line if it's not the last entry.
> - Save and close the file.

#### 4. **Restart Discord**

Ensure Discord is completely closed:

```bash
ps aux | grep discord
```

Then start it again normally.

#### 5. **Open Developer Tools**

* Press `Ctrl + Shift + I` (Linux/Windows) or `Cmd + Option + I` (macOS).
* This opens the Developer Console.

#### 6. **(Optional) Run JavaScript Code**

If following a specific tweak, paste the provided JavaScript into the console.

---

> [!IMPORTANT] 
> Notes About Completing Discord Quests:
>
> [!NOTE]
> * This **does not work in browsers** for non-video, non-activity quests!
> * For stream/play quests, **use the desktop app**.

> [!WARNING]
> When doing **stream quests**, you need **at least one other account** in the voice channel.

---

### JavaScript Code to Make Discord Manipulated By A Activity That You Are Not Doing:

```javascript
delete window.$;
let wpRequire = webpackChunkdiscord_app.push([[Symbol()], {}, r => r]);
webpackChunkdiscord_app.pop();

let ApplicationStreamingStore = Object.values(wpRequire.c).find(x => x?.exports?.Z?.__proto__?.getStreamerActiveStreamMetadata).exports.Z;
let RunningGameStore = Object.values(wpRequire.c).find(x => x?.exports?.ZP?.getRunningGames).exports.ZP;
let QuestsStore = Object.values(wpRequire.c).find(x => x?.exports?.Z?.__proto__?.getQuest).exports.Z;
let ChannelStore = Object.values(wpRequire.c).find(x => x?.exports?.Z?.__proto__?.getAllThreadsForParent).exports.Z;
let GuildChannelStore = Object.values(wpRequire.c).find(x => x?.exports?.ZP?.getSFWDefaultChannel).exports.ZP;
let FluxDispatcher = Object.values(wpRequire.c).find(x => x?.exports?.Z?.__proto__?.flushWaitQueue).exports.Z;
let api = Object.values(wpRequire.c).find(x => x?.exports?.tn?.get).exports.tn;

let quest = [...QuestsStore.quests.values()].find(x => x.id !== "1248385850622869556" && x.userStatus?.enrolledAt && !x.userStatus?.completedAt && new Date(x.config.expiresAt).getTime() > Date.now())
let isApp = typeof DiscordNative !== "undefined"
if(!quest) {
	console.log("You don't have any uncompleted quests!")
} else {
	const pid = Math.floor(Math.random() * 30000) + 1000
	
	const applicationId = quest.config.application.id
	const applicationName = quest.config.application.name
	const taskName = ["WATCH_VIDEO", "PLAY_ON_DESKTOP", "STREAM_ON_DESKTOP", "PLAY_ACTIVITY"].find(x => quest.config.taskConfig.tasks[x] != null)
	const secondsNeeded = quest.config.taskConfig.tasks[taskName].target
	let secondsDone = quest.userStatus?.progress?.[taskName]?.value ?? 0

	if(taskName === "WATCH_VIDEO") {
		const maxFuture = 10, speed = 7, interval = 1
		const enrolledAt = new Date(quest.userStatus.enrolledAt).getTime()
		let fn = async () => {			
			while(true) {
				const maxAllowed = Math.floor((Date.now() - enrolledAt)/1000) + maxFuture
				const diff = maxAllowed - secondsDone
				const timestamp = secondsDone + speed
				if(diff >= speed) {
					await api.post({url: `/quests/${quest.id}/video-progress`, body: {timestamp: Math.min(secondsNeeded, timestamp + Math.random())}})
					secondsDone = Math.min(secondsNeeded, timestamp)
				}
				
				if(timestamp >= secondsNeeded) {
					break
				}
				await new Promise(resolve => setTimeout(resolve, interval * 1000))
			}
			console.log("Quest completed!")
		}
		fn()
		console.log(`Spoofing video for ${applicationName}.`)
	} else if(taskName === "PLAY_ON_DESKTOP") {
		if(!isApp) {
			console.log("This no longer works in browser for non-video quests. Use the desktop app to complete the", applicationName, "quest!")
		}
		
		api.get({url: `/applications/public?application_ids=${applicationId}`}).then(res => {
			const appData = res.body[0]
			const exeName = appData.executables.find(x => x.os === "win32").name.replace(">","")
			
			const fakeGame = {
				cmdLine: `C:\\Program Files\\${appData.name}\\${exeName}`,
				exeName,
				exePath: `c:/program files/${appData.name.toLowerCase()}/${exeName}`,
				hidden: false,
				isLauncher: false,
				id: applicationId,
				name: appData.name,
				pid: pid,
				pidPath: [pid],
				processName: appData.name,
				start: Date.now(),
			}
			const realGames = RunningGameStore.getRunningGames()
			const fakeGames = [fakeGame]
			const realGetRunningGames = RunningGameStore.getRunningGames
			const realGetGameForPID = RunningGameStore.getGameForPID
			RunningGameStore.getRunningGames = () => fakeGames
			RunningGameStore.getGameForPID = (pid) => fakeGames.find(x => x.pid === pid)
			FluxDispatcher.dispatch({type: "RUNNING_GAMES_CHANGE", removed: realGames, added: [fakeGame], games: fakeGames})
			
			let fn = data => {
				let progress = quest.config.configVersion === 1 ? data.userStatus.streamProgressSeconds : Math.floor(data.userStatus.progress.PLAY_ON_DESKTOP.value)
				console.log(`Quest progress: ${progress}/${secondsNeeded}`)
				
				if(progress >= secondsNeeded) {
					console.log("Quest completed!")
					
					RunningGameStore.getRunningGames = realGetRunningGames
					RunningGameStore.getGameForPID = realGetGameForPID
					FluxDispatcher.dispatch({type: "RUNNING_GAMES_CHANGE", removed: [fakeGame], added: [], games: []})
					FluxDispatcher.unsubscribe("QUESTS_SEND_HEARTBEAT_SUCCESS", fn)
				}
			}
			FluxDispatcher.subscribe("QUESTS_SEND_HEARTBEAT_SUCCESS", fn)
			
			console.log(`Spoofed your game to ${applicationName}. Wait for ${Math.ceil((secondsNeeded - secondsDone) / 60)} more minutes.`)
		})
	} else if(taskName === "STREAM_ON_DESKTOP") {
		if(!isApp) {
			console.log("This no longer works in browser for non-video quests. Use the desktop app to complete the", applicationName, "quest!")
		}
		
		let realFunc = ApplicationStreamingStore.getStreamerActiveStreamMetadata
		ApplicationStreamingStore.getStreamerActiveStreamMetadata = () => ({
			id: applicationId,
			pid,
			sourceName: null
		})
		
		let fn = data => {
			let progress = quest.config.configVersion === 1 ? data.userStatus.streamProgressSeconds : Math.floor(data.userStatus.progress.STREAM_ON_DESKTOP.value)
			console.log(`Quest progress: ${progress}/${secondsNeeded}`)
			
			if(progress >= secondsNeeded) {
				console.log("Quest completed!")
				
				ApplicationStreamingStore.getStreamerActiveStreamMetadata = realFunc
				FluxDispatcher.unsubscribe("QUESTS_SEND_HEARTBEAT_SUCCESS", fn)
			}
		}
		FluxDispatcher.subscribe("QUESTS_SEND_HEARTBEAT_SUCCESS", fn)
		
		console.log(`Spoofed your stream to ${applicationName}. Stream any window in vc for ${Math.ceil((secondsNeeded - secondsDone) / 60)} more minutes.`)
		console.log("Remember that you need at least 1 other person to be in the vc!")
	} else if(taskName === "PLAY_ACTIVITY") {
		const channelId = ChannelStore.getSortedPrivateChannels()[0]?.id ?? Object.values(GuildChannelStore.getAllGuilds()).find(x => x != null && x.VOCAL.length > 0).VOCAL[0].channel.id
		const streamKey = `call:${channelId}:1`
		
		let fn = async () => {
			console.log("Completing quest", applicationName, "-", quest.config.messages.questName)
			
			while(true) {
				const res = await api.post({url: `/quests/${quest.id}/heartbeat`, body: {stream_key: streamKey, terminal: false}})
				const progress = res.body.progress.PLAY_ACTIVITY.value
				console.log(`Quest progress: ${progress}/${secondsNeeded}`)
				
				await new Promise(resolve => setTimeout(resolve, 20 * 1000))
				
				if(progress >= secondsNeeded) {
					await api.post({url: `/quests/${quest.id}/heartbeat`, body: {stream_key: streamKey, terminal: true}})
					break
				}
			}
			
			console.log("Quest completed!")
		}
		fn()
	}
}
```

> [!IMPORTANT]
> Note About Completing Discord Quests:
> This does not work in browsers for non-video, non-activity quests! For stream/play quests, use the desktop app.

> [!WARNING]
> When doing stream quests, you need at least one other account in the voice channel.

## Manipulate Discord Activity:

To fake your status (e.g., playing a game or coding), join a voice channel and run the provided JavaScript code in the console.

Enjoy! Your Discord status will now show the activity you faked while you're simply in the voice channel.

## Disclaimer
This tool is intended for fun and personal use only. Use it responsibly, as violating Discord’s terms of service could result in consequences, including being banned. The developer is not responsible for any misuse of this tool.

## License
This project is licensed under the MIT License. See the LICENSE file for more details.
