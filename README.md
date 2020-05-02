# SpectatorDrones

An Unreal Engine plugin providing a system of camera drones for spectating in games. Made to be pleasing for the eyeballs :-)

## Getting Started

### Shorthands

POI - Point of interest. Points in the 3D space which are relevant for the drone system

### Prerequisites

to utilize this Unreal Engine plugin, you need to have installed Unreal Engine. Yes, really.

(Download link: https://www.unrealengine.com/en-US/get-now)

#### Limitations

As the plugin works now, it works in 3d projects where there is sufficient open space above the playing area. Due to not having very sophisticated methods for pathfinding and collision avoidance, the drones  will not work well in cluttered areas. 

### Installing

In the root folder of your Unreal Engine project, move to the Plugins folder. 
(If there is no Plugins folder, you can just create a folder named "Plugins".)

Within the Plugins folder, clone this repository:

```
git clone https://github.com/Hans-Jeiger/SpectatorDrones.git
```

The plugin will now be available in your Unreal Engine project next time you open it. You can find it by clicking on the folder icon in your content browser and locating drone Content:

![How to locate the plugin within your project](https://media.githubusercontent.com/media/Hans-Jeiger/SpectatorDrones/master/rm_files/readme%20guide%20image.png?token=ALCTNAHMWCXG262BPRWX2CC6VVUCM)

### Simple implementation

This is a guide to implementing the drone system as it is straight out of the box. 
A more detailed guide to how you can customize the plugin for your own project is provided further down.

In your project, place a BP_DroneMaster in your world. This location is where the drones will spawn from.

Open the Blueprint of the actor the player controls. Within the Components tab, place a BP_POI_Subject actor component.

Now when you start playing in the project, there will spawn a drone which will film your controlled character.

#### Switching cameras between the drone and the controlled character

This is a very simple example method to test the drone and check out its view. 

In your level blueprint, make an event looking like this:

![How to locate the plugin within your project](https://media.githubusercontent.com/media/Hans-Jeiger/SpectatorDrones/master/rm_files/readme%20level%20blueprint%20example.png?token=ALCTNACNVJWHMLKZFB2JD7S6VVUD2)

## Customization

The drone system works by assigning POI actor components to the blueprints of actors that are relevant for filming.

The POI system is built as follows:

![POI system polymorphism chart](https://raw.githubusercontent.com/Hans-Jeiger/SpectatorDrones/master/rm_files/POI%20chart.PNG?token=ALCTNAAN7HGAABPTAIZAROC6W2P6O)

The Parent component is the parent of all the POI components you will be using.

If you have an actor you want to be filmed by the drones, make the considerations:
* Does the actor move around the map or not? 
  * If it moves around, you should assign a BP_POI_Dynamic to it, as it updates its location each tick.
  * If it does not, you should assign a BP_POI_Static, as it only updates location at Begin Play.
* Will the actor be the primary focus for filming?
  * If it is, it should be assigned a BP_POI_Subject component, as this can keep an updated list of other POIs relative to the actor which are relevant to keep in frame.
* Does the POI need specific variables / functions to add functionality?
  * If so, you can create a child actor component of the appropriate POI component and add any functionality you need.
