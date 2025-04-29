# Code Structure
### a.k.a Code Architecture Principles
[encapsulation]
[potentially rename this file to code architecture 2]
See (fix link) Code_Architecture.md for the overall scope but to elaborate on code structure, encapsulation, and the like:

---

## Architectural Principles

### Command Architecture + Encapsulation

(See [Basic Code Flow](Basic_Code_Flow.md) for a brief refresher on subsystems and commands.)

Taking a look at the code structure image from [Basic Code Flow](Basic_Code_Flow.md),

<img alt="Example WPILib Code Structure" src="../Assets/Example_Code_Structure.png" width="780" height="400">

note the arrows between Commands (`C11`) and Subsystems (`S1`). 

The real implication/connection that is trying to be made with this arrow is that in WPILib commands, generally, commands don't have *inherent* functionality -- ex. instead of calling `motor.set()` themselves, they call a method in the parent Subsystem that does `motor.set()` for the command. This is what's known as encapsulation!

**Encapsulation** is basically a coding paradigm in which instead of accessing things directly, we instead go through methods to do things (this should've covered by Codecademy). The benefits of encapsulation is that with encapsulation, it's super hard to change variables *accidently*. Also, limiting scope can potentially prevent some exploits that reuse variables and stuff (but we don't worry about this part in FRC). 

Encapsulation looks something like this:
Instead of this
```java
public class NumberMaker{
    public int x = 3;

    public NumberMaker(){}
}
```

where `x` can be accessed like in the following way,
```java
public class NumberTester{
    NumberMaker n = new NumberMaker();

    System.out.println(n.x); // 3
    n.x = 5; // change x to 5 (i'm also not sure if this will work so this is kinda psuedocode)
    System.out.println(n.x); // 5
}
```

encaspulation instead recommends us to wrap methods around everything like so:

```java
public class NumberMaker{
    private int x = 3; // note the private keyword

    public NumberMaker(){}
    public int getX(){
        return x;
    }

    public void changeX(int newx){
        x = newx;
    }
}
```

...and all functionality is still kept
```java
public class NumberTester{
    NumberMaker n = new NumberMaker();

    System.out.println(n.getX()); // 3
    n.changeX(5); // change x to 5 
    System.out.println(n.getX()); // 5
}
```

Back to WPILib and FRC coding, we do something super similar in between subsystems and commands. We have some sort of `Motor` or `Actuator` object in some Subsystem, and instead of letting our command access that object directly (and do things with it), we instead encapsulate that object behind some methods and have our Commands call the Subsystem's methods instead of the Subsystem's object directly.


To sum up, instead of doing this,
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

Do this instead:
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

Generally also, no objects are made in a Command. Instead, if we need some object (e.g. an `Arm` subsystem), we pass to our Command via the constructor. The main reason of doing this is that we use the same subsystem for each command, which allows for easy command scheduling and also consistent data between subsystems (e.g. calling `getEncoderTicks()` will read the same across different commands).

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

As another example, don't do this:
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

---

### Subsystem Scope

<note that commands can "take up" multiple subsystems at a time.>

Commands are needy. They need all the subsystems they interact with. As a byproduct, since each subsystem can only do one thing at a time (e.g. your elevator can't move up **and** stay still), only one command can be running on a subsystem at a single moment. As such, if for example `MoveArm` is running on the `Arm` Subsystem but you also want to run the `IntakeArm` command at the same time, your code can either decide to stick with your `MoveArm` command until it's finished (or overridden) or cancel your `MoveArm` command in favor of your `IntakeArm` command. It's pretty annoying if you want to start intaking and move you arm at the same time.

There are (afaik) two ways to solve the issue. The first is to pass a "state" between commands -- for example, if your code is running the `MoveArm` command and `IntakeArm` is scheduled to run, in this state paradigm thing, `IntakeArm` would override `MoveArm` and the arm state (e.g. ticks, angular speed, motor RPM) would translate to over to `IntakeArm` which then could both continue `MoveArm` and intake at the same time. The second (and simpler) method that our team uses to solve this issue is to simply making anything independent a Subsystem. For example, if we want the intake on the arm to be able to intake/outtake regardless of arm movement and vice versa, instead of making one subsystem (`ArmIntake.java`), we would make two (`Arm.java` + `Intake.java`) which would then allow us to run different commands on the subsystems at the same time.

