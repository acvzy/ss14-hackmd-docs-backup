---
title: SS14 Docs
description: Freely editable, organized documentation for the Space Station 14 project. Game design, general thoughts, and links are examples of what might fit here.
tags: ss14, documentation, game design
breaks: false
# Don't touch the YAML stuff. Breaks=false is for compatibility with github in case we decide to sync with them later.
---

# Space Station 14 docs

## What is this?

The main repository for the [Space Station 14 project](https://github.com/space-wizards/space-station-14)'s documentation, roadmaps, and general content.

Use the table of contents at the top right for navigating the document.

You can fully comment/edit this document, even without logging in, but [please read the primer on contributing first](#Meta). The document's auto-versioned, so trying to grief it is pointless.

# Gameplay Design 
In no particular order.
## Machinery Damage

Bullets should collide with machines and all machines take bullet/explosion damage as expected, using the damage component and thresholds for breaking

**3 unique methods of destroying a structure**
- Devolve into previous construction state, dropping a % of materials
        *ex. walls*
- Go into a unique "destroyed" state
state either has a custom sprite, or the normal sprite with a "damaged" layer on top of it (cracks, burn marks)
        This destroyed state is special, can only be transitioned into by damage
        This is considered a non-complete construction state, so the machine won't work
*ex. completed structures*
- Remove the structure, dropping a % of materials
*ex. non-complete construction states*

Destroyed states can either be repaired back into the completed state, or immediately removed for a % of materials and every completed state should define a destroyed state.


```OnMachineBreak:
if CurrentState has defined DestroyedState
    move to DestroyedState
else
    if CurrentState has DevolveFlag
        devolve to parent construction state, drop % of current state materials
    else
        immediate removal, drop % of all state materials (by walking up the construction state tree)
    end
end
```

Construction states need to be stored in a tree, where each state node has exactly 1 parent, not including the special destroyed state

For example breaking a computer in SS13 breaks the screen, so you need to remove the old screen and insert a new one the last bit being the same construction steps to make a computer in the first place.

Ideally all machines would have some form of clear broken version of their sprite to make them recognizable, but a
fallback using the normal sprite with a "damaged" layer on top of it (cracks, burn marks) should serve as a placeholder.

The examine tooltip should inform the player how to go about repairing a "destroyed" state.

## Computers

**Naming**
- Consoles should be specific use computers like a comms console
- Computers should be like those modular ones you can run multiple programs on bay has
- Terminal for the DWAINE terminals Goon has [citation needed]

## Hacking

Basically how SS13 handles hacking, but with a nicer UI. Wires can be cut with wire cutters, and the gold metal contacts can be pulsed with the multitool.
Ideas/Inspiration:
ERIS Hacking design Google Doc [Need link]
[Hacking ideas/improvements Issue](https://github.com/space-wizards/space-station-14/issues/410)

## Research

Research in SS14 is currently following the [TG Station research tree](https://tgstation13.org/wiki//images/thumb/f/f4/ResearchTree.png/800px-ResearchTree.png). The technology tree for SS14 can be found [here](https://github.com/space-wizards/space-station-14/blob/master/Resources/Prototypes/Technologies/technologies.yml).
```
- type: technology

 name: "Industrial Engineering"

 id: Industrial_Base_1

 description: A refresher course on modern engineering technology.

 icon: Buildings/research.rsi/protolathe.png

 requiredpoints: 1000

 requiredtechnologies:
   - Base_1
```
As you can see in this example tech for Industrial Engineering. It is given an ID that defines it while providing something to reference in the requiredtechnologies field for other techs.
As seen in this next tech, in order to research it you require the Industrial_Base_1 tech as a prerequisite.

```
- type: technology

 name: Material sheet printing

 id: Sheets

 description: Print those sheets!

 icon: Objects/Materials/sheet_metal.png

 requiredpoints: 250

 requiredtechnologies:

   - Industrial_Base_1

 unlockedrecipes:

   - MetalStack

   - GlassStack
```

## Combat

> https://discordapp.com/channels/310555209753690112/311537926376783886/638759915170562049

The main problem is that people stay multiple tiles away from each other to prevent instakills (as in parapen/injection). We want to encourage people to RP and not have ‘crippling anxiety’ all the time.

Try to remove instakills from combat to allow the assailee to react.

One suggestion for solving it would be to replace stuns with knockback or some other mechanic.

- [Disabler](https://discordapp.com/channels/310555209753690112/311537926376783886/638819441127194657): Disabler shots disable each limb (and one's voice?) for a different random duration. If shot multiple times, a higher disable duration for a specific 'part' will replace one currently in effect.
Use targeting system for some control over where the shot hits.
Balance each body part so that it’s not just shoot the legs. Stuns on headshots, disarm on arm/hand, slowed movement on legs.

- [Firing slits with lockers](https://discordapp.com/channels/310555209753690112/311537926376783886/609125648606167209)

## Mechs

> https://discordapp.com/channels/310555209753690112/311537926376783886/638828691941752832

Not sure about whether to add mechs yet. Currently it’s hard to get and not always useful thing.

[SS13 Mech rework design doc by XTDM ](https://docs.google.com/document/d/1sESJgyLjYhcFnTGx7aB7ql8-ObQR3xeQQm_vJzpQymM/edit?usp=drivesdk)

**TL;DR** Mechs are not necessary and need to be reworked to be less focused on combat and more useful for a non-combat round.

## PDA/Comms

> https://discordapp.com/channels/310555209753690112/311537926376783886/609131086789869569

## Races

Queue HEATED debate

# Sound Design 

## Muzak

As discussed on the discord, music is tricky to decide on since everyone has different music tastes and what fits the concept of SS14 might not fit for the next person.

The lobby music should be the only one that is actually a full length song, the others should all just be short jingles or ambient music. The golden standard is Title 3.

## Ambience 
The ambient sounds/music should always exist in the world, this means that if in the general zone you hear a vent, you can track it and weld it to stop the sound.

## Audio Systems
To improve immersion there should be added the next features: A positional audio system capable of detecting the state and position of an object, modifying the sound that it plays depending in the distance, objects, density of objects between them, etc. 

# Technical Design

## Camera Rotation

> https://discordapp.com/channels/310555209753690112/560845886263918612/611249500841050124

> https://discordapp.com/channels/310555209753690112/310555209753690112/631946964514242561

## Field of View

> https://discordapp.com/channels/310555209753690112/310555209753690112/631901190841565204

## Action Blocker

> https://discordapp.com/channels/310555209753690112/560845886263918612/612755591567376415

## Atmos

> https://discordapp.com/channels/310555209753690112/310555209753690112/490190802459295744

> https://discordapp.com/channels/310555209753690112/310555209753690112/648617915838365705

# Muh Lore

## Arch-Plasma
Renaming Plasma.

Phoron, Plasma, whatever. Probably not Plasma.

[2017-05-11 Very long Discord discussion with many name suggestions 
](https://discordapp.com/channels/310555209753690112/311537926376783886/312287658946854925)[2019-10-01 Discord discussion
](https://discordapp.com/channels/310555209753690112/623964557064273951/628512272217669633)[2019-10-24 Short Discord discussion
](https://discordapp.com/channels/310555209753690112/623964557064273951/636867401987784724)

# Miscellaneous Opinions/Discussions

[On actions that take time to complete](https://discordapp.com/channels/310555209753690112/311537926376783886/604445031683850240)

[On storing data in a database](https://discordapp.com/channels/310555209753690112/310555209753690112/641265298132959261)

Should we setup a Patreon?
> paypigs when

Short-form discussion doesn't really fit in this document. Better to use the discord and then later summarize in this document.


---

# Meta

Regarding this document itself.

## The quick rundown

* Anyone is welcome to comment on, or edit the document; you don't need to be signed in. We're basically operating off trust (and the ability to easily revert grief).
* You can comment by hovering over a line and hitting the speech bubble to the right of it.
* You can edit by hitting the pencil button at the top right.
* We suggest you sign up anyway though, it's fast. You can use email, a github account, facebook, whatever. It'll help with attributing and discussing changes, and we'll give you more charity if you make any major controversial edits. 
* **If your signup method involves your real name (e.g. you used realname@gmail.com), make sure to set your username to something else [here](https://hackmd.io/settings#general) before you start editing.** Ideally, your discord username so we can discuss changes. 

## Specifics on editing

* Read hackmd's [features page](/features) to learn the formatting. Read it even if you already know Markdown; it extends it a lot with cool stuff like flowcharts and UML diagrams.
* Practice heading hygiene. Don't litter the document with h1's for minor stuff, this kills the usefulness of the hierarchy/table of contents and looks like ass.
* You can use anchor tags in this document, like if you want to link to the `Meta` heading, you can write `[visible part](#meta)`, like [this](#meta). This is particularly useful because it doesn't hard load another page/open a tab. **It also means you need to be careful with renaming headings, because there might be a link pointing to it.**

## Todos

Todos regarding this document.

If you're working on one of these todos, "lock" it by placing your name in bold next to it:
- [ ] Locking example. **X is working on this**

Don't forget to unlock. Feel free to remove someone's lock if it was placed a while ago.

Also, move completed todos to the bottom, or remove them if their completion is obvious or enough time passed.

- [ ] Someone go through the "linked discord threads" which anyone not in the SS14 discord can't access and condense the conversation we had into a readable paragraph.
- [ ] You can apparently link a note with github so that it syncs with a repository; investigate the feasibility of this
- [ ] Link this document in other places relevant to the project; discord, github, website etc. Need to ask an admin for some of these.
- [x] Create a style guide for this document (e.g. heading size, features, whatever) 
- [x]  Determine how to organize this document
    - [x] Putting everything in one place works fine; just use lots of anchor links to link to different sections
- [x]  Port over the existing google docs stuff **Swept**
