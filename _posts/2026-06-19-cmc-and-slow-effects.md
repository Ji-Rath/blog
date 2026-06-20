---
title: CMC and Slow Effects
description: A brief study of slowing effects on characters
pin: false
date: 2026-06-19
categories: Gameplay
tags: movement
---
# What is the Problem

The character movement component is a complex system used for handling smooth server-authoritative movement. Generally, CMC handles client prediction out of the box and does so quite well.

Movement slowing effects on the surface seems like an easy problem - just slow the player down...and in a single player game that is mostly the case. It begins to fall apart once you are in a networked environment.

> It should also be noted that fixed/predictable speed effects like sprinting can be handled completely in the CMC (see More Information).
{: .prompt-info }

This is because the client will have authority over whether it wants to sprint or not, and the speed effect can be pre-determined. The issue covered here mainly pertains to dynamic slow/speed effects and effects that cannot be predicted.

- Not every slowing effect can be predicted
- Server corrections are often quite jarring and not great for the player experience
- We don't want to give the client full authority over their movement (exploits, etc)

# What is the Solution

What I will outline is simply how I solved the problem for my use case. Every game may have different requirements and thus, different solutions.

- Predict slowing effects where possible. ex. self-applied speed/slow effects.
- If using GAS, use prediction keys to add a gameplay effect instantly. When removing the effect, trigger a reverse gameplay effect when possible - as removing gameplay effects cannot be predicted.
- Add a bit of leniency to the CMC. Override `ServerExceedsAllowablePositionError()` to prevent sending corrections to the client (within a reasonable limit). Consider enabling `bServerAcceptClientAuthoritativePosition`  so that the server will accept a slightly incorrect client position.

> Enabling  `bServerAcceptClientAuthoritativePosition` can allow clients to exploit and cheat. Use sparingly and enable temporarily. ex. Enabled only when we recently applied a movement buff/debuff.
{: .prompt-note }

> When we give the client semi-authority over their position and apply a slow effect on the server, The client's move speed will not instantly change - essentially delaying any move effect.
{: .prompt-info }

- If you do not care about client cheats, like in some co-op games, you can enable `bServerAcceptClientAuthoritativePosition` and `bIgnoreClientMovementErrorChecksAndCorrection` which effectively gives the client full authority over their position.

# More Information

- [Best Practices for Networked Movement Unreal Fest Bali 2025](https://youtu.be/tSVcRbfK8X8?si=kYESkdcnstc6BlZq)
- [tranek/GASDocumentation - Question 4](https://github.com/tranek/GASDocumentation#1111-community-questions-1)
- [CMC Sprinting Setup and Guide](https://youtu.be/17D4SzewYZ0?si=rqABFp2FeMb5jtGN)

