# Design patterns

### Types
* __Creational patterns__ - provides flexibility of creating objects
* __Structural patterns__ - deal with classes are actually designed
* __Behavioral patterns__ - communication and responsibility assignment between objects

### Singleton

* One instance for app (database instance for example). 
* Incapsulated constructor. 
* Has public method for getting instance.

```java
public class Singleton {
    private static Singleton instance;
    private String data;

    private Singleton(String data) {
        this.data = data;
    }

    public static Singleton getInstance(String data) {
        if (instance == null) {
            instance = new Singleton(data);
        }
        return instance;
    }
}
```

thread safe implementation:
```java
public class Singleton {
    private static volatile Singleton instance; // when we check instance == null it can be partially initialized but not completely. so instance is not null
                                                // and another thread can return partially constructed object so the result will be incorrect and application will 
                                                // crashed. solution - use volatile keyword. All r/w operation with the object are atomic now
    private String data;

    private Singleton(String data) {
        this.data = data;
    }

    public static Singleton getInstance(String data) {
        Singleton result = instance;    // copy object link to local therad memory 
                                        // (good optimization by reducing r/w operations with volatile object)

        if (result == null) {
            synchronized(Singleton.class) { // use class monitor
                result = instance; // instance can be not null now

                if (result == null) {
                    instance = result = new Singleton(data);
                }
            }
        }
        return result;
    }
}

```

### Factory pattern
Creational design pattern. Separates product construction code from the code that uses this product.  

__basic idiom__

* factory client uses factory by calling create() method to get instance of object.  
* creating object of the specified type is incapsulated in create() method.  
* objects are instances of classes which implement the same interface which factory client is operating with.

```java
public class CarFactoryClient {
    private CarFactory carFactory;

    public Car orderCar(String carType) {
        Car createdCar = carFactory.createCar(carType);
        createdCar.prepareToCell();

        return createdCar;
    }
}

public class CarFactory {
    public final String PASSENGER_CAR = "PASSENGER_CAR";
    public final String PASSENGER_CAR = "CARGO_CAR";

    public Car createCar(String carType) {
        if (carType.equals(PASSENGER_CAR)) {
            return new PassengerCar();
        }
        else if (carType.equals(CARGO_CAR)) {
            return new CargoCar();
        }
        else {
            // another car type case will be here
        }
    }
}

//--------------------------------------------------------//

public interface Car {
    void prepareToCell();
}

public class CargoCar implements Car {
    @Override
    void prepareToCell() {
        // some implementation
    }
}

public class PassengerCar implements Car {
    @Override
    void prepareToCell() {
        // some implementation
    }
}
```

__flaws__: CarFactory class is __open for modification__ when new types of cars appears.

__solution__: implement separate instantiation classes for each product type

```java
public class CarFactoryClient {
    private CarFactory carFactory;

    public Car orderCar() {
        Car createdCar = createCar();
        createdCar.prepareToCell();

        return createdCar;
    }
    public abstract Car createCar();
}

public class CargoCarFactory extends CarFactoryClient {
    @Override
    public Car createCar() {
        return new CargoCar();
    }
}

public class CargoCarFactory extends CarFactoryClient {
    @Override
    public Car createCar() {
        return new PassengerCar();
    }
}

//--------------------------------------------------------//

public interface Car {
    void prepareToCell();
}

public class CargoCar implements Car {
    @Override
    void prepareToCell() {
        // some implementation
    }
}

public class PassengerCar implements Car {
    @Override
    void prepareToCell() {
        // some implementation
    }
}
```

__flaws__: a subtype can be added to each type of the product so create product code become open for modification.  
__example__ - PassengerCar can have two subtypes - _Reno Logan_ and _Lada Granta_.  

__solution__ - abstract factory pattern

### Abstract Factory pattern

allows to produce __families of related objects__ without specifying their concrete classes. Single factory __is not enough__.  

