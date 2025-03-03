import pygame
import random
import os

# Initialize pygame
pygame.init()

# Game Constants
WIDTH, HEIGHT = 800, 600
TILE_SIZE = 40
FPS = 60

# Colors
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
RED = (255, 0, 0)
GREEN = (0, 255, 0)

# Player Settings
PLAYER_SPEED = 5
BULLET_SPEED = 10
MAX_AMMO = 10
MAX_HEALTH = 100

# Enemy Settings
ENEMY_SPEED = 2

# Initialize Screen
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Random Level Game")
clock = pygame.time.Clock()

# Load Assets
player_img = pygame.Surface((40, 40))
player_img.fill(GREEN)

enemy_img = pygame.Surface((40, 40))
enemy_img.fill(RED)

# Classes
class Player(pygame.sprite.Sprite):
    def __init__(self, x, y):
        super().__init__()
        self.image = player_img
        self.rect = self.image.get_rect()
        self.rect.topleft = (x, y)
        self.health = MAX_HEALTH
        self.ammo = MAX_AMMO
        self.score = 0
    
    def update(self, keys):
        if keys[pygame.K_LEFT]:
            self.rect.x -= PLAYER_SPEED
        if keys[pygame.K_RIGHT]:
            self.rect.x += PLAYER_SPEED
        if keys[pygame.K_UP]:
            self.rect.y -= PLAYER_SPEED
        if keys[pygame.K_DOWN]:
            self.rect.y += PLAYER_SPEED

class Enemy(pygame.sprite.Sprite):
    def __init__(self, x, y):
        super().__init__()
        self.image = enemy_img
        self.rect = self.image.get_rect()
        self.rect.topleft = (x, y)
    
    def update(self):
        self.rect.y += ENEMY_SPEED  # Move enemy downward

# Level Generation
def generate_level():
    enemies = pygame.sprite.Group()
    for _ in range(5):
        x = random.randint(0, WIDTH - TILE_SIZE)
        y = random.randint(0, HEIGHT // 2)
        enemies.add(Enemy(x, y))
    return enemies

# Scoreboard System
def save_score(username, score):
    with open("scores.txt", "a") as file:
        file.write(f"{username}: {score}\n")

def load_scores():
    if not os.path.exists("scores.txt"):
        return []
    with open("scores.txt", "r") as file:
        return file.readlines()

# Game Loop
player = Player(WIDTH // 2, HEIGHT - 60)
enemies = generate_level()
running = True

while running:
    clock.tick(FPS)
    screen.fill(WHITE)
    
    # Events
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    
    # Update
    keys = pygame.key.get_pressed()
    player.update(keys)
    enemies.update()
    
    # Draw
    screen.blit(player.image, player.rect)
    enemies.draw(screen)
    
    # Display Stats
    font = pygame.font.SysFont(None, 24)
    health_text = font.render(f"Health: {player.health}", True, BLACK)
    ammo_text = font.render(f"Ammo: {player.ammo}", True, BLACK)
    score_text = font.render(f"Score: {player.score}", True, BLACK)
    screen.blit(health_text, (10, 10))
    screen.blit(ammo_text, (10, 30))
    screen.blit(score_text, (10, 50))
    
    pygame.display.flip()

pygame.quit()
