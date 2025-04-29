# Code Structure
### a.k.a Code Architecture Principles
[encapsulation]
[potentially rename this file to code architecture 2]
See (fix link) Code_Architecture.md for the overall scope but to elaborate on code structure, encapsulation, and the like:

---

## Architectural Principles

### Command Architecture

See [Basic Code Flow](Basic_Code_Flow.md) for a brief refresher (todo: update this with right section header) on subsystems and commands.

#### Encapsulation

Taking a look at the code structure image from [Basic Code Flow](Basic_Code_Flow.md),

<img alt="Example WPILib Code Structure" src="../Assets/Example_Code_Structure.png" width="780" height="400">

note the arrows between Commands (`C11`) and Subsystems (`S1`). 

The real implication/connection that is trying to be made with this arrow is that in WPILib commands, generally, commands don't have *inherent* functionality -- ex. instead of calling `motor.set()` themselves, they call a method in the parent Subsystem that does `motor.set()` for the command. This is what's known as encapsulation!

So don't do this:
```java
public class SomeCommand extends Command{
	private SomeSubsystem m_subsystem;
	// ...
	// ...
	@Override
	public execute(){
		m_subsystem.getMotor().set(1.0); // sets motor to max speed	
	}
}
```

And do this instead:
```java
public class SomeCommand extends Command{
	private SomeSubsystem m_subsystem;
	// ...
	// ...
	@Override
	public execute(){
		m_subsystem.setSpeed(1.0); // sets motor to max speed	
	}
}
```
and implement `setSpeed` in `SomeSubsystem.java` like so:

```java
public class SomeSubsystem extends SubsystemBase{
	private Motor m_motor; 
	// ...
	// ... 
	public void setSpeed(double speed){
		m_motor.set(speed); 
		// functionality is implemented HERE instead
	}
}
```

--- 
<note that commands can "take up" multiple subsystems at a time.>

Generally, no objects are made in each command in WPILib. What this means for example is that (like mentioned in Code_Architecture.md), instead of initializing a subsystem, we merely declare it in our code and pass in one through the constructor. 

Essentially, instead of this:
```java
public class GenericCommand extends Command{
	// note: declaring == creating the variable 
	// instantiating == setting the variable to something (the first time)
	
	// here we both declare and instantiate the subsystem
	// this is bad!
	private Subsystem subsystem = new Subsystem();
	public GenericCommand() {}
}
```

do this: (note: check if the `extends` logic is valid)
```java
public class GenericCommand extends Command{
	private Subsystem subsystem; // only declaration, no initialization
	public GenericCommand(Subsystem subsystem) {
		this.subsystem = subsystem;
	}
}
```

---





- The way commands are created, instead of making a new subsystem for each command (see code snippet 1), we pass in a subsystem (through the constructor) for the command to use and interact with (see code snippet 2). The main advantages of using the same subsystem for each command is that command scheduling is done almost automatically for us, and data is consistent between subsystems (e.g. calling `getEncoderTicks()` will read the same).
Don't do this:
```java
// this is in SomeArmCommand.java

// ...
public class SomeArmCommand extends Command {
    private Arm m_arm;
    // ...
    public ArcadeArm() {
        m_arm = new Arm();
        // ...
    }
    // ...
}
```

Do this instead:
```java
// this is in SomeArmCommand.java

// ...
public class SomeArmCommand extends Command {
    private Arm m_arm;
    // ...
    public ArcadeArm(Arm arm) {
        m_arm = arm;
        // ...
    }
    // ...
}
```

Note that at a deeper level, Subsystems should be thought as independent mechanisms of the robot. For example, in 2025, our robot had an intake-like system on top of our arm mechanism. Since these two components needed to be independent (e.g. intake should be able to intake/outtake regardless of arm movement and vice versa), we had two different subsystems for those two mechanisms: `Arm.java` and `EndEffector.java`. 