* define abstract factory which can produce different product types
* implement concreate factories that extends/implements abstract.factory. They produce __concrete__ products of __each type__.  
* concrete product of each type implement specified product type interface.  

```java
public interface AbstractCarFactory {   // also can be an abstract class
    CargoCar createCargoCar();          // depends on logic you want to include
    PassengerCar createPassengerCar();
}

public class ToyotaCarFactory implements AbstractCarFactory {
    @Override
    CargoCar createCargoCar() {
        return new ToyotaCargoCar();
    }
    @Override
    PassengerCar createPassengerCar() {
        return new ToyotaPassengerCar();
    }
}

public class LadaCarFactory implements AbstractCarFactory {
    @Override
    CargoCar createCargoCar() {
        return new LadaCargoCar();
    }
    @Override
    PassengerCar createPassengerCar() {
        return new LadaPassengerCar();
    }
}

// ------------------------------------- //

public interface CargoCar { // also can be an abstract class
    public void prepareToCell();
}

public interface PassengerCar { // also can be an abstract class
    public void prepareToCell();
}

// ------------------------------------ //

public class LadaCargoCar implements CargoCar {
    @Override
    public void prepareToCell() {
        // implement logic here
    }
}

public class ToyotaCargoCar implements CargoCar {
    @Override
    public void prepareToCell() {
        // implement logic here
    }
}

public class LadaPassengerCar implements PassengerCar {
    @Override
    public void prepareToCell() {
        // implement logic here
    }
}

public class ToyotaPassengerCar implements PassengerCar {
    @Override
    public void prepareToCell() {
        // implement logic here
    }
}
```

### Builder pattern

Creational pattern. Produce different types/representations of the  object using the same step by step construction process.  

Uses when object has huge number of fields. To avoid using a mostrous constructor each time we use this pattern.  

__basic idiom__
```java
public class Client {
    private int id;
    private String name;
    private String surname;
    private String pathronimic;
    private int age;
    private String citizenship;
    private String nationality;

    // using of constructor should be incapsulated. 
    // preferably, only available to Builder

    Client(int id, String name, String surname, ...) {
        this.id = id;
        this.name = name;
        this.surname = surname;
        ...
    }
}

public class ClientBuilder {
    private String name;        // same field as Client class has
    private String surname;     //
    private String pathronimic; //
    private int age;            //
    private String citizenship; //
    private String nationality; //

    public ClientBuilder() {}

    public ClientBuilder id(int id) {
        this.id = id;

        return this;
    }

    public ClientBuilder name(String name) {
        this.name = name;

        return this;
    }
    ...
    public Client build() {
        return new Client(id, name, sruname, ...);
    }
}
```

sometimes we have the same __configuration__ of building products and we want to reuse it.

__solution__: we create new class which uses our builder instance to create objects with specified configuration:

```java
public class SchemaClientCreator {
    public void buildRussianClient(ClientBuilder builder) {
        builder.citizenship("Russian").
        nationality("Russian");
    }
    public void buildBrasilianClient(ClientBuiler builder) {
        builder.citizenship("Brasilian").
        nationality("Brasilian");
    }
}
```

### Prototype pattern

Creational pattern. Delegating object cloning process to the object which will be actually cloned.  

__basic idiom__:

```java
interface Prototype {
    public Car clone();
}

public class Car implements Prototype {
    private String brand;
    private String model;
    private int speed;

    public Car() {}

    public Car(Car car) {   // copy constructor
        this.brand = car.brand;
        this.speed = car.speed;
        this.model = car.model;
    }

    @Override
    public Car clone(Car car) {
        return new Car(this);
    }
}
```

prototype pattern with hierachy:

