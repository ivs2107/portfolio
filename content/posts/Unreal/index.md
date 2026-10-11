---
title: "Nier Automata Remake"
date: 2026-10-10T08:06:25+06:00
description: Nier Automata Fanmade
hero: "/images/nierautomata/title.png"
menu:
  sidebar:
    name: Nier Automata Remake
    identifier: Nier Automata Remake
    weight: 10
tags: ["Basic", "Multi-lingual"]
categories: ["Basic"]
---

## Introduction

This game was made with the goal of learning and master Unreal Engine, as I come from Unity. I played a lot of Nier Automata and was fascinated to know how big games are made and wanted also to try in Unreal how they do that. I really wanted to challenge myself with creating RE:Imagery.

As a programmer I tried to implement everything the only things that I took from others was animations, models and 2D arts.
The game splits like nier automata in 2 clear gameplay mechanics, top down flight shooter and 3D hack and slash.

You can find the github of the game ehre : https://github.com/ivs2107/HackSlashUnreal

## Demo Gameplay

You can find a video showing all the mechanics implemented in the game :
{{< youtube SfO3Ds-El2c >}}

{{< vs >}}

## Story 
I wanted to create a little story with the game to have a guideline of what to do. The story is a little inspired with real life because
I was working in R&D in a group of Imagery and soon it will be closed because of various reasons. So I created the story with in mind trying to stop these closure. 

So starting we are with the principal character 2B who is given a mission getting the "upsi-jam" wich is a dangerous weapon. (Upsi-jam is a reference to one of my first jams I did). 2B who is helped by a pod needs to take this weapon which is located in a castle, protected by many enemies.

She will need to fight in the air like in the ground to take back a dangerous weapon.

### Spoiler

In the end she will fight a boss really dangerous that will absorb the upsi-jam and duplicate himself (reference to one of the upsi-jams where the word was "Multiple" ). And she will be in a space of her mind, where she will see all the members of R&D dissapear and a bright red sword at the ground. Taking it makes her go back to the present self gaining a new ability "berserk", where she is anger to take back what she has lost.

Defeating the boss she will take back the upsijam having the ending scene, but then before the game closes you see 2 pods talking to each other telling that this may not be the end of the story.

## Top Down Flight shooter

The goal was to reproduce the gameplay from nier automata where we start in a sort of airplane.
We have wave of ennemies coming to the player needs to kill the ennemies comming at him.

![Image showing gameplay top down ](/images/nierautomata/topdown.png)

<!--<img src="/images/nierautomata/topdown.png" alt="Nier Photo" width="700px">-->

### Character

This is the blueprint of the airplane representing the player character.
![Image showing the blueprint of the flight character ](/images/nierautomata/FlightCharacter.png)

Even if the character is not humanoid I did some animations to make it more realistic. 

To do so we have iddle animations that plays constantly.
But I also want animation when turning right or left and because I did not have animations of the plane turning right or left so I made it myself with blueprints, using lerp making my plane go one direction or another when I press key and then when the key is released It resets to the default rotation.

<!--![Image showing the blueprint of the flight character ](/images/nierautomata/blueprint_degree.png)-->

<div align="center">
  <img src="/images/nierautomata/blueprint_degree.png" alt="Blueprint degree" width="700px">
</div>

### Help Menu

To make the tutorial for the User, I used the same idea as Nier Automata where the tutorial is above the player and tells what input does what. I attach it to the player so it follows the player with movements, what is important is to set the commonent variable from the "User Interface" to "Screen". 
It makes the UI face always the screen. 

I made methods to "Fade In" and "Fade Out" so I can interact with it whenever I want.

<div style="display: flex; gap: 10px; justify-content: center; align-items: center;">
  <div style="flex: 1;">

  
  ![Help design](/images/nierautomata/help_design.png)

  </div>
  <div style="flex: 1;">

  ![Blueprint help](/images/nierautomata/help_blueprint.png)

  </div>
</div>

### Enemy Waves
I have waves of enemies coming to the player, because i will have a certain amount of waves it means that i wanted something for my level designs later that will be easy to implement. 

My idea was the following I would do a blueprint with each wave where I would put my ennemies out of screen and i would put a sphere. 
The sphere tells where the enemy needs to finish at the end, so we have like waves comming at you with specific placements.

because laoding each Blueprint will not be efficient, each of these blueprint have the same parent this means I can have a method that takes a list of waves and i can choose and then pull the position of the sphere and the positions of the enemies so I don't need to laod everything from blueprint only placement and I can use the blueprint tool for 3D placements to be at ease placing them.

![Waves ](/images/nierautomata/waves.png)

### Attack

