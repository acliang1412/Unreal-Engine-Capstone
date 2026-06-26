# LMS Tech Academy: UE5 First-Person Game Capstone

---

## User Story 1 -- Basic Level with Structures

### Blueprints Created / Modified
* L_Main (Level)

### What Was Built
A playable first-person level was created using the Unreal Engine 5 editor. The core environment layout was established using the SM_Scene static mesh asset from the White Castle pack to form the main structural design. The level was further blocked out and refined using BSP brushes and additional Static Mesh actors, including walls, archways, floors, and staircase pieces. A landscape or terrain base was added to give the level ground to stand on. Foliage and props were placed throughout the level to add visual detail and make the space feel inhabited. A Player Start actor was placed in the level to define where the player spawns when the game begins.
Staircase navigation was solved by placing Blocking Volume actors over the stair meshes and rotating them to act as invisible ramps, allowing the player to walk up and down the steps smoothly using the character's built-in movement system.

### Completion Condition
The level was complete when a player could spawn at the Player Start, walk through the environment, navigate up and down staircases, and explore the space without falling through the floor or getting stuck on geometry.
<img width="1928" height="1040" alt="image" src="https://github.com/user-attachments/assets/4041cbf5-0dfa-41e6-b6be-a4ea770cc77d" />
<img width="1449" height="885" alt="image" src="https://github.com/user-attachments/assets/46ad993e-55eb-4636-8ce5-ebc568e7deda" />
<img width="647" height="656" alt="image" src="https://github.com/user-attachments/assets/87c5f966-81a9-483d-b791-5f3cfc851c1b" />

---

## User Story 2 -- Player in the Level with a Basic HUD

### Blueprints Created / Modified
* BP_GameMode (Created)
* WBP_HUD (Created)
* BP_FirstPersonCharacter (Modified)

### What Was Built
A custom Game Mode Blueprint called BP_GameMode was created with GameModeBase as its parent class. BP_GameMode was set to use the First Person Template's BP_FirstPersonCharacter as the default pawn, so the player spawns with first-person movement and camera already working.

On BeginPlay, BP_GameMode creates an instance of WBP_HUD using the Create Widget node, stores it in a HUDWidget variable, and adds it to the player's viewport using Add to Viewport. It then gets the player controller, sets input mode to Game Only, and hides the mouse cursor so the player has full control of the character immediately.

WBP_HUD was built as a UMG Widget Blueprint containing a health progress bar and a collectible counter text block. The health bar reads from CurrentHealth and MaxHealth variables. The collectible counter displays how many coins have been collected out of the total. BP_GameMode also calls SetHUDRef on the character after spawning, passing the HUD widget reference so the character can update the health bar when it takes damage.

### Completion Condition
The HUD was complete when the health bar and collectible counter both appeared on screen immediately when the level loaded, and the player could move and look around freely without the mouse cursor interfering.
<img width="1928" height="1040" alt="image" src="https://github.com/user-attachments/assets/0dd58e7b-6b53-4e4c-81b8-1c1fce6d9c64" />

---

## User Story 3 -- Collectibles and Obstacles

### Blueprints Created / Modified
* BP_Collectible (Created)
* BP_GameMode (Modified -- CollectCoin function)
* BP_FirstPersonCharacter (Modified -- AnyDamage event)
* WBP_HUD (Modified -- UpdateHealth and UpdateCollectiblesText functions)

### What Was Built
* Collectibles: BP_Collectible is an Actor Blueprint with a Static Mesh component for visuals and a Sphere Collision component that detects when the player walks into it. When the player overlaps the sphere, the OnComponentBeginOverlap event fires. It casts the overlapping actor to Character to confirm it is the player, then gets the Game Mode and calls the CollectCoin function on BP_GameMode.
  
  The CollectCoin function increments the CoinsCollected integer variable by 1, then casts the HUDWidget reference to WBP_HUD and calls UpdateCollectiblesText, passing the new collected count and the total coin count. After calling CollectCoin, BP_Collectible plays the MS_Coin sound effect at the coin's world location using Play Sound at Location, then destroys itself using Destroy Actor. Five BP_Collectible actors were placed in L_Main.