```java
public abstract class Vehicle { // parent class with abstract clone() method
    private String brand;
    private String model;
    private int speed;

    protected Vehicle(Vehicle vehicle) {
        this.speed = vehicle.speed;
        this.model = vehicle.model;
        this.brand = vehicle.brand;
    }
    public abstract Vehicle clone(Vehicle vehicle);

    // we can copy child objects without knowing what class they 
    // actually corresponded to
    public List<Vehicle> clone(List<Vehicle> vehicleList) {
        List<Vehicle> result = new ArrayList<>();

        for (Vehicle vehicle: vehicleList) {
            result.add(vehicle.clone());
        }
        return result;
    }
}

public class Car extends Vehicle {
    private int length;

    public Car(){}
    public Car(Car car) {
        super(car);
        this.length = car.length;
    }
    @Override
    public Car clone() {
        return new Car(this);
    }
}

public class Bus extends Vehicle {
    private int seatsCount;

    public Bus(){}
    public Bus(Bus bus) {
        super(bus);
        this.seatsCount = bus.seatsCount;
    }
    @Override
    public Bus clone() {
        return new Bus(this);
    }
}

```
sometimes it is needed to store the most used prototypes in cache:

```java
public class VehicleCache {
    private Map<String, Vehicle> cache = new HashMap<>();

    public VehicleCache(List<Vehicles> prototypes) {
        ... // initial cache filling
    }
    public Vehicle getCopy(String key) {
        return cach.get(key).clone();
    }
}
```

### Chain of responsibility pattern

Behavioral pattern. Represents a chain of handlers which handle the request one by one in chain order. Each handler desides to pass request to the next handler or reject it.  

```java
interface URL {
    String getDomainName();
    String getProtocol();
    String getSubdomain();
    String getPath();
}

public abstract class Handler {
    private Handler next;
    
    public Handler setNext(Handler nextHandler) {
        this.next = nextHandler;

        return nextHandler;
    }
    public Handler next() {
        return next;
    }
    public abstract boolean handle(URL url);
}

public class ProtocolHandler {
    private String requiredProtocol;

    @Override
    public boolean handle(URL url) {
        return url.getProtocol().equals(requiredProtocol);
    }
}

public class SubdomainHandler {
    private static final String requiredDomain = "www";

    @Override
    public boolean handle(URL url) {
        return requiredDomain.equals(url.getSubdomain());
    }
}

public class PathHandler {
    @Override
    public boolean handle(URL url) {    // handle that path is not an empty string
        return !url.equals(url.getPath())
    }
}

public class DomainHandler {
    public static final String requiredDomain = "com";

    @Override
    public boolean handle(URL url) {
        return url.getDomainName().split(" ")[1] == requiredDomain;
    }
}

// ---------------------------------------- //

public class ValidateURLservice {
    public boolean validate(URL url) {
        Handler curHandler = new ProtocolHandler();
        curHandler.setNext(new SubdomainHandler()).setNext(new DomainHandler()).setNext(new PathHandler());

        while (curHandler != null) {
            if(!curHandler.handle()) {return false;};
            curHandler = curHandler.next();
        }
        return true;
    }
}
```
Use this pattern when you need to execute handlers in the particular order. Handler order can be changed dynamically.  

### Command pattern

Behavioral design pattern. Turns beahvior into a separate object, which incapsulates all necessary info about performing an action.  

roles:
* __Invoker__ - initialize the request for command execution.  
* __Receiver__ - does actual work of commands execution.  
* __Command__ - abstract command.  
* __ConcreteCommand__ - concrete implementation of the command.  
* __Client__ - configuring commands. passes all the request parameters (including a receiver instance) to the command constructor.

