# A2 Justification Document

#### This document is structured with each component being separated, with minimal overlaps of content.

Due to converting to pdf, images are removed.
View images via https://github.com/maybe-Sk1/obsidian-sync/blob/main/README.md
#### In each section, most information is structed like:
1. Title / Topic
	Brief Description
	1. Issue 1
		Explanation
		Fix:
		- How issue 1 was fixed
2. ...
### [1. Code Design](#Component%201%20Code%20Design)
### [2: Limitations](#Component%202%20Limitations)
### [3: JUnit Tests](#Component%203%20JUnit%20Tests)
### [4: Implementations](#Component%204%20Implementation)


## Component #1: Code Design
## Solid Principles
1. Single responsibility principle
	Classes should have a one reason to change and a well-defined responsibility
	- Incorporated by high cohesion
2. Open/Closed Principle
	Entities should be open for extension but closed for modification
	- Incorporated by encapsulation
3. Liskov substitution principle
	Subtypes should be substituab;e for the base types without altering correctness
	- Incorporated with the use of both polymorphism and coupling
4. Interface segregation principle
	Clients should only have to see / have access to what they need to, removing un-nessesary data
	- Incorporated well by the provided code, but can be refined by allowing log messages to be toggles off for regular users
5. Dependency inversion principle
	High level modules should not depend on low-level modules
	- Incorporated by proper coupling

To ensure the solid principles, I prefer to focus on the HELPR principles as a method of cohering to it, as each aspect of HELPR (High cohesion, Encapsulation, Low Coupling, Polymorphism and Refactoring) assists in reinforcing the SOLID principles in a project.

#### High Cohesion:
Game Model has way too many responsibilities, as it manages:
1. The Game State 
2. Handling Collisions
3. Spawning Objects
4. Tracking Achievements

To ensure game model has higher cohesion, we can split up these responsibilities into more focused / direct classes
1. CollisionManager
	1. Handles the collision of entities, 
2. SpawnManager
	1. Handles the spawning of objects 
#### Encapsulation
The majority of methods have sufficient encapsulation that prevent methods being exposed for direct access however, there were two notable methods that needed to be fixed:
1. Direct access to spaceObjects was granted through getSpaceObjects, which exposed the internal list of spaceObjects.
#### Low Coupling

#### Polymorphism
Redundant code with large handle time can be refactored using polymorphism by splitting the behavior of the class to separate subclasses.

This is already shown in the constructor in the provided code for GameController; ![](Pasted%20image%2020250521110740.png)
Shown here, the GameControll constructor method has two different ways it can run;
1. If two input fields are given - excluding the gamemodel - it will create a new game

Optimisations:
1. Switching HandlePlayerInput to a switch for optimal runtime and code efficiency
	1. ![[Pasted image 20250519121624.png]]
	2. ![[Pasted image 20250519123049.png]]



### Refactoring:
Example taken from lecture slides
- ![](Pasted%20image%2020250520133415.png)
1. [x] Tick movement if statement refactored to become a helper method
	- ![](Pasted%20image%2020250520133748.png)
	- Becomes
	- ![](Pasted%20image%2020250520133819.png)
2. [x] Unreadable return statements being refactored
	1. ![](Pasted%20image%2020250520134132.png)

-----
## Component 2: Limitations

List of bugs:
1. [x] Entity phasing on tick
	Game.GameModel#CheckCollision
	Classes to fix:
	1. GameModel
		1. CheckCollision <- Method to fix
	
	Entities to fix:
	- Bullet x Enemy
		1. Implement y || y+1
	- Bullet x Asteroid
		1. Implement y || y+1
	- Ship x Enemy
		1. Not implemented, as it would result in a worse bug
	- Ship x Asteroid
		1. Not implemented, as it would result in a worse bug
		
	Fix method:
	
	- When a bullet is fired, both it and the moving enemies are dependent on the current tick
		![](Pasted%20image%2020250518182301.png)
	  But it is this very fact, that results in an issue when **both objects move in the same tick**. For example:
		![](Comparison.png)
	  Given that the only result of this bug is the bullet being one position above the enemy, we can fix this by checking if a bullet is above the enemy at any time, and if so, flag this as the same collision
	  
	  This should work without fault, as there is no edge cases where the bullet would be above an enemy without it meaning to collide with it.
	
	Issues that arise when implementing:
	- Can a ship fire a bullet above the enemy?
		No, since the bullet spawning method makes a bullet above at the ship's x and y+1 position and thus spawning above the ship.
		Hence, for a bullet to spawn above the enemy, the ship must be in the same position as  the enemy which **SHOULD** cause a collision between ship and enemy before the bullet
		
	- Would this also trigger a collision with the entity above it (if any)?
		No, due to the fact that Entities are spawned in order, the collision checks for each entity would be considered / run from bottom up and thus the bullet would collide with the bottom entity only
	
	- Why not implement this for ship collisions?
		Since the ship's position is determined by the players input, its position can be directly above any entity and would incorrectly be deemed as colliding if this fix was implemented to this entity.
2. [x] Press enter to start
	Game.GameController#HandlePlayerInput
	Classes to fix:
	1. GameController (Handling the game flow / tick)
		1. HandlePlayerInput <- Method to fix
	
	Fix Method:
	- Implement a (working) catch case for the enter key that will then allow the game to start once pressed
		- Thus, create a hasStarted Boolean that will detemine if onTIck(tick) and handlePlayerInput(tick) will run
		- Implement a check for both of these functions to block the method's functionality
			- ![](Pasted%20image%2020250519124214.png)
			- ![[Pasted image 20250519124234.png]]


	
	Fix method:
	- Create a Boolean for the game's start in GameController - hasStarted
	  When the game is initially run, check if a Boolean state is true, and if and ONLY if it is, then run the game, elsewise don't start the game
	
	Test method:
	Check if game starts immediately without enter key being used
