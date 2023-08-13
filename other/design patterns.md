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


