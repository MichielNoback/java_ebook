# Objects: ninjas and growing cells {#first-look-at-objects}

This chapter presents two independent discussions of what classes and objects are. The first "Ninjas" focuses on what an object is and does. The second, "Cells" also discusses object relationships and testing.

## Ninjas

Suppose you are building a game with ninjas, trolls and all sorts of nasty creatures. 
In an Object-Oriented programming environment such as Java you will start by defining the entities that play a role in the app you are developing.  

In this case, `Ninja` will be one of the entities, or **_Types_** populating the application. Other Types in this game may be `Troll`, `Game`, `Player`, `Weapon`, `ScoreBoard`, and so on. Each of these Types will have **_properties_** and **_behavior_**: things they _have_ and things they _can do_. 

A Type is represented by a blueprint, its class. Such a class resides in a single source file. In the Java core packages many types are predefined. There are very basic types, for instance `Integer` (representing whole numbers) and `String` (representing character data) but there are also more complex Types, such as `LocalDate`. When programming you will create novel Types all the time. A `Ninja` type for instance.

We'll have a look at the `Ninja` type and create a blueprint for it. In our game, Ninjas can do what you expect of them: they have a name and an energy level (0 means they're dead, 100 is freshly spawned), they have a position in the game and they can move, and attack their opponents.
There's more of course but let's keep things simple for now.

Given the above description, this is a first draft of a Ninja class that I came up with.

```java
//package defines namespace
package nl.bioinf.nomi.ninjas;

//the actual class
class Ninja {

    //Here are the properties every Ninja will HAVE
    //We call these INSTANCE VARIABLES
    String name;
    int energyLevel = 100;
    double topCoordinate;
    double leftCoordinate;

    //Here are the methods - what every Ninja can DO
    void move(double top, double left) {
        this.topCoordinate += top;
        this.leftCoordinate += left;
    }

    void attack(int power, GameCharacter opponent) {
        //energy is drawn from its own reserve for an attack
        this.energyLevel -= power;
        //but has double effect on its opponent
        opponent.drainEnergy(power * 2);
    }
//end of the class
}
```

I kept this class really clean - no access modifiers (`public`, `private`, `static`, etc) that will distract from the key points being made here. 

At the top of the file there is a **_package declaration_**. This defines the namespace this class lives in. Since nobody in their right mind would use my web domain, I'm pretty sure the fully qualified name `nl.bioinf.nomi.ninjas.Ninja` will uniquely point to this class. Think about it: how many `User` classes will have been designed over the world?

Next, there is the line `class Ninja {` which delimits the actual **_class body_**. No code except for the package declaration and import statements can live outside the class body - it will not compile.

Below the class name declaration you find its **_instance variables_**. These define the properties that all instances (created objects) of this class will have. You can see only one of the instance variables has an assigned value: `energyLevel`. This means that every Ninja will have an energy level of 100 once it is constructed. Do the other variables have no value? Yes they do; if a value is not declared for instance variables they will get the **_default value_** of the type. For object types this is `null`, for boolean it is `false` and for numeric types it is `0`. So this:

```java
    String name;
    int energyLevel = 100;
    double topCoordinate;
    double leftCoordinate;
```

is exactly te same as this:

```java
    String name = null;
    int energyLevel = 100;
    double topCoordinate = 0;
    double leftCoordinate = 0;
```

Within the class body, no free-living statements are allowed other than declaration and assignment of its instance variables. So these are allowed:
`double leftCoordinate;` and `int energyLevel = 100;`, but a `for`-loop is not.

Finally, there are two **_methods_** defined: `move()` and `attack()`. Method `move()` is pretty straightforward; it changes the location of the Ninja object on which the method is called (we call this the _current_ object) by the given lateral and vertical offsets. The use of `this.` indicates we are accessing the current objects' instance variables. Use of `this.` is not required but strongly encouraged. The method does not return anything, as declared by the `void` return statement.

The next method, `attack()` is a little more complex. Its **_signature_**  

