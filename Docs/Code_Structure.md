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


## Code Architecture

As mentioned previously, our code uses commands and subsystems to try to mimic the real world robot. What this translates to structure-wise is this:    
- **Subsystems** can be thought of as physical parts of the robot. Example subsystems would be the `Arm` Subsystem, the `SwerveDrive` Subsystem, the `Intake` Subsystem, and so on. Also, note that **subsystems** are not executable/runnable commands and have no inherent functionality -- rather, **subsystems** act as interfaces between the coammands we run (in the `commands` folder) and the actual robot.     
- **Commands** can be thought of as the actions a robot takes. For example, an `Arm` Subsystem might have the `MoveArmUp` command, the `TrapezoidalMoveArm` command, the `ArmScorePiece` command, and so on. Commands are what the code calls if it wants something to be done.    
- `RobotContainer.java` can be thought of as the final assembly of the robot. In the same way that Fabrication has to assemble all the subsystems -- the `Arm`, the `Elevator`, the `SwerveDrive` (chassis), etc. -- into one robot, `RobotContainer.java` serves as the main assembly for the code and is where all the `Subsystems` and `Commands` are actually *initalized* and bound to joysticks.[^2]


## Execution Structure/Logic (Approximate)
Main -> Robot -> RobotContainer -> Subsystems -> Commands




---
[^1]: For now. Everything is important to some extent but for a first overview none of it is consequential.
[^2]: The constants folder just has files with constants in it (e.g. `DriveConstants.java`, `ArmConstants.java`). That's just to make things neater so not all the constants are in a massive `Constants.java` folder.