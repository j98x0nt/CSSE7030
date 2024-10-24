java c
SlugDungeon
Assignment 2
Semester 2, 2024
CSSE7030
Due date: 25 October 2024, 15:00 GMT+10
1    IntroductionIn this assignment, you will implement a game in which a player must escape from a dungeon of slugs.  Unlike assignment 1, in this assignment you will be using object-oriented programming and following the Apple Model-View-Controller (MVC) design pattern shown in lectures. In addition to creating code for modelling the game, you will be implementing a graphical user interface (GUI). An example of a final completed game is shown in Figure 1.

Figure 1: Example screenshot from a completed implementation. Note that your display may look slightly different depending on your operating system.
2    Getting Started
Download a2. zip from Blackboard — this archive contains the necessary files to start this assign- ment. Once extracted, the a2. zip archive will provide the following files:
a2. py  This is the only file you will submit and is where you write your code. Do not make changes to any other files.
support . py  Do not modify or submit this file, it contains pre-defined classes and constants to assist you in some parts of your assignment. In addition to these, you are encouraged to create your own constants and helper functions in a2. py where possible.
levels/ This folder contains a small collection of files used to initialize games of SlugDungeon.  In addition to these, you are encouraged to create your own files to help test your implementation where possible.
3    Gameplay
This section provides a brief overview of gameplay for Assignment 2.  Where interactions are not explicitly mentioned in this section, please see Section 4.
3.1    Overview and TerminologySlugDungeon takes place on a 2D grid called a board, where each < row, col > position on the board is represented by a tile.  In the dungeon, there are also  entities, which move around the board and potentially attack other entities.Each game consists of one player entity, and a number of slug entities.  Entities have a health and poison stat, and may optionally have an equipped  weapon.   An  entity  dies  when  their health is reduced to 0. At the end of each turn, if an entity is poisoned  (has a poison stat greater than 0) the entity takes damage equal to its poison amount before its poison stat is reduced by 1.  Each  turn, the player makes one move (either moving one square up, down, left, or right or remaining where they are). All entities attack every turn, but slugs can only move every second turn.
Weapons have an effect and a range (depending on their type).  Weapon’s effects can alter the health and poison stats of the entities at positions within their range.
3.2    Player InteractionThe game is played as a series of turns.  The user may instruct the player to move or stay where they are via keypresses (see Table 1 for the behaviours associated with each key). If the key pressed is not associated with a valid movement (e.g. it is not one of ’a’, ’s’, ’w’, ’d’, or ’space’, or it would move the player to a blocking tile or to another entity’s position) it is ignored and not counted as a turn. Otherwise, if the keypress does result in a valid move (or intentionally leaving the player in place), the following steps should occur:
1.  The player moves (or remains where it is) according to Table 1.
2.  If the player’s new position contains a weapon, the player picks up the weapon and discards any weapon it may have already been holding.
3. The player attacks from its new position.
4.  All slugs that die as a result of the player’s attack are removed from the game.  Each dead slug drops its weapon onto the tile it occupied when it died, replacing any weapon that already existed on that tile.
5.  The player’s poison is applied and then reduced.
6.  Each slug’s poison is applied and then reduced.
7.  If the slugs are able to move on this turn, all slugs should make their desired move.
8. All slugs attack from their (potentially new) position.
9.  If the player has won or lost the game, a messagebox is displayed informing the user of the outcome and asking whether the user would like to play again.  If the user chooses to play again, the game file should reload and the user should be able to play again.  If they select not to play again, the program should terminate gracefully.  Otherwise, the program should continue waiting for more keypresses to begin a new turn.
Key Pressed                      Intended behaviour
a                          Attempt to move player left by 1 square.
s                           Attempt to move player down by 1 square.
d                           Attempt to move player right by 1 square.
w                           Attempt to move player up by 1 square.
space                      Have player remain where they are (allow them to attack again from their current position).
Anything else           Ignore.
Table 1: Key press behaviours
3.3    MovementEntities can only move one square at a time (directly up, down, left, or right).  Slugs are only allowed to move once for every second turn (where a turn is one player movement).  An entity can only move to a target position if they are allowed to move this turn, the target position does not contain a blocking tile or another entity, and the target position is one square away from the entity’s original position.
3.4    AttackingEntities attempt to make an attack every turn after they have moved. When the player attacks, it applies its weapon’s effect to all slugs within its weapon’s range. When a slug attacks, it applies its weapon’s effect to the player if the player is within the slug’s weapon’s range.  If an entity is not holding a weapon its attack should have no effect.
3.5    Game End
The game ends when one of the following occurs:
•  The user wins the game because all slugs are dead and the player is on a goal tile.
•  The user loses the game because the player dies.