To attack I have set hitboxes in the character and where if my projectiles touches my target ir educe the health points of the target.
The interesting part is how to launch the projectile, to do so I do trace a line between the socket of the gun and in front of him.

Then I give the final location and the start to apply a velocity and then launch the bullet in that direction with a sound.

![Attacks ](/images/nierautomata/AttackTopDown.png)


## Character 3D

### States

I made a state system to track the player’s current actions, such as attacking, dodging, taking damage, and other gameplay interactions.

I will go into detail on how my state animation machine works for character movement:

* **Idle:** The default state applied when the character is not moving.
* **Walk/Run:** Switched to when the character starts moving. Depending on the movement speed, the walk and run animations are blended together smoothly.
* **JumpStart** When the character starts the jump action key we start the animation where the character will jump.
* **JumpLoop** When the character has jumped or when the character is falling we launch the Jump loop animation where we see the character in this states until he tauches the ground.
* **JumpEnd** When then character was in JumpLoop and hit the gorund the animation JumpEnd activates

![Animation States](/images/nierautomata/AnimationStates.png)


# Combo system

In Nier: Automata, you can chain light and heavy attacks to create combos, so I implemented something similar in the game. This lets players string moves together for more fluid and satisfying combat. 

I will try explaining the light attacks how it works, when we do an attack we have a soft lock target becuase in Nier automata we can target ennemies, what it does is simplify the attack to have the character turn exactly as we want to land the attack.

After we have locked the nearest target we will look if the character has the right to attack because he could be damaged and cannot attack.

Now maybe the character cannot attack normaly but maybe he can attack in the air, so we look if he can do air combat, if so we launch the combo air animation and attacks.

Let's look at the light combo system so it's easier to understand.
I have made a list for all the combos possible with all the animation the character needs to perform.

So we imagine that the character did a full light attack combo, we watch if the character could attack normally before with light and not heavy and then perform light attack with the index incrementing for the list of combos. Now we imagine a mixed combo of light and heavy, so if the attack before was heavy we start the combo mix, and this means now we need to go trough the list of mixed combo animations.

In the attack we have a buffer of attacks so the user doesn't need to time it perfectly, and then we get the buffer to launch the next attack.

![Light Attacks](/images/nierautomata/lightcombo.png)

# AI

I implemented AI for melee enemies, ranged enemies, and even the boss. Each enemy has simple behaviors, like chasing the player, attacking, or launching a projectile.

To understand how it works I will take a simple behavior system like the range ennemy (see image below), where we have a service on top defining the behavior of the ennemy that help us choose which action the AI needs to do. First thing we need to do is move to the target be close enough to perfom a range attack. This is what we do on left of the tree. Then on the right we will not be static, so we made a strafe function so it moves around the range position to attack again and to maybe dodge attacks.

We do this to have a more dynamic battle.

![Behavior Tree](/images/nierautomata/BehaviorTree2.png)

# Berserk mode

Just like in Nier: Automata, I added a berserk mode where the player becomes faster, deals more damage, and can chain combos more quickly.

This will start by making the character enter the animation of berserk and having an UI with a chronometer for entering the berserk mode.

Then we have particles effects and having the character with particles in red showing he has changed, and he will have a mirror of his movements every 1 sec to show he is faster. 

Then also he will gain stats like damage, movement speed also the animations are fatser.

<div style="display: flex; gap: 10px; justify-content: center; align-items: center;">
  <div style="flex: 1;">

  
  ![BerserkV1](/images/nierautomata/BerserkV1.png)

  </div>
  <div style="flex: 1;">

  ![BerserkV2](/images/nierautomata/BerserkV2.png)

  </div>
</div>

<div style="display: flex; gap: 10px; justify-content: center; align-items: center;">
  <div style="flex: 1;">

  
  ![BerserkV3](/images/nierautomata/BerserkV3.png)

  </div>
  <div style="flex: 1;">

  ![BerserkV4](/images/nierautomata/BerserkV4.png)

  </div>
</div>

# Particles effects

I used particle effects to experiment with Unreal’s systems and polish certain parts of the game, like explosions, hits, and other visual feedback. 

We do this using the Niagara system of unreal engine and touching certain parameters.

![Particle Effect](/images/nierautomata/ParticleEffect.png)

# Cinematics

Like every AAA I love cinematics so I also tried to understand how it works and implemented also in the game.

It is like video editing where you tell the camera when to be open or not, when to blend in with other camera positions and so on.

![Particle Effect](/images/nierautomata/Cinematic.png)

# AI Voice

For the voice lines, I recreated the voice from Nier: Automata using AI voice generation and used it in the game. This allowed me to quickly generate dialogue and add more personality to some moments without needing to record voice actors for a fun project and keep the authencity of nier automata.



