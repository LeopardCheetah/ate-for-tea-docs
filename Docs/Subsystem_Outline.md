(In the interest of making you guys work out the details and actually code, I am not going to flesh out everything exactly here).   

# Subsystem Outline
### aka How to Code A Subsystem

Let's make a subsystem! This example will make some kind of arm subsystem but most subsystems are pretty similar.       
Note that this is an intro-level doc.


## Preliminaries

Before doing any actual coding, it's good to know:    
1. what the subsystem you're coding *does*     
2. what sensors/actuators are related to your subsystem (neo 550 connected to a sparkmax? limit switch? etc.)

Also, at this step, the brainstorming step basically, you should scope out what you want to be part of your subsystem; remember that subsystems should correspond to physical parts of the robot!

As an example, take a generic `Arm` Subsystem. First, to define scope, we must figure out what the Arm as a whole can do -- for example, if our arm has a telescoping elevator on it connected to a differential 2 degree of freedom (dof) wrist that is meant to operate independently of our arm (see 2910 2025), it would make sense to limit the `Arm` subsystem to really only be the motors controlling the arm. As another example, if the hypothetical arm has an intake attached to it but also some sensor to detect april tags on the arm, it may help to include that april tag sensor in our `Arm` Subsystem and delegate the rest to a separate `Intake` Subsystem. Or it might not. It all depends on use case.        
For this example `Arm`, I'll assume there's some sort of `Encoder` tracking where the arm is, a rev motor (controlled by a `SparkMax`) to move the arm, and the arm is simply supposed to move from position to position.

---

## Code Structure

Take a look at a Subsystem and more specifically, its code structure.


<img alt="Example Indexer Code Structure" src="../Assets/Example_Subsystem_Code.png" width="842" height="600">

In the screenshot above of a completed subsystem (in this case the `Indexer` Subsystem from the 2025 robot code), notice the overall structure: there's some imports at the top (in red), some component definitions in green, a constructor (light blue), some methods (in orange), and the `periodic()` function which is usually only used for logging code (dark blue).[^1] Let's break down each piece and how you would implement it in the example arm subsystem.

### Imports

Imports are usually auto-generated when you press `Tab` and autocomplete a line -- as such, there's not much really to say besides the fact that if you have many imports, organizing them is important.

### Components

This is one of the juicy parts of the Subsystem. The components section (right below the class definition and above the constructor) is where all the motor/sensor/config variables that the subsystem uses are initialized and assigned values. For example, if you have some sort of encoder attached to your subsystem, you'd add some code here to make an encoder.

As an example, for our sample arm code, the instance variables in the 'component section' could look something like this[^2]:
```java
private SparkMax m_someMotor = new SparkMax(0, MotorType.kBrushless);
private Encoder encoder = new Encoder(0);
```

### Constructor

Usually nothing happens in the constructor. Note that the constructor is also usually only called **once** (usually you don't use two `Arm` objects in your code), so the constructor is really used to set up and bind configurations.

### Methods

This is the other juicy part of the subsystem. See [Code Structure](Code_Structure.md)[^3] for a detailed overview of how subsystems and commands interact but the tl;dr is that subsystems should not have any inherent functionality and should instead provide ways to function (methods) that commands then use to do tihngs.     
For example, returning to our arm subsystem, let's say we want our arm to move. Since the command is the thing that actually dictates how fast our arm should move and maybe where it should go to, we have to write a generic function that can make the arm move at any speed, so we'll make a `setSpeed` method defined as follows:
```java
public setSpeed(double speed){
    m_someMotor.set(speed);
}
```
and we're done. Nothing fancy, nothing complicated, just a method that can now allow future Commands to make our motor spin at a certain speed.[^4]

As an exercise, try writing a method that returns the speed of the motor in our example `Arm` subsystem.

Note also that how many and what methods you make will be dicated by how many components (motors/sensors) you have and what you want to do with that specific Subsystem. For example, in our screenshotted `Indexer` subsystem which is just an intake basically, there's no need for any position or any sensing -- we literally just need to intake and outtake which is why there's only one method in that subsystem.

### periodic()

Eh. See Footnote 1[^1] if you want some information.


---

## Miscellaneous

See [Code Structure](Code_Structure.md) and Footnote 4[^4] on some more information about why exactly things are the way they are in Subsystems. Also, since only one command can run on one subsystem at one time, if you want things to move independently (e.g. you have a 2 dof elevator or something), it's best to make a separate subsystem for each independent physical mechanism.




----
footnotes/todos:

[^1]: The periodic function is set to run every 20ms, so any real function check or whatever put there for any subsystem is likely going to continue indefinitely once the subsystem is made. Usually, the motors/whatevers in subsystems are controlled by Commands (e.g. `SpinOuttake.java`) so generally no real actionable code is written in periodic. 
[^2]: This code isn't validated. Might be incorrect syntactically but it gets the idea across.
[^3]: File does not currently exist but will be made to explain how this encapsulation idea works and how subsystems/commands are supposed to interact.
[^4]: Of course, we could always make all the methods do the heavy lifting; for example, what if we made a command called `MoveToPosition` that did a PID loop on our arm angle and motor speed until it reached the desired angle? While in theory this would work and be efficient, in practice, we relegate that task to a command and subsystems should instead have minimal interfacing and functionality. See [Code Structure](Code_Structure.md) for more details (if I remember to write this doc).