4    ImplementationNOTE: You are not permitted to add any additional import statements to a2. py.  Doing so will result in a deduction of up to 100% of your mark. You must not modify or remove the import statements already provided to you in a2. py. Removing or modifying these existing import statements may result in a deduction of up to 100% of your mark.
Required Classes, Methods, and FunctionsYou are required to follow the Apple Model-View-Controller design pattern when implementing this assignment. In order to achieve this, you are required to implement the classes, methods, and func- tions described in this section.The class diagram in Figure 2 provides an overview of all of the classes you must implement in your assignment, and the basic relationships between them.  The details of these classes and their methods are described in depth in Sections 4.1, 4.2 and 4.3. Within Figure 2:
•  Orange classes are those provided to you in the support file, or imported from TkInter.
•  Green classes are abstract classes.  However, you are not required to enforce the abstract nature of the green classes in their implementation.  The purpose of this distinction is to indicate to you that you should only ever instantiate the blue classes in your program (though you should instantiate the green classes to test them before beginning work on their subclasses).
•  Blue classes are  concrete classes.
•  Solid arrows indicate inheritance (i.e. the “is-a” relationship).
• Dotted arrows indicate composition (i.e. the “has-a” relationship). An arrow marked with 1-1 denotes that each instance of the class at the base of the arrow contains exactly one instance of the class at the head of the arrow.  An arrow marked with 1-N denotes that each instance of the class at the base of the arrow may contain many instances of the class at the head of the arrow.The rest of this section describes the required implementation in detail.  You should complete the model section, and ensure it operates correctly  (via your own testing and the Gradescope tests) before moving on to the view and controller sections.  You will not be able to earn marks for the controller section until you have passed all Gradescope tests for the model section.NOTE: It is possible to receive a passing grade on this assessment by completing only section 4.1, provided the solution works well (according to the Gradescope tests) and is appropriately styled. See section 5.2 for more detail on style. requirements.
4.1    Model
The following are the classes and methods you are  required  to implement  as part of the model.You should develop the classes in the order in which they are described in this section and test each class (locally and on Gradescope) before moving on to the next class. Functionality marks are awarded for each class (and each method) that works correctly. You will likely do very poorly if you submit an attempt at every class, where no classes work according to the description. Some classes require significantly more time to implement than others.  The marks allocated to each class are not necessarily an indication of their difficulty or the time required to complete them.  You are allowed (and encouraged) to write additional helper methods for any class to help break up long methods, but these helper methods MUST be private (i.e. they must be named with a leading underscore).

