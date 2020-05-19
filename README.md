# SpectatorDrones

An Unreal Engine plugin providing a system of camera drones for spectating in games with a focus on camera composition. The drones have the ability to align itself to put different subjects in visual compositions similar to Rule of Thirds.

## Getting Started

### Shorthands

* POI - Point of interest. Points in the 3D space which are relevant for the drone system.
* BP - blueprint. Visual scripts in Unreal Engine.

### Prerequisites

to utilize this Unreal Engine plugin, you need to have installed Unreal Engine.

(Download link: https://www.unrealengine.com/en-US/get-now)

#### Limitations

As the plugin is now, it works in 3d projects where there is sufficient open space above the playing area. Due to not having very sophisticated methods for pathfinding and collision avoidance, the drones  will not work well in cluttered areas. 

### Installing

Locate the root directory of your project in your explorer, and enter the Plugins folder. If it does not exist, make a new folder named "Plugins" in the root directory. clone this project within this folder: 

```
git clone https://github.com/Hans-Jeiger/SpectatorDrones.git
```

The plugin will now be available in your Unreal Engine project next time you open it. You can find it by clicking on the folder icon in your content browser and locating drone Content:

![How to locate the plugin within your project](https://github.com/Hans-Jeiger/SpectatorDrones/blob/master/rm_files/readme%20guide%20image.png?raw=true)

If it has not appeared by now, post an issue here and we will get back to you. 
It might appear if you create a new "Content Only" plugin in your project while in the UE4 editor.

### Simple implementation

This is a guide to implementing the drone system as it is straight out of the box. 
A more detailed guide to how you can customize the plugin for your own project is provided further down.

In your project, place a BP_DroneMaster in your world. This location is where the drones will spawn from.

Open the Blueprint of the actor the player controls. Within the Components tab, place a BP_POI_Subject actor component.

Now when you start playing in the project, there will spawn a drone which will film your controlled character.

#### Switching cameras between the drone and the controlled character

This is a very simple example method to test the drone and check out its view. 

In your level blueprint, make an event looking like this:

![How to switch between cameras - simple](https://github.com/Hans-Jeiger/SpectatorDrones/blob/master/rm_files/readme%20level%20blueprint%20example.png?raw=true)

## Customization

### POI - Point of interest

The drone system works by assigning POI actor components to the blueprints of actors that are relevant for filming. the POI components contain information about its actors location.

The POI system is built as follows:

![POI system polymorphism chart](https://github.com/Hans-Jeiger/SpectatorDrones/blob/master/rm_files/POI%20chart.PNG?raw=true)

The Parent component is the parent of all the POI components you will be using.

If you have an actor you want to be filmed by the drones, make the considerations:
* Does the actor move around the map or not? 
  * If it moves around, you should assign a BP_POI_Dynamic to it, as it updates its location each tick.
  * If it does not, you should assign a BP_POI_Static, as it only updates location at Begin Play.
* Will the actor be the primary focus for filming?
  * If it is, it should be assigned a BP_POI_Subject component, as this can keep an updated list of other POIs relative to the actor which are relevant to keep in frame.
* Does the POI need specific variables / functions to add functionality?
  * If so, you can create a child actor component of the appropriate POI component and add any functionality you need. When doing this you need to make some edits in the Drone Master, which is explained below in **Making a custom custom POI**.
  
### Drone Master

BP_DroneMaster is an actor that keeps control of all drones and POIs in the world. One of its main tasks is to provide all  Subject POIs with arrays of other POIs that need to be considered in relation of the subject. In this version, this is done by just checking the distance between the subject POIs and assigning them to each others arrays when they are within a given radius.
 
This function is meant to be customizable to fit your project. some considerations to make when customizing it:
* Different criteria to prioritize different POIs. (for example base it on distance or a calculated value)
* What kind of subject POI are you prioritizing for? (If you have made several different children of BP_POI_Subject)
* Make sure to remove the POI from a subjects array when it does not fulfill the criteria for prioritization anymore.

The Drone Master also controls how many drones currently exist. You can spawn them with the SpawnDrone function, and destroy them with the DestroyDrone function.

#### Making a custom POI

When you need to make a custom POI, you right click the appropriate POI blueprint and choose *"Create child blueprint class"*. You can fill this in with any variables and functionality you need. However, you need to make some changes in the BP_DroneMaster blueprint:

1. Make a new variable, and make it an array of the POI actor component you created.
2. In the Event Graph, in *"Event POI Created"*, add a new pin to the *"Switch on String"* node. the pin name must be *"**\*Name_of_your_POI_blueprint\*_C**"*. from this pin, you cast the POI variable from the event node to your POI and add it to your array from step 1.
3. In *"Event POI Destroyed"*, add a new pin to the *"Swtch on String"* node, and name the pin the same as in step 2. Cast the POI from the event node to your POI and remove it from your array from step 1.

### Drone Controller

BP_Drone_Controller is an AI Controller controlling the drone. When the drone is assigned a Subject POI it will fly around the subject, filming it. The drone also has a calculated midpoint, which the drone will try to keep within its frame. This midpoint is calculated from the other POIs which the Drone Master has deemed relevant for the Subject POI. It works as follows:

![Rule of thirds grid](https://github.com/Hans-Jeiger/SpectatorDrones/blob/master/rm_files/3partsGrid.jpg?raw=true)

This represents the camera view of the drone. In this example, we utilize a 3x3 grid to achieve a Rule of Thirds composition. The drone will move to keep the Subject POI along the green line and the calculated midpoint within the blue zone. The Subject POI and the midpoint will also be kept on opposite sides of the orange middle line with equal distance to the line. some examples of how the view can look:

![view example](https://github.com/Hans-Jeiger/SpectatorDrones/blob/master/rm_files/gridExamples.JPG?raw=true)


Inside the Drone Controller blueprint, there are two functions that should be customized for each project: *Calculate Midpoint of POIs* and *Calculate View Value*.

#### Calculate Midpoint of POIs

This function calculates the midpoint which the drone will attempt to keep in frame (described over). As it is now, it calculates the midpoint between all the Subject POIs prioritized by the drone's assigned POI. If it has no Subject POIs prioritized, it sets the midpoint a distance in front of the assigned POI. 

Some considerations to make when customizing this function:

* Do you want the drone to orient towards a single actor at a time?
  * You can set the midpoint to the location of that actor's POI.
* Do you want to try to capture many actors at the same time?
  * Calculate the midpoint of all the actor's POIs.
* Do you want specific POIs to affect the midpoint differently?
  * Make different macros / functions for calculating how much the POI pulls the midpoint to itself.

#### Calculate View Value

This function calculates a View Value based on which POIs the drone is currently filming. If you have several drones in the same area, you can use this to find out which drone currently has the "best view" (as in, which drone has the highest value).

Some considerations to make when customizing this function:

* Do you only use one drone at a time?
* Will different types of POIs influence the View Value differently?
  * You can set the Value variable of the POI to different values and base their influence on the View Value of the drone based on this. 
* Will the POIs not affect the View Value when they are obscured?
  * You can line trace to them and exclude them from the calculation if the line trace is true.

