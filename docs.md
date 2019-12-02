---
title: SS14 Knowledgebase
description: Space Station 14 knowledgebase. Mostly game design, general thoughts, and curated information.
tags: ss14, knowledge, game design
breaks: false
# Don't touch the YAML stuff.
---

# SS14 knowledgebase

![](https://i.imgur.com/yvy8gCd.png)

## What is this?

[Space Station 14](https://spacestation14.io/about/)'s knowledgebase and prototyping space for developing and keeping track of content such as game design, curated information, and general thoughts.

If you're new to ss14, you can check out the [website & blog](https://spacestation14.io/about/), [discord](https://discord.gg/XrVdCvM), or [subreddit](https://old.reddit.com/r/ss14/) to get familiar with the project before reading on.

### Reading this document

This document is more like a dictionary than an essay, so the content isn't arranged or ordered particularly well. Also, someone might be editing the content while you're reading it, leading to totally broken sentences. For this reason, feel free to use the table of contents at the top right to skip between sections.

Since anyone can edit this thing without even logging in, don't go crying to reddit/4chan about something you see on here until you can confirm that the actual developers agree with it.

### Adding to this document

Stuff that doesn't fit well for the [discord](https://discord.gg/XrVdCvM) or [github issues](https://github.com/space-wizards/space-station-14/issues) can go in here. So anything from collaborative real-time prototyping of a new feature to an extensive design document is fine. If you plan on doing anything more than minor editing, [please read the primer on contributing first](#Meta).

# Gameplay Design 

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

[#410](https://github.com/space-wizards/space-station-14/issues/410) proposes the ability to cross-link wires in the interface, but how this would concretely work has not really been thought out well yet.

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

## Guns (By brndd)

### Preface

Detailed semi-realistic handling is underrated in video games, and SS1314 is the kind of game where that kind of thing is right at home IMO. Most SS13 codebases already have this to some degree, but it would be a wasted opportunity to not expand on it further with this reimplementation.

As a caveat it should be said that I am not a gun nut, though I would consider myself generally informed on the basic principles of this stuff. If something here is wrong, especially in a way that makes a gameplay difference, please do point it out. I'm also assuming the reader knows what the basic parts of a gun generally are; the core concepts here are the magazine, the bolt, the chamber, and the trigger.

### Projectile Weapons

Projectile weapons should work like real-life guns do. By this I mean not taking typical video game shortcuts like not simulating the chamber, or treating magazines as a simple two-pile system of "ammo in the gun vs. ammo in reserve".

However, going too deep with the simulation will make things confusing for players, so some parts will have to be simplified. The goal here is to have enough detail in the simulation that you feel like a real tough and cool action guy while handling a gun in-game, but don't need extensive knowledge of firearms to operate them.

For simplicity's sake, I think it makes sense to treat all weapons as closed-bolt weapons. Open-bolt weapons have some differences with the way rounds are chambered, but I don't think adding this distinction wouldn't provide enough gameplay value to make up for the added complexity.

#### Most guns

Most guns have enough in common that they can be covered by generic, shared logic. This is an attempt at detailing this generic logic. This should cover things like pistols, assault rifles, submachine guns, shotguns of all kinds, bolt-action rifles, single-shot weapons like flare guns or single-shot grenade launchers... as well as weapons with both detachable and internal magazines.

Please let me know if you can think of situations where this logic fails.

* The state of the chamber should be simulated in a realistic fashion. Inserting a magazine does not chamber a round, and therefore pulling the trigger will not fire the weapon. A round is only chambered when the action is cycled by pulling the charging handle (on a rifle) or racking the slide (on a pistol).
    * It should be possible to manually insert rounds into the chamber one at a time if the gun does not have a magazine. Chambering a round this way is only possible if the bolt is open (see next point).

* The gun's action should be simulated superficially. We don't care about every single part inside, but we care about what those parts do. In practice this means we care about the state of the bolt. The state of the firing pin/hammer does not need to be simulated (in other words, pulling the trigger will always fire the round if it can be fired).
    * The action can be cycled by the player at will, regardless of the current state of the gun (how will be detailed in the Controls section).
    * Cycling the action will:
        * Eject the cartridge currently in the chamber, regardless of whether it has been fired or not. The cartridge will in most cases be dropped on the floor somewhere nearby.
        * Move a cartridge from the magazine into the chamber.
        * Close the bolt, making the gun ready to be fired.
    *    For semi-automatic weapons, firing the weapon will automatically cycle the action. For manual weapons the action must be cycled by the player after every shot. This should be the only functional difference between the two types, allowing basically all of the code to be shared between them.
    *    The bolt will automatically lock open when the magazine is empty, ie. when it fails to chamber a round. When the gun is in this state, the player manually cycling the action will toggle the bolt between its open state and closed state. For simplicity's sake manually locking the bolt open on an automatic gun (a feature on some real firearms) is not possible; it will only lock open when the gun is empty.
            * Bolt-action guns, such as Mosins, are an exception to this; for them the bolt should always toggle between an open state and a closed state. This is because the bolt must be open in order to load the internal magazine of these guns (either one round at a time or via a stripper clip). This will also serve to give bolt-action guns something of an unique feel.
* Guns need a fire selector, ie. the ability to toggle the gun between safety, semi-auto, full-auto, burst-fire etc., whatever is applicable for the gun in question.
* The magazine should be modeled realistically. We already mostly have this. Some weapons have detachable magazines (eg. C-20r), while others have internal magazines (eg. pump-action shotguns). The primary functional difference between the two is rather obviously the ability to detach the magazine.
    * Some guns will allow you to load rounds into the magazine without detaching the magazine, while others will not. In general weapons with internal magazines allow reloading the magazine without detaching it (for obvious reasons), while those with detachable magazines do not. However, to catch edge cases this should be a separate flag; someone might want to make a gun with a detachable magazine that can reloaded without detaching it, or alternatively a gun with an internal magazine that cannot be reloaded without detaching it (ie. cannot be reloaded).
    * Magazines need to remember and respect the order rounds are loaded into them, to enable eg. alternating beanbag/stun shell shotguns, or assault rifles with a flare round every 4 rounds.

#### Outliers and how to (maybe) deal with them.
Not all guns fit nicely into the generic mould suggested above. Here are some examples that may exist and how they can be dealt with.

##### Revolvers
* Could be presented by a semi-automatic weapon with an internal 6-round (or however big the cylinder is) magazine. If this is done, they will need some snowflake logic to keep empty cartridges inside of the gun until the magazine is reloaded.
* However, it would probably be better to just implement them as their own subclass of weapon, because revolvers are very common and everyone knows how they're supposed to work. If this is done, it should be possible to manually rotate the cylinder in order to enable eg. Russian roulette (only one round in the cylinder, cylinder spun to a random position) and cowboy loads (one chamber in the cylinder is empty and the hammer is resting on this chamber to avoid bump-firing the gun; no practical advantages in the context of spess, but it's cool).
* It might be cool to simulate double-action/single-action on these. Cycling the action would cock/uncock the hammer. If the hammer is cocked when you pull the trigger, you'll immediately fire. If the hammer is not cocked, on a double-action revolver you'll fire after a small delay (to simulate the much heavier trigger pull of a double-action system), and on a single-action revolver you won't fire.
##### Caseless ammunition
* The Lawgiver uses this in SS13, I think. Could simply be an ammunition subtype that has no empty casing defined. This means that when a round is fired, no empty casing is ejected. However, if the action is cycled with an unfired cartridge in the chamber, the cartridge would naturally be ejected intact.
##### Belt-fed guns
* These can probably just be represented as regular magazine-using guns except the magazines are ammunition boxes. This is common in video games already (see: M249 in CS:GO), and the ability to splice belts together for continuous machine gun fire is probably not that useful in spess.
* Or extra gun porn factor belt-fed guns should eject the disintegrated links of the belt along with empty casings, but this is a pointless detail.

### Energy Weapons

I might write more about these later, but they're pretty simple. They have a power cell and a certain amount of charge is drained from it whenever the weapon is fired.

Eris has detachable power cells on energy weapons, which is cool. If we want detachable power cells, they should probably handle similarly to magazines as far as controls go. The fire selector on energy weapons would be pretty much the same as on projectile weapons: safe, stun, kill for the e-gun for instance.

### Controls

I'm not completely up to speed with the control scheme plans for SS14, so this section could do with some input.

* Cycling the action needs to be its own key on the keyboard. Players will need to be constantly hitting this key when using eg. a pump-action shotgun. The "activate item" key Z might be good for this.
* Detaching the magazine can be done in a couple of ways. One would be just clicking on the gun with an empty hand, but I don't like this because it makes it difficult to move the gun from one hand to another. Personally I prefer the way Eris does this: drag-and-drop from the gun's sprite to an empty inventory slot (or anywhere in the world) to take the magazine out and put it where you dragged it to. However, this may or may not align with the control schemes you have planned for SS14.
    * Additionally, a right-click menu option for detaching the magazine should be present for whenever the primary option is inconvenient.
* Loading ammunition into a magazine should just entail clicking on the magazine with a cartridge, a pile of cartridges, an ammo box, etc. This will automatically load all the rounds the magazine was clicked with into the magazine up to the magazine's capacity, with a delay that depends on the number of rounds being loaded.
* Loading ammunition into a gun is similarly just a matter of clicking on the gun with the ammunition in hand. The exact behaviour of this will depend a little on the gun: for shotguns the rounds are inserted into the internal magazine regardless of bolt state; for bolt-action rifles like Mosins the rounds are inserted into the internal magazine only when the bolt is open (otherwise they cannot be inserted at all); for automatic weapons like the C-20r a single round can be inserted into the chamber if the bolt is open, and the magazine must be detached in order to be reloaded.
* The fire selector could be done as a contextual button somewhere near the gun's icon in the hands GUI. There should be the possibility for an optional keybind for this.
* Holstering/unholstering a gun should have an optional keybind, unless you believe that clunkiness is a gameplay mechanic.

#### References for other games that have done this well

CEV-Eris (you know, the place we get our sprites from) has pretty good gun management.

[Receiver](https://store.steampowered.com/app/234190/Receiver/) is an indie FPS game with highly detailed gun management, and it manages to do this with a somewhat reasonable amount of controls on a standard keyboard. We don't need that amount of complexity, but I still recommend giving it a try. It'll teach you how an M1911 works, too.

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

The lobby music should be the only one that is actually a full length song, the others should all just be short jingles or ambient music. Think Breath Of The Wild just playing a few piano notes every now and then. The golden standard is Title 3.

## Ambience 
The ambient sounds/music should always exist in the world, this means that if in the general zone you hear a vent, you can track it and weld it to stop the sound.

## Audio Systems
To improve immersion there should be added the next features: A positional audio system capable of detecting the state and position of an object, modifying the sound that it plays depending in the distance, objects, density of objects between them, etc. 

Some sort of crude audio pathfinding might be interesting here.

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
> 
Cluster's idea is that things would be mostly zone based, but we would also track differentials in the zone, for example a vent pushing plasma into the zone would make a differential. 

# Misc Ops

[On actions that take time to complete](https://discordapp.com/channels/310555209753690112/311537926376783886/604445031683850240)

[On storing data in a database](https://discordapp.com/channels/310555209753690112/310555209753690112/641265298132959261)

Should we setup a Patreon?
> paypigs when

Short-form discussion doesn't really fit in this document. Better to use the discord and then later summarize in this document.


# Other work

These are some of the projects/unmet demands/hopeless wishes that don't involve code or game design.

## Naming things

The hardest problem in computer science.
If you come up with a good idea for a name, post it in the discord in the #ss14-development or #ideaguys channel or whatever, or as a comment on here.

### Space Station 14

The name for Space Station 14 was easy to come up with, but isn't very inspiring. Can you come up with a better name? Try to match as many of these criteria as possible:

* Relevant to the themes, gameplay, or community of Space Station 14.
    * By far the most important criterion. For example, `Space Station 13` has a very good name, because it makes it clear that the game is on a `Space Station`, and doomed by the `13` to end in tragic/comic destruction. This is one of the main reason `Space Station 14` doesn't really work that well as a name - yeah, it's in space (though it may not end up being coded as a station), but what does the `14` even imply to anyone who hasn't heard of ss13?
    * Try not to make it too specific. There are a lot of ss13 servers that don't take place on a Space Station, or in space at all, like World Server or Fallout 13. Mods of ss14 could end up going in very different directions.
    * Mild bonus points if the name makes it obvious that it's a remake, since this will help with tying it to SS13 and bringing over its players.
* Minimal competition for the name in domain names, search results, and mindshare.  
    * The name should be available as a domain name. It's almost mandatory for some variant of a `.com` of the name to be available. Ideally just `[x].com`, but something like `play[x].com` or `[x]game.com` is fine. Non-`.com`'s like `.net` are basically not worth it: just ask minecraft.
        * If the TLD becomes part of the name itself, that can work. For example, if the name is `remakio`, and the domain name is then `remak.io`.
    * The name should avoid same- or similarly-named competitors on search results. Especially if the competitor with a similar name is also a game or a software project or whatever. Name conflicts with unrelated businesses like sewing clubs or charity organizations aren't as much of a problem.
    * Also, if the name can be broken down into an acronym, check if anything already uses that acronym. Don't pull a TitanFall 2.
    * Try this [convenient tool for checking name availability.](https://www.namecheckr.com/)
* Short, or an abbreviation exists to make it short.
    * E.g. Space Station 13 -> SS13.
* Can actually be typed on a keyboard or pronounced.
    * Pronunciation in real life is a little less important, since no one who plays SS13 ever goes outside and talks to people anyway.

### Plasma/phoron

The thing that space station 13 is supposed to be researching. Plasma is a very annoying name, being shared with a blood type, state of matter, and sci-fi energy mumbo jumbo, and phoron sounds like moron. Can you think of something better?

See the previous discord discussions for phoron:

* [2017-05-11 long discussion with many name suggestions 
](https://discordapp.com/channels/310555209753690112/311537926376783886/312287658946854925)
* [2019-10-01 Discord discussion
](https://discordapp.com/channels/310555209753690112/623964557064273951/628512272217669633)
* [2019-10-24 Short Discord discussion
](https://discordapp.com/channels/310555209753690112/623964557064273951/636867401987784724)

# Links

A section for organizing links. Might be split apart later.

## SS13 game design inspiration

Various SS13 codebases' forums and githubs can be a good source of long-form posts on the state of the game, ideas for development, or general thoughts.

* [SS13 reddit](https://old.reddit.com/r/SS13/)
* [TGStation's forums](https://tgstation13.org/phpBB/index.php). In particular,
    * [policy discussion](https://tgstation13.org/phpBB/viewforum.php?f=33)
    * [coding feedback](https://tgstation13.org/phpBB/viewforum.php?f=10)
    * [ideas](https://tgstation13.org/phpBB/viewforum.php?f=9)
    * [coding](https://tgstation13.org/phpBB/viewforum.php?f=5)
    * some good posts:
        * [Antag rolling](https://tgstation13.org/phpBB/viewtopic.php?f=33&t=22704&sid=df4cab6df054cc4fcbc387a1f9a1e893)
        * [Tragedy of the commons](https://tgstation13.org/phpBB/viewtopic.php?f=10&t=10074)
* [Baystation's forums](https://forums.baystation12.net/)
    * [policy discussion](https://forums.baystation12.net/view/policy-discussion.67/)
    * [suggestions & feedback](https://forums.baystation12.net/view/s-f/)


---

# Meta

Regarding this document itself.

## The quick rundown

* Anyone is welcome to comment on, or edit the document. You don't need to be signed in. Make sure to read the rest of this list first though.
* You can comment by hovering over a line and hitting the speech bubble to the right of it, and you can edit by hitting the pencil button at the top right.
* Generally, try to respect existing content, space and formatting when adding stuff, unless you're actually objectively™ improving things.
    * If you want to do any major removal of content, or write a section as if it's the project's official thoughts, make sure to discuss it with the actual devs first. They're easiest to contact at the [discord](https://discord.gg/XrVdCvM). Just type in the #ss14-development channel or whatever.
* We suggest signing up for an account for editing this document. It's fast. You can use email, a github account, facebook, whatever. It'll help with attributing and discussing changes, and we'll give you more charity if you make any major controversial edits. 
    * **If your signup method involves your real name (e.g. you used realname@gmail.com), make sure to set your username to something else [here](https://hackmd.io/settings#general) before you start editing.** Ideally, your discord username so people can ping you on there.
* If you don't want people reading your stuff while you're working on it, [open a new note](https://hackmd.io/new) and then paste it here when you're done.

## Specifics on editing

* Read hackmd's [features page](/features) to learn the formatting. Read it even if you already know Markdown; it extends it a lot with cool stuff like flowcharts and UML diagrams.
* Practice heading hygiene. Don't litter the document with h1's for minor stuff, this kills the usefulness of the hierarchy/table of contents and looks like ass.
    * Also, keep top level (h1's / single #'s) heading's names short. They get truncated in the ToC if they're too long.
* You can use anchor tags in this document, like if you want to link to the `Meta` heading, you can write `[visible part](#meta)`, like [this](#meta). This is particularly useful because it doesn't hard load another page/open a tab. **It also means you need to be careful with renaming headings, because there might be a link pointing to it.**
* At the moment, we've decided to try to keep all content in the same document. Feel free to experiment with linking to other documents from this one if you want though.

## Further collab & docs organization

Asset overview:

* Discord
* Hackmd
* Github issues
* SS14 website
* Subreddit

Prospective actions:

* Since hackmd was recently introduced and sits "between" discord and github on the documentation persistence/organization scale, there is content on both of those that should be moved here.
* Plan out other knowledge tools. Would adding a proper wiki like mediawiki be worth it given the overhead of keeping track of yet another knowledge solution? Could it exist alongside hackmd or would it replace it (and hackmd would then be relegated to just rapid prototyping prior to wiki submission)?