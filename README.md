# Negative Latency

## Origins

The term 'Negative Latency' picked up a little bit recently with the release of Google Stadia. With Stadia, the game client is really light-weight, and just needs to send controller inputs and translate video/audio feeds back from the server -- the server is the one doing all the heavy lifting. The problem with this scenario for the vast majority of gamers I'm immersed in is the problem of input latency.

Input latency is the time it takes from a button press to seeing the results of the input show on-screen. For a typical gaming setup you would expect somewhere in the neighborhood of 40ms of input latency from your mouse through the computer's bus, the operating system, the drivers, the game engine, the graphics drivers, the video card and the monitor. What frustrates this pipeline further with Stadia is the fact that there is additional processing required to convert a video to a network-sendable stream as well as there typically being a lot of physical network nodes between you and the game server - the more jumps there is, the more latency will accrue as a result.

To this end - Google developers for Stadia have coined the term 'Negative Latency' to describe a couple of in-house technologies they're experimenting with to try and mitigate the feeling of heavy-listless controller inputs. Basically they are trying to simulate the responses to your inputs locally on the client before the server even knows anything has occurred, it's got some complications to work through but I don't doubt if anyone can do it, Google can.

In this post I don't want to dwell as much on Google's advancements in tightening up their latency problem as much as what the term 'Negative Latency' has sparked in my imagination.

## It's Already In Your Games!

I'm gonna go ahead and broaden the definition of Negative Latency beyond what Google describes and have some fun with it. In my definition, 'Negative Latency' is any game mechanic where-in the game responds faster to your inputs than would have been realistic in a true simulation. Let's go through some genres:

* Fighting

> The moment you press a light-attack button your characters fist/foot is jutted out forward *as if* the character had time to wind up and push out their limb in advance of you pushing the button.

* Real-time Strategy

> The moment you place a building down, the frame is already placed down *as if* the frame was already in-development long before you desired it's placement.

* Real-time Tactics

> The moment you order new units into the battlefield, they appear at the bottom of the screen *as if* your general had anticipated their necessity well in advance of your command to send them in.

* First Person Shooter

> The moment you hit respawn, you are placed within seconds of a combat encounter with the enemy *as if* your character was marching towards the battlefield long before you took control of him.

## Snappy Actions

So my initial observation when noting that we already have some semblance of negative latency is: *Instant responses to input resonate well with digital buttons.*

Consider a subset of games that don't have instant response - VR titles. In a VR fighting game, the player himself performs wind-ups and followthroughs before making contact with the opponent, since that's what the analog nature of motion controllers asks of the player. Having an instant punch come out due to motion controls would be strange and out-of-place.

In contrast, the shiny colored buttons of a fightstick with satisfying tactile clicks and a clear distinction between inactive and activated states almost demands a parallel within the game - where the action of the player slamming their finger on a button perfectly mirrors the in-game character making contact with their opponent.

Nintendo has refined this technique of complementing the user's inputs so wel that it also extends to the user interfaces. Within their game menus every selection made has a instant chime, only then followed by satisfying yet subtle menu transitions. This creates an experience that almost sings a harmony to the player's own audible button presses. You know Nintendo is self-aware of this part of their design when snapping fingers is the catch phrase for the Switch.

## Can We Generalize?

I'm a programmer by trade, so my first response after coming to this conclusion is to try and figure out some sort of universal generalization. To this end I need to go back to my initial definition - *'Negative Latency' is any game mechanic where-in the game responds **faster** to your inputs than would have been realistic in a **true simulation***.

>Let's imagine a simple fighting game and figure out if we can come up with a rule-of-thumb.
* Start with a **true simulation**
    > * When the user hits the punch button, the character has to plant his feet, wind up his fist and push it forward before he is able to damage the opponent
    > * Time taken during this process might be ~1 second, or 60 frames

Ok, well this game's bad, if you know anything about the infamous Genesis game Shaq Fu, you know that adding build-up frames and delay before actions where in other games there was none feels backwards and terrible. Shaq Fu was nuts enough to try adding buildup animations to *jumping animations* no less. We need to keep moving forward.

* Keep a running history of the game state
    > * Every frame of character movement is recorded in-full: Current animation, animation progress, position, velocity
    > * Any previous frame can be perfectly restored and resimulated without any change to final outcome

Another strange in-between step that needs to be made --- Imagine what it takes for a drawing program to restore old changes by pressing ctrl+z [undo], the program has to essentially keep track of the state of the application before the user clicked the mouse versus afterwards. This allows us to do something akin to sorcery:

* Send button presses *into the past*
    > * When the player presses the punch button, restore the state of the game 60 frames in the past **except** the punch input is activated
* Resimulate the game forward until it reaches the current frame again
    > * Flash-forward to current frame where the punch animation has already gone through its wind-up phase and the fist is firmly in the opponents face

Alright, so we're back where we started essentially, but it's generic - The frame where the player hits the punch is the frame in which the character contacts the opponent.