Figure 2: Basic class relationship diagram for the classes in assignment 2.
4.1.1    Weapon()Weapon is an abstract class from which all instantiated types of weapon inherit. This class provides default weapon behaviour, which can be inherited or overridden by specific types of weapons.  All weapons should have a symbol, name, effect, and range.
• When a weapon is used to attack, it has an effect on its target, which may alter the targets health or poison stat. Weapon classes are not responsible for applying their effects to entities, but must provide details about their intended effects.   Within this class, a weapon’s effect is represented by a dictionary mapping strings to integers, where the string represents the type of effect and the integer represents the strength of the effect.  The valid effect types are "damage" (which reduces the target’s health), "healing" (which increases the target’s health), and "poison" (which increases the targets poison stat).
•  A weapon’s range is an integer representing how far the effects of the weapon reach in all 4 directions.  A range of 1 would mean that a weapon’s effects would reach entities located one position up, down, left, and right of the weapon.
Abstract weapons have no effects and a range of 0. The    init    method does not take any arguments beyond self. Weapon must implement the following methods:
• get name(self)  ->  str
Returns the name of this weapon.
• get   symbol(self)  ->  str
Returns the symbol of this weapon.
• get effect(self)  ->  dict[str,  int]
Returns a dictionary representing the weapon’s effects.
• get targets(self, position:  Position)  ->  list[Position]Returns a list of all positions within range for this weapon, given that the weapon is currently at position. Note that this method does not need to check whether the target positions exist within a dungeon.
•     str    (self)  ->  str
Returns the name of this weapon.
•     repr    (self)  ->  str
Returns a string which could be copied and pasted into a REPL to construct a new instance identical to self.
Examples:>>>  weapon  = Weapon() >>>  weapon. get_name() 'AbstractWeapon'
>>> weapon. get_symbol() '代 写CSSE7030 SlugDungeon Assignment 2 Semester 2, 2024Python
代做程序编程语言W'
>>> weapon. get_effect() {}
>>> weapon. get_targets((1,  1))
[]
>>>  str(weapon)   'AbstractWeapon'
>>>  weapon Weapon()
4.1.2    PoisonDart(Weapon)PoisonDart inherits from Weapon.  A poison dart has a range of 2 and applies 2 poison to its targets.
Examples:
>>> poison_dart  =  PoisonDart() >>> poison_dart. get_name()
'PoisonDart'
>>> poison_dart. get_symbol() 'D'
>>> poison_dart. get_effect() {'poison':  2}
>>> poison_dart. get_targets((1,  1))
[(1,  2),  (1,  0),  (2,  1),  (0,  1),  (1,  3),  (1,  -1),  (3,  1),  (-1,  1)] >>> poison_dart. get_targets((8,  8))
[(8,  9),  (8,  7),  (9,  8),  (7,  8),  (8,  10),  (8,  6),  (10,  8),  (6,  8)] >>>  str(poison_dart)
'PoisonDart'
>>> poison_dart PoisonDart()
4.1.3    PoisonSword(Weapon)
PoisonSword inherits from Weapon.  A poison sword has a range of 1 and both damages its targets by 2 and increases their poison stat by 1.
Examples:
>>> poison_sword  =  PoisonSword() >>> poison_sword. get_name()
'PoisonSword'
>>> poison_sword. get_symbol() 'S'
>>> poison_sword. get_effect() {'damage':  2,  'poison':   1}
>>> poison_sword. get_targets((1,  1)) [(1,  2),  (1,  0),  (2,  1),  (0,  1)]
>>> poison_sword. get_targets((0,  0)) [(0,  1),  (0,  -1),  (1,  0),  (-1,  0)]
>>>  str(poison_sword)
'PoisonSword'
>>> poison_sword PoisonSword()
4.1.4    HealingRock(Weapon)
HealingRock inherits from Weapon. A healing rock has a range of 2 and increases its targets health by 2.
Examples:
>>> healing_rock  =  HealingRock() >>> healing_rock. get_name()
'HealingRock'
>>> healing_rock. get_symbol() 'H'
>>> healing_rock. get_effect() {'healing':  2}
>>> healing_rock. get_targets((1,  1))
[(1,  2),  (1,  0),  (2,  1),  (0,  1),  (1,  3),  (1,  -1),  (3,  1),  (-1,  1)] >>>  str(healing_rock)
'HealingRock'
>>> healing_rock HealingRock()
4.1.5    Tile()Tile is a class representing individual tiles (positions) in the dungeon.  A tile may or may not beblocking and may or may not have a weapon sitting on it.  Entities cannot move onto blocking tiles, but weapon effects can pass through blocking tiles.  Each tile has a symbol which represents what type of tile it is. Tile must implement the following methods:
•     init    (self,  symbol:  str,  is blocking:  bool)  ->  None
Constructs a new tile instance with the given symbol and is_blocking status.  Newly instan- tiated tiles do not have a weapon on them.
•  is blocking(self)  -> bool
Returns True if this tile is blocking and False otherwise.
• get weapon(self)  ->  Optional[Weapon]
Returns the weapon that’s on this tile, or None if there is no weapon on this tile.


