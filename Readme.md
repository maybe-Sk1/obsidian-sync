
1. [ ] Achievement package must be implemented
	1. Implement Achievement interface
		``` 
		public interface Achievement { ... }
		```
	2. Implement AchievementFile interface
	3. Implement AchievementManager class
	4. Implement GameAchievement class
	5. Implement FileHandler class
	6. Implement PlayerStats tracker
2. [ ] Implement missing methods in GameModel
	1. Implement 'statsTracker' input for gameModel 
	2. Implement Verbose variable, and getVerbose method
	3. Implement isGameOver method
	4. Implement 
	5. Add recordShotFired to fireBullet
	6. Add recordShotHit to collision
	7. Implement achievements on each tick
	8. Imple
3. [ ] Implement missing methods in GameController
	1. Implement getModel
		1. 
			
	2. Implement getStatsTracker
		1. Requires implementing gameModel.getStatsTracker.
	3. Implement RefreshAchievements
		1. Catch the edge case of going over-progress of achievements
	4. Implement fix of two bullets hitting an object at the same time
	5. 
4. [ ] Implement Assets folder
5. [ ] Implement fix to check collistion
	1. Remove bullet when collide with asteroid
6. [ ] Implement 

------------------------

List of bugs:
1. [ ] Entity phasing on tick
	Entities to fix:
	1. Bullet x Enemy
		1. Implement y || y+1
	2. Bullet x Asteroid
		1. Same as Enemy
	3. Ship x Enemy
		1. Not implemented, as it would result in a worse bug
	4. Ship x Asteroid
		1. Same as Enemy
		
	Fix method:
	When a bullet is fired, both it and the moving enemies are dependent on the current tick
		![[Pasted image 20250518182301.png]]
	But it is this very fact, that results in an issue when **both objects move in the same tick**. For example:
		![[Comparison.png]]
	Given that the only result of this bug is the bullet being one position above the enemy, we can fix this by 
	
3. [ ] Press enter to start
4. [ ] No area handling for bountry exception
5. [ ] Two bullets hitting an object at the same time
	1. Destroy both bullets due to interpretation of javadocs
6. [ ] Time elapsed while game is paused