```java
public interface Command {      // -- abstract command -- //
    void execute();
}

public class ArtilleryFireCommand implements Command {  // -- concrete command -- //
    private List<ArtilleryBattery> batteries;

    public ArtilleryFireCommand(List<ArtilleryBattery> batteries) {
        this.batteries = batteries;
    }

    @Override
    public void execute() {
        for (ArtilleryBattery battery: batteries) {
            battery.fire();
        }
    }
}

public class InfantryMarchCommand implements Command {  // -- concrete command -- //
    private List<InfantryPlatoon> infantryPlatoons;

    public InfantryMarchCommand(List<InfantryPlatoon> infantryPlatoons) {
        this.infantryPlatoons = infantryPlatoons;
    }

    @Override
    public void execute() {
        for (InfantryPlatoon platoon: infantryPlattons) {
            if (platoon.getMoral() > platoon.getMinMoral()) {
                platoon.march();
            }
        }
    }
}

public class Army {     // -- Invoker -- //
    private List<ArtilleryBattery> batteries;
    private List<InfantryPlatoon> infantryPlatoons;
    private Command currentCommand; // we can also implement queue/list of commands 

    public void setCurrentCommand(Command command) {
        this.currentCommand = command;
    }

    public void executeCommand() {
        currentCommand.execute();
    }
}

public class ArmyCommander {        // -- client -- //
    private Army army;

    public void offensive() {
        army.setCurrentCommand(new ArtilleryFireCommand(army.batteries()));
        army.executeCommand();
        army.setCurrentCommand(new InfantryMarchCommand(army.infantryPlatoons()));
        army.executeCommand();
    }

    public void defense() {
        army.setCurrentCommand(new ArtilleryFireCommand(army.batteries()));
        army.executeCommand();  // double artilery fire
        army.executeCommand();  //
    }
}

public class ArtilleryBattery {     // -- receiver -- //
    void fire() {
        ... // some buisness logic
    }
}

public class InfantryPlatoon {      // -- receiver -- //
    private int moral;
    private static final int MIN_MORAL = 10;
    
    public int getMoral() {return moral;}

    public static int getMinMoral() {return MIN_MORAL;}
    
    void march() {
        ... // some buisness logic
    }
}

```

Commands can be queued, serialized and passed through the network.

### Template pattern

Behavioral design pattern. Defines skeleton of an algorithm in a superclass. Lets subclasses override specific steps of the algorithm without changing the structure.  

```java
public abstract class Army {
    public abstract void reconnaissance();
    public abstract void preliminaryBombardment();
    public abstract void droneSupport();
    public abstract void storming();

    public void attack() {  // template method
        reconnaissance();
        preliminaryBombardment();
        droneSupport();
        storming();
    }
}

public class RussianArmy {
    @Override
    public void reconnaissance() {
        // specific subclass implementation
    }

    @Override
    public void preliminaryBombardment() {
        // specific subclass implementation
    }

    @Override
    public void droneSupport() {
        // specific subclass implementation
    }

    @Override
    public void storming() {
        // specific subclass implementation
    }
}

public class USArmy {
    @Override
    public void reconnaissance() {
        // specific subclass implementation
    }

    @Override
    public void preliminaryBombardment() {
        // specific subclass implementation
    }

    @Override
    public void droneSupport() {
        // specific subclass implementation
    }

    @Override
    public void storming() {
        // specific subclass implementation
    }
}
```

Algorithm steps with similar implementation are moved into the superclass.  

### Mediator pattern

Behavioral design pattern. Defines the object that encapsulates how other objects interact between each other. Reducing dependences between objects.  

__roles__:
* __Mediator interface__ - abstract mediator, need if we want several implementations of it. Defines main method which component will use for communication.  
* __Concrete mediator__ - concrete implementation of mediator interface. Implements components interraction.  
* __Abstract component__ - we can have several components classes, so define abstract component class which will be used by a mediator.   
* __Concrete component__ - concrete implementation of abstract component class.  