•  set weapon(self,  weapon:  Weapon)  ->  None
Sets the weapon on this tile to weapon, replacing any weapon that may already be there.
• remove weapon(self)  ->  None
Removes the tiles’s current weapon.
•     str    (self)  ->  str
Returns the symbol associated with this tile.
•     repr    (self)  ->  str
Returns a string which could be copied and pasted into a REPL to construct an instance of Tile with the same symbol and is_blocking status as self.
Examples:
>>>  tile  =  Tile("#",  True) >>>  tile. is_blocking()
True
>>>  tile. get_weapon()
>>> healing_rock  =  HealingRock()   >>>  tile. set_weapon(healing_rock)
>>>  tile. get_weapon() HealingRock()
>>>  tile. get_weapon() . get_effect() {'healing':  2}
>>>  tile. remove_weapon() >>>  tile. get_weapon()
>>>  str(tile)
'#'
>>>  tile
Tile('#',  True)
>>>  tile  =  Tile("hello",  False) >>>  tile
Tile('hello',  False)
>>>  tile. is_blocking() False
4.1.6    create tile(symbol:  str)  ->  Tile
create tile is a function which constructs and returns an appropriate instance of Tile based on the symbol string. Table 2 describes how the tile should be created based on the value of symbol.
symbol                            Tile details
"#"                       A tile representing a wall, which is blocking and has the symbol "#".
" "                         A tile representing the floor, which is non-blocking and has the symbol " ".
"G"                        A tile representing the a goal, which is non-blocking and has the symbol "G".
"D", "S", or "H"        A floor tile (non-blocking with the symbol " "). The weapon on the tile should be an instance of the type of Weapon corresponding to the given symbol.
Any other symbol        A floor tile (non-blocking with the symbol " ").
Table 2: Symbol to Tile conversion.
Examples:
>>> wall  =  create_tile("#") >>> wall
Tile('#',  True)
>>> wall. is_blocking() True
>>> wall. get_weapon() >>>  create_tile("  ")   Tile('  ',  False)
>>>  create_tile("hello") Tile('  ',  False)
>>> weapon_tile  =  create_tile("D")
>>> weapon_tile   Tile('  ',  False)
>>> weapon_tile. get_weapon() PoisonDart()
4.1.7    Entity()Entity is an abstract class from which all instantiated types of entity inherit.  This class providesdefault entity behaviour which can be inherited, overridden, or extended by specific types of entities. All entities should have a health and poison stat, as well as a maximum health.  Entities may or may not hold a weapon.
Entity must implement the following methods:
•     init    (self, max health:  int)  ->  None
Constructs a new entity with the given max health.  The entity starts with its health stat equal to max_health, its poison stat at 0, and no weapon.
• get   symbol(self)  ->  str
Returns the symbol associated with this entity. The default for an abstract entity is "E".
• get name(self)  ->  strReturns the name associated with this entity. The default for an abstract entity is "Entity".
• get health(self)  ->  int
Returns the entity’s current health stat.
• get poison(self)  ->  int
Returns the entity’s current poison stat.
• get weapon(self)  ->  Optional[Weapon]Returns the weapon currently held by this entity, or None if the entity is not holding a weapon.
•  equip(self, weapon:  Weapon)  ->  None
Sets the entity’s weapon to weapon, replacing any weapon the entity is already holding.
• get weapon targets(self, position:  Position)  ->  list[Position]
Returns the positions the entity can attack with its weapon from the given position.  If the entity doesn’t have a weapon this method should return an empty list.
• get weapon effect(self)  ->  dict[str,  int]
Returns the effect of the entity’s weapon.  If the entity doesn’t have a weapon this method should return an empty dictionary.
•  apply effects(self,  effects:  dict[str,  int])  ->  NoneApplies each of the effects described in the effects dictionary to the entity.  This involves increasing the entity’s health by any "healing" amount, reducing the entity’s health by any "damage" amount, and increasing the entity’s poison stat by any "poison" amount.  Note that this method should not handle reducing the entity’s health by the poison stat.  The entity’s health should be capped between 0 and its max_health (inclusive).
•  apply poison(self)  ->  NoneReduces the entity’s health by its poison amount (capping the health at 0), then reduces the entity’s poison stat by 1 if it is above 0. If the player’s poison stat is 0, this method should do nothing.
•  is alive(self)  -> bool
Returns True if the entity is still alive (its health is greater than 0) and False otherwise.
•     str    (self)  ->  str
Returns the name of this entity.
•     repr    (self)  ->  str
Returns a string which could be copied and pasted into a REPL to construct a new instance which would look identical to the original state of self.
Examples:>>>  entity  =  Entity(10) >>>  entity. get_symbol() 'E'
>>>  entity. get_name() 'Entity'
>>>  entity. get_health() 10
>>>  entity. get_poison() 0
>>>  entity. get_weapon()
>>>  entity. get_weapon_targets((1,  1)) []
>>>  entity. get_weapon_effect() {}
>>>  entity. equip(PoisonSword()) >>>  entity. get_weapon()
PoisonSword()
>>>  entity. get_weapon_targets((0,  0)) [(0,  1),  (0,  -1),  (1,  0),  (-1,  0)]
>>>  entity. get_weapon_effect()
{'damage':  2,  'poison':   1}
>>>  entity. apply_effects({'poison':  4,  'damage':  3})
>>>  entity. get_health() 7
>>>  entity. get_poison() 4
>>>  entity. apply_poison() >>>  entity. get_health()
3
>>>  entity. get_poison() 3
>>>  entity. apply_effects({'healing':  20}) >>>  entity. get_health()
10
>>>  str(entity)
'Entity'
>>>  entity Entity(10)
4.1.8    Player(Entity)
Player inherits from Entity.  Player should provide almost identical behaviour to the abstract Entity class, but with a name of "Player" and a symbol of "P".
Examples:
>>> player  =  Player(20) >>>  str(player)
'Player'
>>> player Player(20)>>> player. equip(PoisonDart()) >>> player. get_weapon_effect() {'poison':  2}
>>> player. apply_effects({'damage':  25}) >>> player. get_health()
0
>>> player Player(20)
4.1.9    Slug(Entity)Slug is an abstract class which inherits from Entity and from which all instantiated types of sluginherit.  Slugs can only move  (change position) every second turn, starting on the first turn of the game. That is, slugs move on the first turn, the third turn, the fifth turn, and so on. Even though slugs only move every second turn, they attack every turn.  Slugs provide functionality to decide how they should move.  In addition to regular Entity behaviour, the Slug class must implement the following methods:
•  choose move(self,  candidates:  list[Position],  current position:  Position, player position:  Position)  ->  PositionChooses and returns the position the slug should move to (from the positions in candidates or the current_position), assuming the slug can move. In the abstract Slug class this method should raise a NotImplementedError with the message "Slug  subclasses  must  implement
a  choose_move  method . ".  Subclasses of Slug must override this method with a valid imple- mentation. Note that this method must not mutate its arguments.
•  can move(self)  -> bool
Returns True if the slug can move on this turn and False otherwise.
•  end turn(self)  ->  None
Registers that the slug has completed another turn (toggles whether the slug can move).
Examples:
>>>  slug  =  Slug(5)
>>>  slug. equip(HealingRock()) >>>  slug.get_weapon_effect()   {'healing':  2}
>>>  slug. can_move() True>>>  slug. end_turn() >>>  slug. can_move() False
>>>  slug. can_move() False>>>  slug. end_turn() >>>  slug. can_move() True
>>>  slug. choose_move([(0,  0),  (1,  1)],  (1,  0),  (2,  4))
...
NotImplementedError:  Slug  subclasses  must  implement  a  choose_move  method . >>>  str(slug)
'Slug'
>>>  slug Slug(5)
4.1.10    NiceSlug(Slug)NiceSlug inherits from Slug, and represents a nice but lazy slug which stays where it is wheneverit can move.  All NiceSlug instances should hold a HealingRock weapon and have a max_health of 10. NiceSlug should not take any arguments to     init     (beyond self). NiceSlug, as a Slug subclass, must override the choose_move function with appropriate behaviour.
Examples:
>>> nice_slug  =  NiceSlug()
>>> nice_slug NiceSlug()
>>>  str(nice_slug) 'NiceSlug'
>>> nice_slug. get_health() 10
>>> nice_slug. get_weapon() HealingRock()
>>> nice_slug. get_weapon_effect() {'healing':  2}
>>> nice_slug. choose_move([(0,  1),  (1,  0),  (1,  2),  (2,  1)],  (1,  1),  (2,  4)) (1,  1)



         
加QQ：99515681  WX：codinghelp  Email: 99515681@qq.com
