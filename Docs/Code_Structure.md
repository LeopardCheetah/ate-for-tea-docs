# Code Structure    
### (e.g. how does our code run?)   

In one picture, this is how our code structure looks:

<img alt="Example WPILib Code Structure (MS Paint)" src="../Assets/Example_Code_Structure.png" width="780" height="400">

In multiple words, our code structure uses commands and subsystems to try and mimic the real world robot. 

## Basics

When opening any WPILib project, the basic layout looks a little like this:

<img alt="Example WPILib Code Structure (VSCode)" src="../Assets/Example_WPILib_Code_Structure.png" width="320" height="422">

where almost anything above the `src/main` folder or below the `vendordeps` folder can be safely ignored[^1] (these are usually simulation-related folders or other small folders that help VSCode/WPILib but don't matter too much). 

Taking stock at what we have, in the `vendordeps` folder, we have the vendor dependencies for WPILib, which can be thought of as imported classes for motor controller or other functionality. Instead of coding a motor from scratch, we can use the motor controller built-in to the motor (like with a Falcon 500 motor and the TalonFX controller) or the external motor controller (like with a SparkMax controller for a Neo motor).

Above that folder, we have the `src/main` folder, which is where our code usually lives. The folder `src/main/deploy` is auto-generated and we don't really worry about that, so essentially everything of value is in the `src/main/java/frc/robot` folder.

---

## Code Architecture

As mentioned previously, our code uses commands and subsystems to try to mimic the real world robot. What this translates to structure-wise is this:    
- **Subsystems** can be thought of as physical parts of the robot. Example subsystems would be the `Arm` Subsystem, the `SwerveDrive` Subsystem, the `Intake` Subsystem, and so on. Also, note that **subsystems** are not executable/runnable commands and have no inherent functionality -- rather, **subsystems** act as interfaces between the coammands we run (in the `commands` folder) and the actual robot.     
- **Commands** can be thought of as the actions a robot takes. For example, an `Arm` Subsystem might have the `MoveArmUp` command, the `TrapezoidalMoveArm` command, the `ArmScorePiece` command, and so on. Commands are what the code calls if it wants something to be done.     
It is worth noting that each Subsystem can only run one command at a time -- for example, if the `Elevator` subsystem is running the `MoveUp` command and hasn't finished, without proper overriding, the `MoveDown` command or any other elevator command would not be able to run until the `MoveUp` command was done executing.          
- `RobotContainer.java` can be thought of as the final assembly of the robot. In the same way that Fabrication has to assemble all the subsystems -- the `Arm`, the `Elevator`, the `SwerveDrive` (chassis), etc. -- into one robot, `RobotContainer.java` serves as the main assembly for the code and is where all the `Subsystems` and `Commands` are actually *initalized* and bound to joysticks.[^2]

---

## Execution Structure/Logic (Approximate)

Hijacking the opening picture, our code generally runs like this:

<img alt="Example WPILib Code Structure (MS Paint)" src="../Assets/Example_Code_Structure.png" width="780" height="400">

We start in the top left with the box with an "M" that represents `Main.java`. Like most java programs, this is the first and only java file that is called to run, and this happens when the robot first connects to the RIO[^3]. If we take a look inside [`Main.java`](https://github.com/Aragon-Robotics-Team/frc-2025/blob/main/src/main/java/frc/robot/Main.java) however, nothing interesting happens -- `Main.java` makes a robot base and essentially runs whatever's in `Robot.java` (thus the arrow from M to R in the picture). 

`Robot.java` is not as boring but also doesn't give much insight. Primarily, `Robot.java` is used as a way for our code to communicate with the NI Driver Station Application -- for example, the `Autonomous Periodic` method is what's called when we are enabled and in the Autonomous Mode/Tab in our Driver Station.     
Taking a look at the code in [`Robot.java`](https://github.com/Aragon-Robotics-Team/frc-2025/blob/main/src/main/java/frc/robot/Robot.java), it's not too interesting -- there's some logging initialization and some basic scheduling. Moreover, `Robot.java` initalizes a `RobotContainer` object and basically runs the code in `RobotContainer.java` (the "RC" box in the top right in the picture above).

`RobotContainer.java`, like mentioned above, is then sorta the assembly of our code -- where all our code comes together.    
Broadly, when initalized, [`RobotContainer.java`](https://github.com/Aragon-Robotics-Team/frc-2025/blob/main/src/main/java/frc/robot/RobotContainer.java) first initializes all the subsystems, then initalizes all the commands[^4], and finally binds commands to joystick triggers/buttons/axes. Note that each commands needs a subsystem as an argument in its constructor which is why there are arrows from each Subsystem (S1, S2, S3) to the appropriate command (C11, C12, ...).



---
[^1]: For now. Everything is important to some extent but for a first overview none of it is consequential.
[^2]: The constants folder just has files with constants in it (e.g. `DriveConstants.java`, `ArmConstants.java`). That's just to make things neater so not all the constants are in a massive `Constants.java` folder.
[^3]: Or whatever the new 2029 robot controller is gonna be called. Also I'm not 100% accurate on this but I'm pretty sure this is true.
[^4]: This is an oversimplification but is mostly true.