```java 
void attack(int power, GameCharacter opponent){ }
```  

says it should receive a `power` value for its attack as well as an instance of the `GameCharacter` class (below) which will be the subject of the attack. 
Not only does it operate on its own instance variables, but also it also **_calls_** a method on the object it is attacking: `opponent.drainEnergy(power * 2);`. This method does not return anything either.

Here is the `GameCharacter` class:

```java
package nl.bioinf.nomi.ninjas;

public class GameCharacter {
    public int energyLevel = 100;

    public void drainEnergy(int amount) {
        this.energyLevel -= amount;
    }
}
```

Here is some usage within a class called `Game`. The `main()` method has been discussed in the previous chapter.
This is the first place we see the `new` keyword. Whenever you see the `new` keyword, it means a new object is instantiated of the type specified after `new`. So `new Ninja();` means a new Ninja instance (object) has been created, inwhich the instance variables have been created and given their specified values, or the default values discussed above. See the section later in this chapter for more details on object construction. 

```java
package nl.bioinf.nomi.ninjas;

public class Game {
    public static void main(String[] args) {
        //spawn (instantiate) a Ninja
        Ninja ninja = new Ninja();

        //change its name
        ninja.name = "Rogue Bastard";
        //print some values of the ninja
        System.out.println("Ninja name      = " + ninja.name);
        System.out.println("Ninja energy    = " + ninja.energyLevel);
        System.out.println("Ninja position  = ["
                + ninja.topCoordinate
                + ":"
                + ninja.leftCoordinate
                + "]");

        //spawn an opponent
        GameCharacter opponent = new GameCharacter();
        opponent.name = "Delirious Troll";

        //change position twice
        ninja.move(2, 9);
        ninja.move(-6.4, 3.5);
        System.out.println("Ninja position  = ["
                + ninja.topCoordinate
                + ":"
                + ninja.leftCoordinate
                + "]");

        //attack opponent
        ninja.attack(5, opponent);

        //energy of both has changed
        System.out.println("Ninja energy    = " + ninja.energyLevel);
        System.out.println("Opponent name   = " + opponent.name);
        System.out.println("Opponent energy = " + opponent.energyLevel);
    }
}
```

The terminal output, when `main(0)` is run, will be:

<pre class="console_out">
Ninja name      = Rogue Bastard
Ninja energy    = 100
Ninja position  = [0.0:0.0]
Ninja position  = [-4.4:12.5]
Ninja energy    = 95
Opponent name   = Delirious Troll
Opponent energy = 90
</pre>


That's a first acquaintance with classes and objects. We have used the core Java class `String`, and defined three of our own: `Ninja`, `GameCharacter` and `Game`. Besides this, we have used the **_primitive types_** `int` and `double`. 

Next up: Cells and TestTubes in a second round of getting to know objects. As extra layer, JUnit testing will be introduced.


## Growing cells

Suppose you want to build a cell growth simulation application. 
After careful analysis of the domain you decided that this involves three entities: 
A simulator that controls the simulation process, a test tube that will hold the cells that are growing, and the cells themselves.

The next step in a modeling process is to determine the **_relationships_** between the different entities. 
In this case this is as follows. The simulator will receive configuration 
arguments (e.g. how many cells to grow) from the command-line, start the 
simulation process and instantiate a test tube. 
The test tube will be responsible for instantiating the initial cell
population. Finally, there will be cells growing within the test tube.

Three separate source files are created (within a single package), each holding one class:

- `CellGrowthSimulator.java`
- `TestTube.java`
- `Cell.java`

#### Cell.java {-}

We'll start with the simplest one: Cell. 
It has an instance variable called "size" which has a default value of 5 when Cell objects are instantiated. 
Every call of the `grow()` method makes it increase its size by one.