* Obstacles: Pain Causing Volume actors were placed in the level over hazardous areas. Each volume was configured with a Damage Per Second value and a Pain Interval of 1.0 seconds so damage fires once per second. When the player stands inside a volume, UE5 automatically calls the TakeDamage event on the player actor.

  BP_FirstPersonCharacter was given CurrentHealth and MaxHealth float variables both defaulting to 100. An Event AnyDamage node was added to the EventGraph. When damage is received, it subtracts the damage amount from CurrentHealth, clamps the result between 0 and MaxHealth, stores the new value, then casts the stored HUD reference to WBP_HUD and calls UpdateHealth, which updates the health bar's percentage display. A Branch node then checks if CurrentHealth is less than or equal to zero to trigger the lose condition.

### Completion Condition
The story was complete when picking up a coin incremented the HUD counter and played a sound, and standing in a Pain Volume caused the health bar to visibly decrease over time.
<img width="1344" height="323" alt="image" src="https://github.com/user-attachments/assets/5f9ce061-37c1-4b6b-b020-f7b5a4b134ff" />

---

## User Story 4 -- Main Menu, Victory Screen, and Game Over Screen

### Blueprints Created / Modified
* WBP_MainMenu (Created)
* WBP_Victory (Created)
* WBP_GameOver (Created)
* BP_MainMenuGameMode (Created)
* L_MainMenu (Created)

### What Was Built
Three UMG Widget Blueprints were created for the game's menu screens.

* WBP_MainMenu contains a title text block and two buttons -- Play and Quit. The Play button's OnClicked event calls Open Level by Name with L_Main as the target, loading the main game level. The Quit button calls Quit Game.
* WBP_Victory contains a "YOU WIN!" title, a subtitle, and three buttons -- Play Again, Main Menu, and Quit. Play Again calls Open Level with L_Main to restart the level. Main Menu calls Open Level with L_MainMenu to return to the start screen. Quit calls Quit Game.
* WBP_GameOver contains a "GAME OVER" title, a subtitle, and the same three buttons wired identically to WBP_Victory but with Try Again instead of Play Again.

A separate Game Mode called BP_MainMenuGameMode was created for the menu level. Its BeginPlay creates WBP_MainMenu, adds it to the viewport, shows the mouse cursor, and sets input mode to UI Only so the player can click the buttons. L_MainMenu is an empty level assigned to use BP_MainMenuGameMode, and it is set as the project's default startup map.

### Completion Condition
The story was complete when the game launched into the main menu, the Play button successfully loaded L_Main, and both the Victory and Game Over screens displayed with working buttons.
<img width="235" height="222" alt="image" src="https://github.com/user-attachments/assets/e54fa5cc-1106-476c-b90c-3bd0b21daa0f" />
<img width="371" height="262" alt="image" src="https://github.com/user-attachments/assets/b813ab85-67e9-4914-8a10-a43e3aeaf5bc" />

---

## User Story 5 -- Win and Lose Conditions

### Blueprints Created / Modified
* BP_GameMode (Modified -- ShowVictory and ShowGameOver custom events)
* BP_FirstPersonCharacter (Modified -- AnyDamage branch wired to GameMode)
* BP_GameMode (Modified -- CollectCoin function win check added)

### What Was Built
Two custom events were added to BP_GameMode's EventGraph -- ShowVictory and ShowGameOver. Both follow the same pattern: create the appropriate widget (WBP_Victory or WBP_GameOver), add it to the viewport, call Set Game Paused with true to freeze the game, show the mouse cursor on the player controller, and set input mode to UI Only so the player can click the screen buttons.

* Lose Condition: At the end of the AnyDamage event in BP_FirstPersonCharacter, a Branch node checks if CurrentHealth is less than or equal to zero. If true, it gets the Game Mode, casts it to BP_GameMode, and calls the ShowGameOver custom event. This causes the Game Over screen to appear and the game to pause.
* Win Condition: At the end of the CollectCoin function in BP_GameMode, a Branch node checks if CoinsCollected is greater than or equal to TotalCoins. If true, it calls the ShowVictory custom event directly on self. This causes the Victory screen to appear and the game to pause.

The TotalCoins variable was set to 5 to match the number of BP_Collectible actors placed in L_Main.

### Completion Condition
The story was complete when the full game loop worked end-to-end -- starting at the Main Menu, pressing Play to load L_Main, collecting all 5 coins to trigger the Victory screen, pressing Play Again to restart, then letting health drain to zero in a Pain Volume to trigger the Game Over screen, and pressing Try Again to restart again. All transitions worked correctly and the game paused on both end screens.
