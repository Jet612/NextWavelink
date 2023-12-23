<div align="center">


![Logo](https://raw.githubusercontent.com/PythonistaGuild/NextWavelink/master/logo.png)

![Python Version](https://img.shields.io/pypi/pyversions/NextWavelink)
[![PyPI - Version](https://img.shields.io/pypi/v/NextWavelink)](https://pypi.org/project/nextwavelink/)
[![Github License](https://img.shields.io/github/license/PythonistaGuild/NextWavelink)](LICENSE)
[![Lavalink Version](https://img.shields.io/badge/Lavalink-v4.0%2B-blue?color=%23FB7713)](https://lavalink.dev)
![Lavalink Plugins](https://img.shields.io/badge/Lavalink_Plugins-Native_Support-blue?color=%2373D673)
[![Discord](https://img.shields.io/discord/490948346773635102?logo=discord&logoColor=%23FFF&label=Pythonista&labelColor=%235865F2&color=%232B2D31)](https://nextcord.gg/RAKc3HF)


</div>


NextWavelink is a robust and powerful Lavalink wrapper for [Discord.py](https://github.com/Rapptz/nextcord)
NextWavelink features a fully asynchronous API that's intuitive and easy to use.


# Migrating from Version 2 to Version 3:

[Migrating Guide](https://nextwavelink.dev/en/latest/migrating.html)


### Features

- Full asynchronous design.
- Lavalink v4+ Supported with REST API.
- nextcord v2.0.0+ Support.
- Advanced AutoPlay and track recommendations for continuous play.
- Object orientated design with stateful objects and payloads.
- Fully annotated and complies with Pyright strict typing.


## Getting Started

**See Examples:** [Examples](https://github.com/PythonistaGuild/NextWavelink/tree/main/examples)

**Lavalink:** [GitHub](https://github.com/lavalink-devs/Lavalink/releases), [Webpage](https://lavalink.dev)


## Documentation

[Official Documentation](https://nextwavelink.dev/en/latest)

## Support

For support using WaveLink, please join the official [Support Server](https://nextcord.gg/RAKc3HF) on
[Discord](https://discordapp.com)

[![Discord Banner](https://discordapp.com/api/guilds/490948346773635102/widget.png?style=banner2)](https://nextcord.gg/RAKc3HF)


## Installation

**WaveLink 3 requires Python 3.10+**

**Windows**


```sh
py -3.10 -m pip install -U nextwavelink
```

**Linux**

```sh
python3.10 -m pip install -U nextwavelink
```

**Virtual Environments**

```sh
pip install -U nextwavelink
```


## Lavalink

NextWavelink **3** requires **Lavalink v4**.
See: [Lavalink](https://github.com/lavalink-devs/Lavalink/releases)

For spotify support, simply install and use [LavaSrc](https://github.com/topi314/LavaSrc) with your `nextwavelink.Playable`


### Notes

- NextWavelink **3** is compatible with Lavalink **v4+**.
- NextWavelink has built in support for Lavalink Plugins including LavaSrc and SponsorBlock.
- NextWavelink is fully typed in compliance with Pyright Strict, though some nuances remain between nextcord and nextwavelink.
