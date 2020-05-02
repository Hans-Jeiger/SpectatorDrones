# SpectatorDrones

An Unreal Engine plugin providing a system of camera drones for spectating in games. Made to be pleasing for the eyeballs :-)

## Getting Started

### Prerequisites

to utilize this Unreal Engine plugin, you need to have installed Unreal Engine.

(Download link: https://www.unrealengine.com/en-US/get-now)

### Installing

In the root folder of your Unreal Engine project, move to the Plugins folder. 
(If there is no Plugins folder, you can just create it.)

Within the Plugins folder, clone this repository:

```
git clone https://github.com/Hans-Jeiger/SpectatorDrones.git
```

The plugin will now be available in your UE project. You can find it by clicking on the folder icon in your content browser and locating drone Content:

![How to locate the plugin within your project](https://raw.githubusercontent.com/Hans-Jeiger/SpectatorDrones/master/Resources/readme%20guide%20image.png?token=ALCTNAFKSFLL5EJGS4NHZLK6WZVC4)

### Simple implementation

This is a guide to implementing the drone system as it is straight out of the box. 
A more detailed guide to how you can customize the plugin for your own project is provided further down.

In your project, place a BP_DroneMaster in your world. This location is where the drones will spawn from.

Open the Blueprint of the actor the player controls. Within the Components tab, place a BP_POI_Subject actor component.

Now when you start playing in the project, there will spawn a drone which will film your controlled character.

#### Switching cameras between the drone and the controlled character

This is a very simple example method to test the drone and check out its view. 

In your level blueprint, make an event looking like this:

![How to locate the plugin within your project](https://raw.githubusercontent.com/Hans-Jeiger/SpectatorDrones/master/Resources/readme%20level%20blueprint%20example.png?token=ALCTNABUKSDTSFT5PJY5HOC6WZ3FG)
