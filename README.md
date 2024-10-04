# Ex.No: 10   Mini Project 
### DATE:                                                                           
### REGISTER NUMBER : 212222110022
### AIM: 

To develop a Flappy Bird game using Python's pygame library, integrated with asyncio for handling game states asynchronously. 
The game will feature a player-controlled bird that must navigate through obstacles, accumulating points, while smoothly 
transitioning between game states such as splash, play, and game over.

### Steps:
1. Set up the environment: Install Python and pygame, and create the project structure with src/ folder for the main game files.

2. Create core game components: Develop classes for the player, pipes, background, score, and game screens.

3. Implement game logic: Code the game states (splash, play, game over) in the Flappy class using asynchronous functions.

4. Handle user input: Capture mouse, keyboard, and screen tap events for controlling the bird and quitting the game.

5. Test and debug: Run the game, ensure smooth transitions, and fix any issues with collision detection or user inputs.

### Program:
```
## Flappy:
import asyncio
import sys

import pygame
from pygame.locals import K_ESCAPE, K_SPACE, K_UP, KEYDOWN, QUIT

from .entities import (
    Background,
    Floor,
    GameOver,
    Pipes,
    Player,
    PlayerMode,
    Score,
    WelcomeMessage,
)
from .utils import GameConfig, Images, Sounds, Window


class Flappy:
    def __init__(self):
        pygame.init()
        pygame.display.set_caption("Flappy Bird")
        window = Window(288, 512)
        screen = pygame.display.set_mode((window.width, window.height))
        images = Images()

        self.config = GameConfig(
            screen=screen,
            clock=pygame.time.Clock(),
            fps=30,
            window=window,
            images=images,
            sounds=Sounds(),
        )

    async def start(self):
        while True:
            self.background = Background(self.config)
            self.floor = Floor(self.config)
            self.player = Player(self.config)
            self.welcome_message = WelcomeMessage(self.config)
            self.game_over_message = GameOver(self.config)
            self.pipes = Pipes(self.config)
            self.score = Score(self.config)
            await self.splash()
            await self.play()
            await self.game_over()

    async def splash(self):
        """Shows welcome splash screen animation of flappy bird"""

        self.player.set_mode(PlayerMode.SHM)

        while True:
            for event in pygame.event.get():
                self.check_quit_event(event)
                if self.is_tap_event(event):
                    return

            self.background.tick()
            self.floor.tick()
            self.player.tick()
            self.welcome_message.tick()

            pygame.display.update()
            await asyncio.sleep(0)
            self.config.tick()

    def check_quit_event(self, event):
        if event.type == QUIT or (
            event.type == KEYDOWN and event.key == K_ESCAPE
        ):
            pygame.quit()
            sys.exit()

    def is_tap_event(self, event):
        m_left, _, _ = pygame.mouse.get_pressed()
        space_or_up = event.type == KEYDOWN and (
            event.key == K_SPACE or event.key == K_UP
        )
        screen_tap = event.type == pygame.FINGERDOWN
        return m_left or space_or_up or screen_tap

    async def play(self):
        self.score.reset()
        self.player.set_mode(PlayerMode.NORMAL)

        while True:
            if self.player.collided(self.pipes, self.floor):
                return

            for i, pipe in enumerate(self.pipes.upper):
                if self.player.crossed(pipe):
                    self.score.add()

            for event in pygame.event.get():
                self.check_quit_event(event)
                if self.is_tap_event(event):
                    self.player.flap()

            self.background.tick()
            self.floor.tick()
            self.pipes.tick()
            self.score.tick()
            self.player.tick()

            pygame.display.update()
            await asyncio.sleep(0)
            self.config.tick()

    async def game_over(self):
        """crashes the player down and shows gameover image"""

        self.player.set_mode(PlayerMode.CRASH)
        self.pipes.stop()
        self.floor.stop()

        while True:
            for event in pygame.event.get():
                self.check_quit_event(event)
                if self.is_tap_event(event):
                    if self.player.y + self.player.h >= self.floor.y - 1:
                        return

            self.background.tick()
            self.floor.tick()
            self.pipes.tick()
            self.score.tick()
            self.player.tick()
            self.game_over_message.tick()

            self.config.tick()
            pygame.display.update()
            await asyncio.sleep(0)
```
```
## Main:

import asyncio

from src.flappy import Flappy

if __name__ == "__main__":
    asyncio.run(Flappy().start())
```

### Output:

![Screenshot 2024-10-04 103908](https://github.com/user-attachments/assets/1bd702bd-f96e-4274-a1ca-03a40d92eb31)

![Screenshot 2024-10-04 103921](https://github.com/user-attachments/assets/85b70107-c0bb-4e6f-b18b-150e1b675e7f)

![Screenshot 2024-10-04 103932](https://github.com/user-attachments/assets/12fd4bbe-ca0d-4713-989e-2a544588feaa)



### Result:
The Flappy Bird game was successfully created with smooth transitions and functional gameplay using pygame and asyncio. 
User input and game events are handled seamlessly for an engaging experience.
