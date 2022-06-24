<h1>Intro</h1>
<p>First introduced in their original form by <a href="http://staff.cs.utu.fi/~jounsmed/doos_06/material/DesignPrinciplesAndPatterns.pdf">"Uncle" Bob Martin in 2000</a>, the SOLID principles represent a series of rules for writing object-oriented code that is easier to modify and maintain.</p>
<p>The following are the principles that make up the SOLID acronym.</p>
</br>

<h1>Single Responsibility Principle</h1>
<p>The single responsibility principle is about keeping components (primarily classes, structs, and functions) small and avoiding writing monolithic components that do too many things.</p>
<p>Our example includes a Combatant class that represents a combatant in a game. It stores a name, health, strength, a weapon, and currently contains a method to simulate combat between itself and another combatant, returning the winner:</p>

```
struct Combatant {
  string m_name;
  int m_health;
  int m_strength;
  Weapon m_weapon;

  Combatant fight(Combatant combatant);
};
```

<p><i>Note: I purposely differentiate between 'Combatant' and 'combatant' in the following paragraphs. 'Combatant' refers to the Combatant struct, and 'combatant' refers either to an instance of that struct, or a combatant in general.</i></p>
<p>This works for simple combat, but what if we want to introduce 2-on-1, or 3-on-1 combat? Or, combat in units? We can add more methods to the Combatant class that accepts other combatants or lists of combatants, but does it make sense for a single Combatant instance to be responsible for all combat between combatants? What about global effects on combat? If we wanted to add a special effect that boosts or limits certain weapons, do we add a parameter to every method that simulates combat in order to use that effect? How many changes will we have to make to support that? How risky does that become the longer Combatant is relied on by other components?</p>
<p>Counter to the single responsibility principle, we have a component that is doing two things: representing a combatant and simulating combat. A simplified (and easier to maintain) version of our Combatant would solely represent what a Combatant is. The Combatant itself shouldn't know anything about the act of combat because it only stores what it's actually capable of: not how it uses those capabilities. This becomes more apparent in the previous example of unit combat where multiple combatants need to be considered because a single combatant is not responsible for the combat of others.</p>
<p>This updated example includes a CombatSimulator class to handle combat for the combatants. It also includes a method to set a global effect that can be applied to all combat:</p>

```
struct Combatant {
  string m_name;
  int m_health;
  int m_strength;
  Weapon m_weapon;
};

class CombatSimulator {
  public:

    // Applies an effect to all Weapons of WeaponType.
    void set_weapon_effect(WeaponType type, Effect effect);

    // Simulate combat between two combatants, returning the survivor.
    Combatant simulate(Combatant first, Combatant second);

    // Simulate combat between two groups, and return the survivors of the
    // winning group.
    vector<Combatant> simulate(
      vector<Combatant> first, vector<Combatant> second);
};
```
<p>With this new class we've effectively separated the definition of Combatant from combat itself. We can consider what defines a Combatant and the properties it has separately from the specifics of how it uses those properties, which in the future may be expanded into scenarios that we couldn't predict. Adding those scenarios becomes simpler if we limit the responsibilities of our classes.</p>
<p>For the sake of this example the CombatSimulator class was kept simple, but it may also be handling too many responsibilities. If we imagine how the second simulate method would be implemented, it has to take the two groups of combatants and simulate combat between them, then return the survivors of the winning group. It's likely the logic of our combat will dictate other behaviors or strategies specific to combatants in groups, and so the simulate method will have to implement those strategies. We can simplify this by separating these strategies from the CombatSimulator class, maybe by adding CombatUnit and CombatStrategy components, where a unit contains a list of combatants and an instance of the CombatStrategy that it uses. Now, instead of taking two vectors of combatants the simulate method could simply take two CombatUnit instances.</p>
<p>You will sometimes see this principle described as a component needing 'only a single reason to change'. This doesn't mean one specific reason (e.g., the only change we're allowed to make to Combatant is to add a field for agility), instead it means within the purpose of the component it should only have one reason to change. The purpose of our Combatant class is to represent a combatant in our program, so the single reason it should change is if the definition of a combatant fundamentally changes (e.g., we decide to add an endurance stat).</p>
</br>

<h1>Open-Closed Principle</h1>
<p>The open-closed principle (OCP) states that components should be "open for extension but closed for modification". This means that new behavior can be added to the program using an existing component ("open for extension"), but adding that behavior doesn't modify that component ("closed for modification").</p>
<p>The primary means of leveraging the open closed principle in object-oriented programming is through inheritance: defining a common interface (base class) that defines the available behaviors and allowing the specifics of those behaviors to vary via substituting different subclasses.</p>
<p>Continuing with our game example, we can implement a CombatUnit's strategy in compliance with the OCP by defining a CombatStrategy base class that the CombatUnit instances make use of. To keep this example simple we'll imagine that combat always occurs between only two units, and combat happens in waves: each unit sends out at least one combatant to fight in the wave and this is repeated until one side has no combatants left. In the event of a tie where both units have no surviving members, the fight will be considered a tie and both sides perish.</p>