4. [x] No area handling for bountry exception
	Game.GameController#HandlePlayerInput
	Classes to fix:
	1. GameController
		1. HandleplayerMovement <- Method to fix

	Fix method:
	Instead of throwing a boundary exception catching an out-of-bounds move, handle the collision log the improper move.
6. [x] Two bullets hitting an object at the same time
	Game.GameModel#CheckCollision
	Classes to fix:
	1. GameModel
		1. CheckCollision <- Method to fix
	
	Entities to fix:
	- Bullet x Enemy
	- Bullet x Asteroid

	Fix method:
	- As stated in the javadocs, if multiple bullets collide with an entity, all bullets colliding must be destroyed
	- Once a collision is between bullet and entity is found, use the x and y positions of the bullet colliding to find if any other existing bullets have the same position, and hence destroy these bullets at the same time as the original bullet.
8. [ ] %% Time elapsed while game is paused %% -- Not required via javadocs
	Classes to fix:
	1. PlayerStatsManager
		1. Implement a Boolean that will allow the elapsed time to keep on ticking
9. [x] Ensure pausing the game has a toggled log 
	Game.GameController#pauseGame()
	- i.e instead of logging "Game is paused!" on every pause press, it should alternate between 
		- "Game is paused!"
		- "Game is unpaused!"
	
	Fix:
	- Create a new method in gameModel, that returns a togglable state of isPaused
10. [x] Ensure pausing the game restricts the player's movement as the java docs state:
	Game.GameController#handlePlayerInput
	- "When the game is paused, only un-pausing should be possible. No other action of printing should occur."
	Fix:
	- Use return statement to break out of the method if the game is paused, hence blocking all input aside from un-pausing the game.
	- ![](Pasted%20image%2020250519132919.png)
	
	Fix:
	- Like the gameStarting block, create an if statement that blocks the functionality of the handlePlayerInput function if the game is paused
12. [x] Add the toString method implemented in a2 the ObjectsWithPosition
	1. Game.Core.ObjectsWithPosition
13. [x] Remove the string output for powerups:
	Game.Core.HealthPowerUp
	Game.Core.ShieldPowerUp
	1. HealthPowerUP
		![](Pasted%20image%2020250519140825.png)
	2. ShieldPowerUp
		![](Pasted%20image%2020250519141005.png)
		
14. [x] Implement descending movement for powerups
	Game.Core.DescendingEnemy
	fix:
	- Change the actions on tick to move downwards every 10 ticks
15. [x] For all tick-based movement, since the javadocs say 'every 10 ticks', it should not move on the initial tick so the operation used: 
	- "tick % 10 == 0" must be changed to
	- "tick % 10 == 0 && tick != 0" 
		For:
		`if (tick % 10 == 0 && tick != 0) {`  
		    `y++; // Move downward`  
		`}`
	This change is implemented to:
	1. Descending Enemies
	2. Powerups
	
	************************* Important
	In doing so - despite abiding to the javadocs - fails a gradescope test as it wants the enemies to move every 5 ticks, with the initial tick inclusive

16. [x] General limitations to GameModel:
	Game.GameModel
	1. Implement 'statsTracker' input for gameModel 
	2. Implement Verbose variable, and getVerbose method
	3. Implement isGameOver method
	4. Add recordShotFired to fireBullet
	5. Add recordShotHit to collision
	6. Implement achievements on each tick
17. [x] General limitations to GameController
	GameGameController
	1. Implement getModel
	2. Implement getStatsTracker
		1. Requires implementing gameModel.getStatsTracker.
	3. Implement RefreshAchievements
		1. Catch the edge case of going over-progress of achievements
	4. Implement fix of two bullets hitting an object at the same time
18. [x] GameModel.spawnobjects must not spawn when another object occupies space
	Game.GameModel#spawnObjects
	Fix:
	- Check if an object is in a space before spawning
	- Created a new method to return a Boolean if an object already is occupying the space
	- ![](Pasted%20image%2020250520124649.png)
	- Implemented a check before each spawning action, ensuring no object is at that position
	- ![](Pasted%20image%2020250520124954.png)
	- 
19. [x] Remove the logged output when firing a bullet
	Game.GameModel#fireBullet()
	- Fix: Remove the following line from gameModel.fireBullet()
		![](Pasted%20image%2020250519144008.png)


------
## Component 3: JUnit Tests

### Game Controller:
1. Test that the game is paused
2. Test that the ship cannot move out of the boundaries
3. Test that the correct exception is thrown for out-of-bounds
4. Test that the user cannot move when the game is paused
	** Removed TestPaused due to gradescope failing to compile the isPaused() method
	![](Pasted%20image%2020250521124137.png)

### Game Model:
1. Test that the model can level up
2. Test that the enemies are removed on collision
### Game.Core:
#### ObjectWithPosition
1. Check that the string omitted is in the correct format
	![](Pasted%20image%2020250521115830.png)
2. Check that x and y positions are correct
	![](Pasted%20image%2020250521115848.png)





------

## Component 4: Implementation

1. [x] Achievement package must be implemented
	1. Implement Achievement interface
		``` 
		public interface Achievement { ... }
		```
	2. Implement AchievementFile interface
	3. Implement AchievementManager class
	4. Implement GameAchievement class
	5. Implement FileHandler class
	6. Implement PlayerStats tracker
2. [x] Implement Assets folder
3. [x] Implement fix to check collistion
	1. Remove bullet when collide with asteroid
