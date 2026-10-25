<p align="center">
  <img src="assets/logo.png" alt="Extra Elytra logo" width="256" height="256">
</p>

# Extra Elytra

A Fabric mod that brings the *Extra Elytra* feature from the Wurst client to Minecraft: control your gliding speed and height with the movement keys, hover in place, and take off instantly, all without fireworks.

![Minecraft](https://img.shields.io/badge/Minecraft-1.21.11-00AF5C)
![Fabric](https://img.shields.io/badge/Loader-Fabric-dbd0b4)
![Version](https://img.shields.io/badge/Version-1.2.4-6e5494)
![License](https://img.shields.io/badge/License-GPL--3.0-blue)
![Status](https://img.shields.io/badge/Maintained%20by-AI%20(Codex)-orange)

> [!WARNING]
> This project is built and maintained entirely by **AI** (Codex). **No human reads the issues or writes the code.**
>
> Codex is the only maintainer, and maintenance is limited to **fixing bugs when they are found**. Don't expect new features, replies to questions, or ports to other Minecraft versions.
>
> It targets **Minecraft 1.21.11 only** and is provided **as is**. If you need something else, fork it (see [Building from Source](#building-from-source)).

> [!IMPORTANT]
> Extra Elytra must be installed on **both the client and the server**. Without the server side the client stays disabled and nothing changes.

## What it does

- **[Speed Control](#speed-control)** - Speed up, slow down and strafe while gliding with the movement keys
- **[Height Control](#height-control)** - Climb and descend with Jump and Sneak
- **[Hovering](#hovering)** - Hold Jump and Sneak together to hover in place
- **[Instant Fly](#instant-fly)** - Take off with a single Jump press, no double-jump
- **[Stop Flying in Water](#stop-flying-in-water)** - Stop gliding when you touch water
- **[Server Handshake](#server-handshake)** - The client only activates on servers that have the mod
- **[Config Screen](#configurations)** - In-game settings through Mod Menu and YACL

## Requirements

- **Minecraft** 1.21.11
- **Fabric Loader** 0.19.2+
- **Fabric API** (matching your Minecraft version)
- **YetAnotherConfigLib (YACL)** 3.8.1+1.21.11-fabric or newer (declared as a hard dependency)
- **Mod Menu** *(optional, client only; gives you the in-game config screen)*
- **Java 21** (required by Minecraft 1.21.11)

### Installation

1. Install [Fabric Loader](https://fabricmc.net/use/) for Minecraft 1.21.11
2. Download [Fabric API](https://modrinth.com/mod/fabric-api) and [YACL](https://modrinth.com/mod/yacl) for 1.21.11
3. Download `ExtraElytra-1.2.4.jar` from the **Releases** page
4. Place the mod jar, Fabric API and YACL in the `mods` folder of your **client**
5. Place the same jar, Fabric API and YACL in the `mods` folder of your **server**
6. Launch Minecraft and join the server

## Configurations

The settings menu is accessible via [Mod Menu](https://modrinth.com/mod/modmenu) (**Mod Menu → Extra Elytra → Config**).

#### Generic Tab

| Setting | Default | Description |
| --- | :---: | --- |
| **Enable Mod** | ON | Master switch for the whole mod |
| **Instant fly** | OFF | Enable [Instant Fly](#instant-fly) |
| **Speed control** | ON | Enable [Speed Control](#speed-control) |
| **Height control** | OFF | Enable [Height Control](#height-control) |
| **Hovering** | OFF | Enable [Hovering](#hovering) (needs Height control) |
| **Stop flying in water** | ON | Enable [Stop Flying in Water](#stop-flying-in-water) |
| **Show in chat when enabled** | OFF | Print a chat message when the server confirms the mod is installed |

#### Config File

Settings are stored in `config/extra_elytra_config.toml`:

```toml
enableMod = true
instantFly = false
speedCtrl = true
heightCtrl = false
hovering = false
stopInWater = true
chatLog = false
```

Manual edits must keep each line exactly in the form `key = true`. The loader matches lines literally, and any option not set to `true` in an existing file is read as `false`.

## Feature Details

### Speed Control

While gliding, the movement keys change your speed directly instead of needing fireworks.

| Key (default) | Effect |
| --- | --- |
| **W** | Accelerate forward |
| **S** | Slow down / accelerate backward |
| **A / D** | Strafe left / right |

Keys follow your current key bindings.

---

### Height Control

While gliding, change your altitude with the jump and sneak keys.

| Key (default) | Effect |
| --- | --- |
| **Space** | Climb |
| **Shift** | Descend |

Off by default.

---

### Hovering

With **Height control** enabled, hold **Space** and **Shift** together to hover in place. Releasing either key goes back to normal height control. Off by default.

---

### Instant Fly

Normally you jump, then jump again in mid-air to start gliding. With Instant Fly on, holding **Space** while wearing a usable elytra jumps and starts gliding right away. A cooldown of 20 ticks stops repeated jumps. Off by default.

---

### Stop Flying in Water

Touching water while gliding cancels the glide, so the mod doesn't push you along through water. On by default.

---

### Server Handshake

The mod only does anything on servers that have it installed.

1. When a player joins, the server sends a `mod_check` packet.
2. When the client receives it, it marks the server as supported and enables the mod.
3. Each client tick while gliding, the client applies its speed/height changes locally and sends its velocity to the server in a `motion_update` packet.
4. The server sets the player's velocity to that value if the player is currently gliding.

Client state resets on join and on disconnect, so it never carries over between servers.

> [!NOTE]
> The server applies the velocity reported by the client **without validating it**. That is what makes the movement smooth, but it also means anyone with the client mod (or a modified client) can glide faster than vanilla allows. Only install it on servers where that's acceptable, and expect anti-cheat plugins to flag it.

---

## Known Issues

These are known and have not been fixed yet. Codex will only change them if they are found as bugs:

- Left/right strafing uses `Vec3d.rotateY(90)`, which takes **radians**, so the strafe direction is probably not exactly perpendicular to your heading.
- The config file is parsed line by line with exact matching, so hand-edited files with extra spaces or different casing are read as `false`.
- Only Minecraft 1.21.11 is supported.

## Building from Source

### Prerequisites

- **Java 21**
- **Gradle** (included via wrapper)

### Building the Minecraft Mod

1. **Clone the repository**

```
git clone <your-fork-url>
cd extra-ely
```

2. **Build**

```
# Windows
.\gradlew.bat build

# Linux/Mac
./gradlew build
```

The output jar is located in `build/libs/` (`ExtraElytra-1.2.4.jar`).

Versions are set in `gradle.properties` (`minecraft_version`, `yarn_mappings`, `loader_version`, `fabric_version`, `yacl_version`, `modmenu_version`). To port to another Minecraft version yourself, update those and fix whatever the mappings changed.

## References

- [Wurst Client](https://www.wurstclient.net/) - Original *Extra Elytra* feature
- [Fabric API](https://github.com/FabricMC/fabric-api) - Networking and client events
- [YetAnotherConfigLib](https://github.com/isXander/YetAnotherConfigLib) - Config screen
- [Mod Menu](https://github.com/TerraformersMC/ModMenu) - Config screen integration

## Credits

Author: **Rumi**

## Disclaimer

Extra Elytra gives you movement abilities that vanilla gliding doesn't, and it only works on servers that choose to install it. It is not intended or encouraged for bypassing server rules, evading bans, or gaining unfair advantages. Users are responsible for complying with the rules and terms of service of any server they connect to.

## License

Released under the **GNU General Public License v3.0**. See [LICENSE](LICENSE).

---

<p align="center"><sub>Built and maintained entirely by AI (Codex). No human reads issues or writes code. Bug fixes only.</sub></p>
