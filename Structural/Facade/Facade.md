# 🎯 Facade Design Pattern
- In complex systems, multiple classes often interact with each other in intricate ways (e.g., ClassA calls ClassB, which calls ClassC, and so on).
- You don’t want the client or user to manage these internal dependencies or understand how they work together.
- The Facade Pattern introduces a single, unified interface that hides the underlying complexity and coordinates all subsystem interactions.
- The client simply calls the Facade, and the Facade internally handles all required operations.
- This pattern follows the Principle of Least Knowledge (Law of Demeter) — classes should communicate with as few others as possible.

---

## Multiple Sub-Classes interacting with each other in a complex way having patterns etc etc
```java
// Subsystems
class PowerSupply {
    public void providePower() {
        System.out.println("Power Supply: Providing power...");
    }
}

class CoolingSystem {
    public void startFans() {
        System.out.println("Cooling System: Fans started...");
    }
}

class CPU {
    public void initialize() {
        System.out.println("CPU: Initialization started...");
    }
}

class Memory {
    public void selfTest() {
        System.out.println("Memory: Self-test passed...");
    }
}

class HardDrive {
    public void spinUp() {
        System.out.println("Hard Drive: Spinning up...");
    }
}

class BIOS {
    public void boot(CPU cpu, Memory memory) {
        System.out.println("BIOS: Booting CPU and Memory checks...");
        cpu.initialize();
        memory.selfTest();
    }
}

class OperatingSystem {
    public void load() {
        System.out.println("Operating System: Loading into memory...");
    }
}
```
## Wrapping Multiple Subsystem Classes into a Single Simplified Interface
Before: Complex Way of Accessing and Managing Multiple Classes
After (Using Facade): A Simple Unified Interface to Interact with the System
```java
// Facade
class ComputerFacade {
    private PowerSupply powerSupply = new PowerSupply();
    private CoolingSystem coolingSystem = new CoolingSystem();
    private CPU cpu = new CPU();
    private Memory memory = new Memory();
    private HardDrive hardDrive = new HardDrive();
    private BIOS bios = new BIOS();
    private OperatingSystem os = new OperatingSystem();

    public void startComputer() {
        System.out.println("----- Starting Computer -----");
        powerSupply.providePower();
        coolingSystem.startFans();
        bios.boot(cpu, memory);
        hardDrive.spinUp();
        os.load();
        System.out.println("Computer Booted Successfully!");
    }
}
```
## How client accesses
```java
// Client
public class FacadePattern {
    public static void main(String[] args) {
        ComputerFacade computer = new ComputerFacade();
        computer.startComputer();
    }
}
```
