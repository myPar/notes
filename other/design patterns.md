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