```
class CombatStrategy {
  public:

    virtual vector<Combatant> get_combatants(
      CombatUnit unit, unsigned int wave) const = 0;
};

struct CombatUnit {
  string m_name;
  vector<Combatant> m_combatants;
  CombatStrategy* m_strategy;
};
```

<p>The CombatStrategy defines a get_combatants method that takes a vector of the available combatants and a wave number as a non-negative integer, and returns the combatants for that wave. The CombatSimulator will use a unit's CombatStrategy instance to determine what strategy it uses when sending combatants out to fight.</p>
<p>For some examples, we could have:</p>

```
// Sends out every combatant regardless of wave number.
class AllOutCombatStrategy : public CombatStrategy {
  public:

    vector<Combatant> get_combatants(
      vector<Combatant>, unsigned int wave) override;
};

// Sends the weakest remaining combatant out for each wave.
class WeakestFirstStrategy : public CombatStrategy {
  public:

    vector<Combatant> get_combatants(
      vector<Combatant> combatants, unsigned int wave) override;
};
```

<p>The alternative to the CombatUnit containing a CombatStrategy would have been for the CombatUnit to implement methods that define its own strategy. But, this creates the same issues we saw when we violate the single responsibility principle, as well as violating OCP and making the CombatUnit hard to maintain. Instead, the CombatUnit simply contains a strategy that can be changed at runtime, and new strategies can be added without modifying any of the CombatUnit, the base CombatStrategy, or the CombatSimulator itself (since the CombatSimulator only needs to know the API for the CombatStrategy, which won't change when strategies are added).</p>
</br>

<h1>Liskov Substitution Principle</h1>
<p>The Liskov substitution principle is about the expectations of types (typically classes, structs) when they're substituted for one another. Using classes as an example the principle dictates that when a subclass is substituted in place of a base class, the user of that subclass should not have to make any accommodations for that it wouldn't have to make for the base class.</p>
<p>We partially accomplished this with our CombatStrategy, where if the CombatSimulator uses a unit's CombatStrategy the simulator doesn't care which subclass instance the unit is using, only that it satisfies the API of the base CombatStrategy.</p>
<p>However, the first violation is in what a CombatStrategy returns. What if a CombatUnit has combatants remaining but the CombatStrategy implementation it uses returns an empty vector of Combatants? This may be more of an oversight in our game logic; maybe the unit that didn't send out any units can be considered as surrendering, but, for our current implementation this breaks the expectation we have of the CombatStrategy.</p>
<p>A second violation is the result of introducing a hypothetical CombatStrategy implementation that actually creates additional combatants. Suppose we add a CheatCombatStrategy where instead of returning combatants from the unit it just creates and returns a thousand random combatants every time. The expectation we had of the CombatStrategy is that it would only return combatants based on the vector of combatants it was given. The issue is if two CombatUnits both containing an instance of the CheatCombatStrategy fight then we have a situation where neither side is ever depleted, so the game will become unresponsive and just loop over this simulated fight that will never end.</p>
</br>

<h1>Interface Segregation Principle</h1>
<p>The interface segregation principle (ISP) is about preventing components from depending on code that they do not use by breaking up interfaces into smaller, more specific, pieces.</p>
<p>ISP addresses a situation that can be seen in the single responsibility principle example, where we have the CombatSimulator that implements methods for simulating combat between individuals and units. If we wanted to introduce combat simulations between more than two combatants or units, we can add methods to support those simulations. But, what happens if we want to introduce new <i>types</i> of combat simulations? What if instead of the 'wave' format of combat we want to be able to simulate multiple skirmishes simultaneously? It's excessive to have the CombatSimulator implement more methods to implement more types of simulations, so we'll use a simpler alternative.</p>
<p>The first step we'll take is to create an abstract base class to represent our combat simulation:</p>

```
class CombatSimulator {
  public:

    virtual Combatant simulate(Combatant& first, Combatant& second) = 0;

    // Simulate combat between multiple combatants.
    virtual Combatant simulate(vector<Combatant> combatants) = 0;

    virtual CombatUnit simulate(CombatUnit first, CombatUnit second) = 0;

    // Simulate combat between multiple units.
    virtual CombatUnit simulate(vector<CombatUnit> units) = 0;
};
```

<p><i>Note: to simplify the example, set_weapon_effect(...) was removed.</i></p>
<p>This works fine: we can now change the type of combat by implementing different subclasses of CombatSimulator and instantiating them when we want to update the simulation. So we continue and implement some different simulations:</p>

```
class WaveCombatSimulator : public CombatSimulator {
  public:

    // ...implements base class methods...
};

class SkirmishCombatSimulator : public CombatSimulator {
  public:

    // ...implements base class methods...
};
```

<p>I leave out the implementations for brevity, but if I did show them the implementations for Combatant versus Combatant combat would look the same for both the wave and skirmish styles of simulation. The reason being a wave style of combat and a skirmish style of combat are indistinguishable when the combat involves single combatants. The effect is we have duplicate code across both classes, and, we're forcing them to implement methods that they probably don't need to implement, because neither the wave style nor the skirmish style of combat care about implementing single combat in a unique way.</p>
<p>Single combat seems like a nice feature to have, though, so we probably don't want to completely discard it. It's also possible that our unit combat simulations will either contain or devolve into single combat as the fight takes place, so maybe we want to make use of it within the unit combat simulations. This leaves our final API looking like this:</p>

```
class SingleCombatSimulator {
  public:

    virtual Combatant simulate(Combatant& first, Combatant& second) = 0;

    virtual Combatant simulate(vector<Combatant> combatants) = 0;
};

class UnitCombatSimulator {

    virtual CombatUnit simulate(CombatUnit first, CombatUnit second) = 0;

    virtual CombatUnit simulate(vector<CombatUnit> units) = 0;
};

class BasicSingleCombatSimulator : public SingleCombatSimulator {
  public:

    // ...implements SingleCombatSimulator methods...
};

class WaveUnitCombatSimulator : public UnitCombatSimulator {
  public:
    
    // ...implements UnitCombatSimulator methods...
};

class SkirmishCombatSimulator : public UnitCombatSimulator {
  public:

    // ...implements UnitCombatSimulator methods...
};
```

<p>Our classes now only need to implement what's unique to them (in accordance with ISP), so any new UnitCombatSimulator subclasses only need to define the behaviors of combat as it relates to the style of simulation they represent. In doing so we have maintained the functionality of both combat styles (single, unit) with a more flexible design by allowing the unit simulations to potentially use different types of single combat simulations at runtime, if required.</p>
</br>

<h1>Dependency Inversion Principle</h1>
<p>Though the name only makes sense when the concept is demonstrated in a UML diagram, the dependency inversion principle aims to keep our software flexible by adding an abstraction between components that depend on each other.</p>
<p>Our next example will be to add some functionality so that the user can save and load their CombatUnit to a file.</p>
<p>To simplify the example we'll make a few assumptions:</p>
<ul>
  <li>That saving and loading always succeeds, removing the need for error checking.</li>
  <li>The GameSaveManager class maintains some kind of relationship with the operating system's filesystem (e.g., file handles, permissions, etc.), and sets up those dependencies in the constructor.</li>
  <li>Calling the save method with an existing filename overwrites that file.</li>
</ul>
<p>This leaves us with a straightforward API:</p>

```
class GameSaveManager {
  public:

    GameSaveManager();
    
    optional<CombatUnit> load(string filename);

    void save(CombatUnit unit, string filename);

    vector<string> get_saves() const;
};
```

<p>Pretty simple. Our class just sets up the filesystem dependencies when it's constructed and provides methods to save, load, and return all existing save files. The load method returns an optional in case we give it a filename that doesn't represent a save file.</p>
<p>As is tradition, this works, but the issue this time is that our game manager is now completely dependent on the file system. Suppose we decide we want to be able to write saves to a cloud server: we would either have to rewrite the internals of the GameSaveManager to switch between using the file system and using the cloud (which violates SRP), or, we'll need to find a way to separate them while making both methods of saving CombatUnits available.</p>
<p>By introducing an abstraction between the GameSaveManager and how files are saved we 'invert' the dependency between the GameSaveManager and precisely how it saves files. For this example, we'll modify the GameSaveManager's constructor to take a driver class that defines the relationship between the GameSaveManager and specifically how files are saved:</p>

```
class GameSaveManager {
  public:

    GameSaveManager(GameSaveDriver* driver);

    optional<CombatUnit> load(string filename);

    void save(CombatUnit unit, string filename);

    vector<string> get_saves() const;
};

class GameSaveDriver {
  public:

    virtual optional<CombatUnit> load(string filename) = 0;

    virtual void save(CombatUnit unit, string filename) = 0;

    virtual vector<string> get_saves() const = 0;
};

class FileSystemGameSaveDriver : public GameSaveDriver {
  public:

    // Sets up file handles, permissions, etc.
    FileSystemGameSaveDriver();

    // ...implements GameSaveDriver methods...
};

class CloudGameSaveDriver : public GameSaveDriver {
  public:

    // Sets up connection to our game save server.
    CloudGameSaveDriver();

    // ...implements GameSaveDriver methods...
};
```

<p>The GameSaveDriver becomes the abstraction between saving and the specific details of how files are saved: inherently documented in its API is the functionality required to save and load files, but leaves the details of how that's done to subclasses (FileSystemGameSaveDriver and CloudGameSaveDriver). The GameSaveManager can then forward calls to save, load, and get_saves to its driver instance, without worrying how that behavior is implemented.</p>
</br>

<h1>In Closing</h1>
<p>For clarity and brevity, the examples were purposely short and may contain bad practices (raw pointers and unnecessary copies, among other questionable design choices). The intent is to convey the main idea behind the given principle while keeping them easy to read for developers that use object-oriented languages other than C++.</p>
<p>A great way to test an API for flexibility and maintainability is to preemptively subject it to change. Imagining what happens if operations need to be added, if external devices change, and so on can expose future weaknesses in our designs. The SOLID principles give us time-tested solutions for improving our designs and keeping them maintainable when presented with these changes.</p>
</br>