```java
public interface SupportMediator { // Mediators can be different
    public void getSupport(Supporter asker);
}

public abstract class Supporter {   // abstract component class
    private SupportMediator mediator;

    public void askForSupport() {
        mediator.getSupport(this);
    }
    public abstract boolean support();   // true if can support
    public abstract boolean canSupport();
    public abstract String getName();
}

public class ConcreateSupportMediator implements SupportMediator {  // concreate implementation of mediator
    private List<Supporter> supporters;

    @Override
    public void getSupport(Supporter asker) {
        for (Supporter supporter: supporters) {
            if (supporter.canSupport() && !supporter.getName.equals(asker.getName())) {
                supporter.support();
            }
        }
    }
}

public class Artillery extends Supporter {  // concreate component class
    private int ammunition;
    private static final int MIN_AMMUNITON_SUPPORT_COUNT = 100;

    private void shelling() {
        ...// some implementation
    }

    @Override
    public boolean support() {
        shelling();
    }
    
    @Override
    public boolean canSuport() {
        return ammunition >= MIN_AMMUNITON_SUPPORT_COUNT;
    }
    
    @Override
    public String getName() {
        return "Artillery";
    }
}

public class Infantry extends Supporter {   // concreate component class
    private int moral;
    private int curStamina;
    
    private static final int MIN_SUPPORT_MORAL;
    private static final int MIN_SUPPORT_STAMINA;

    private void positionsStorm() {
        ...// some implementation
    }

    @Override
    public boolean support() {
        positionsStorm();
    }
    
    @Override
    public boolean canSuport() {
        return moral >= MIN_SUPPORT_MORAL && curStamina >= MIN_SUPPORT_STAMINA;
    }
    
    @Override
    public String getName() {
        return "Infantry";
    }
}
```

### Decorator pattern

Structural design pattern. Attach new responsibility dynamically (in runtime) to the object by placing it in a special wrapper, that contains these. Solution for subclass explosion problem.   

__roles__:

* __Component__ - abstract component which declares common behaviors for wrappers and wrapped object.  

* __Concrete component__ - wrapped class. Defines basic behavior which can be altered by decorators.  

* __Base decorator__ - references the wrapped object vie the abstract component.  

* __Concrete decorator__ - override the methods of the base decorator but still use it. The execution of the base decorator methods is necessary.  

* __Client__ - wraps components in layers of decorators. And works with it via the component interface.  

```java
// Component
public interface Coffee {
    public double getCost(); // Returns the cost of the coffee
    public String getIngredients(); // Returns the ingredients of the coffee
}

// Concrete Component
public class SimpleCoffee implements Coffee {
    @Override
    public double getCost() {
        return 1;
    }

    @Override
    public String getIngredients() {
        return "Coffee";
    }
}

// Base decorator
public abstract class CoffeeDecorator implements Coffee {
    private final Coffee decoratedCoffee;

    public CoffeeDecorator(Coffee cofee) {
        this.decoratedCoffee = c;
    }

    @Override
    public double getCost() { // Implementing methods of the interface
        return decoratedCoffee.getCost();
    }

    @Override
    public String getIngredients() {
        return decoratedCoffee.getIngredients();
    }
}

// concrete decorator 1
class WithMilk extends CoffeeDecorator {
    public WithMilk(Coffee cofee) {
        super(c);
    }

    @Override
    public double getCost() { // Overriding methods defined in the abstract superclass
        return super.getCost() + 0.5;
    }

    @Override
    public String getIngredients() {
        return super.getIngredients() + ", Milk";
    }
}

// concrete decorator 2
class WithSprinkles extends CoffeeDecorator {
    public WithSprinkles(Coffee cofee) {
        super(c);
    }

    @Override
    public double getCost() {
        return super.getCost() + 0.2;
    }

    @Override
    public String getIngredients() {
        return super.getIngredients() + ", Sprinkles";
    }
}
// --------------------------------------- //

// Client
public class Main {
    public static void printInfo(Coffee c) {
        System.out.println("Cost: " + c.getCost() + "; Ingredients: " + c.getIngredients());
    }

    public static void main(String[] args) {
        Coffee c = new SimpleCoffee();
        printInfo(c);

        c = new WithMilk(c);
        printInfo(c);

        c = new WithSprinkles(c);
        printInfo(c);
    }
}
```
__output:__
```
Cost: 1.0; Ingredients: Coffee
Cost: 1.5; Ingredients: Coffee, Milk
Cost: 1.7; Ingredients: Coffee, Milk, Sprinkles
```

### Facade pattern