```java
package snippets.testtube;

class Cell {
    /**
     * Instance variable "size"; 5 micrometer is the default diameter when Cell objects are instantiated
     */
    int diameter = 5;
    /**
     * Lets this cell grow in a single 1-micrometer increment
     */
    void grow() {
        //grow by 1 micrometer
        this.diameter += 1;
        System.out.println("I am a Cell. My size is " + this.diameter);
    }
}
```

To test the logic and correctness of this class we will have to wait until other components are developed, such as TestTube and CellGrowthSimulator.
_Unless you use (J)Unit testing_. 

#### JUnit testing {-}

JUnit is a [unit testing](https://en.wikipedia.org/wiki/Unit_testing) framework for Java. 
Its main purpose is to have a suite of test methods guaranteeing the correctness of your **_production code_**. 
To create JUnit a test method, you need to have the JUnit libraries defined as dependencies on your **_class path_**. 
See the post ["A first IntelliJ project"](/01_getting_started/intellij.md) for instructions how to that for JUnit5. This piece should be in your `build.gradle` file:

```gradle
dependencies {
    testImplementation("org.junit.jupiter:junit-jupiter-api:5.6.2")
    testRuntimeOnly("org.junit.jupiter:junit-jupiter-engine:5.6.2")
}
```

Next, you need to create a test class. Place the cursor on the class name (`Cell`) and press `alt+enter`. Select "Create Test".

![Create JUnit class](01_getting_started/figures/create_junit_test_class_1.png) 

Select the method(s) you want to create test(s) for and press "OK".

![Create JUnit class](01_getting_started/figures/create_junit_test_class_2.png)

The test class opens in the editor and looks something like this:

```java
package snippets.testtube;

import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

class CellTest {

    @Test
    void grow() {
    }
}
```

Let's put some testing code in it to see whether our program logic works as intended:

```java
    @Test
    void grow() {
        Cell cell = new Cell();
        //the initial diameter is supposed to be 5
        assertTrue(cell.diameter == 5);
        cell.grow();
        //the diameter should have increased by one
        assertTrue(cell.diameter == 6);
    }
```

When you click on the little green triangle in the editor margin, the test will be executed and you get output like this:

![Create JUnit method](01_getting_started/figures/create_junit_test_method_1.png)

The green checks indicate the test **_assertions_** passed. Knowing the Cell class is OK, let's proceed to class TestTube.


#### TestTube.java {-}

TestTube is slightly more complex. 
It defines a **_constructor_** that makes it mandatory to provide an initial number of cells. It will throw an `IllegalArgumentException` when a wrong value is passed for the `initialCellCount` parameter. You again see the use of the keyword `new` to instantiate Cell objects.  

```java
package snippets.testtube;

class TestTube {
    Cell[] cells;

    /**
     * Constructs with an initial cell count.
     * An exception is thrown when the initial cell count is below 1 or above 10e4.
     *
     * @param initialCellCount the initial cell count
     * @throws IllegalArgumentException ex
     */
    TestTube(int initialCellCount) {
        if (initialCellCount == 0 || initialCellCount > 10e4) {
            throw new IllegalArgumentException("initial cell count should be above 1 and below 10e4: " + initialCellCount);
        }
        //initialize the array with new Cells
        cells = new Cell[initialCellCount];
        for (int i = 0; i < initialCellCount; i++) {
            cells[i] = new Cell();
        }
    }

    /**
     * Grows the cells, in one single iteration.
     */
    void growCells() {
        for (Cell cell : cells) {
            cell.grow();
        }
    }
}
```

Note the for-loop in the constructor of this class:

```java
for (int i = 0; i < initialCellCount; i++) {
    cells[i] = new Cell();
}
```

It has three elements between the parentheses: 

```
for (<LOOP INITIALIZATION>; <EXIT CONDITION>; <ITERATION INCREMENT>)
```

The for-loop itself will be dealt with in the post ["Flow control structures"](/02_syntax/flow_control_structures.md).  

Here is a JUnit test to verify the construction of the TestTube:

```java
class TestTubeTest {
    @Test
    void growCells() {
        TestTube testTube = new TestTube(10);
        assertTrue(testTube.cells.length == 10);
    }
}
```

#### CellGrowthSimulator.java {-}

The last class of the system is `CellGrowthSimulator`. Here is a first version:

```java
package snippets.testtube;
/**
 * "Controller" class
 */
class CellGrowthSimulator {
    /**
     * @param args command-line args should be length one, 
     * containing an initial cell number.
     */
    static void main(String[] args) {
        if (args.length != 1) {
            System.err.println("You must provide an initial cell count. Aborting.");
        }
        //args[] is a String array, so the item at index [0] 
        //should be converted into and `int` first.
        int initialCellNumber = Integer.parseInt(args[0]);
        startSimulation(initialCellNumber);
    }

    static void startSimulation(int initialCellNumber) {
        TestTube testTube = new TestTube(initialCellNumber);
        //do one iteration of growing
        testTube.growCells();
    }
}
```

The `String[] args`  argument to `main()` is the argument array passed from the terminal when you start the application.

(Be aware that this type of use of command-line arguments is discouraged; 
you should implement and support a standards-adhering command-line syntax, 
e.g. `java -jar GrowthSimulator --initial_count 5`).

The final model now has this chain of relationships:

**_CellGrowthSimulator HAS-A TestTube and TestTube HAS one or more Cells_**.


## Object construction (first iteration)

You have seen the `new` keyword used several times now. But what does happen, exactly, when you type

```java
Cell cell = new Cell();
```

The `new` keyword combined with the class name followed by parentheses 
calls the _constructor_ method of that class. 
The constructor instantiates an instance of the class and returns the reference to the object. 
The parentheses `()` enclose the argument list for the constructor method, which is empty in this case. 
In class `TestTube` we _did_ see a constructor argument however - one specifying the number of cells to grow.

The three steps of object construction are **_declaration_**, **_creation_** and **_assignment_**:

1. The code `Cell cell` **_declares_** a variable of type Cell.
2. The code `new Cell()` **_instantiates_** an object of type Cell, stores this in memory and returns a reference to this stored object.
3. The equals sign `=` **_assigns_** (couples) the returned reference to the declared variable.

So...where is this constructor method in class Cell and what does it do? 
If you look at the class you don't see a constructor, as we have seen in class `TestTube`.
It is created by the Java compiler if you don't specify it yourself. The compiler-processed code will have something like this inserted: 

```java
public Cell() {}
```

More on constructors in later chapters.

## Inheritance (first discussion)

Inheritance is one of the key features of object-oriented programming.

This is a very powerful mechanism to introduce new or adjusted behavior in a software system because subclasses inherit all properties and methods of their superclass. 
For example, the `CancerCell` class below `extends Cell` and thus declares itself to be a **_subclass_** of `Cell`. 
It not only extends itself with new functionality (`move()`), but also modifies the behavior of the `grow()` method by changing the `growthIncrement` property of its _supertype part_.

```java
//Cell.java
class Cell {
    int diameter = 5;
    int growthIncrement = 1;

    /**
     * Lets this cell grow in a single increment
     */
    void grow() {
        //grow by 1 micrometer
        this.diameter += growthIncrement;
        System.out.println("I am a Cell. My size is " + this.diameter);
    }
}
```

```java
//CancerCell.java
public class CancerCell extends Cell {
    CancerCell() {
        growthIncrement = 3;
    }

    void move() {
        System.out.println("Moving through the body");
    }
}
```

```java
//usage within GrowthSimulator
Cell cell = new Cell();
cell.grow();
cell.grow();
System.out.println("..........");
CancerCell cCell = new CancerCell();
cCell.grow();
cCell.grow();
cCell.move();
```

output:

<pre class="console_out">
I am a Cell. My size is 6
I am a Cell. My size is 7
..........
I am a Cell. My size is 8
I am a Cell. My size is 11
Moving through the body
</pre>

This is of course not the only place where we'll see inheritance discussed.
