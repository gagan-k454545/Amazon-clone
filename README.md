6
import pygame
import sys
import random

# Initialize Pygame
pygame.init()

# Constants
WIDTH, HEIGHT = 800, 600
WHITE = (255, 255, 255)
PLAYER_COLOR = (0, 100, 255)
ENEMY_COLOR = (255, 50, 50)

# Screen setup
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Dodge the enemy")
clock = pygame.time.Clock()
font = pygame.font.SysFont(None, 36)

# Player setup
player_size = 50
player_pos = [WIDTH // 2, HEIGHT // 2]
player_speed = 5

# Enemy setup
enemy_size = 50
enemy_pos = [random.randint(0, WIDTH - enemy_size), random.randint(0, HEIGHT - enemy_size)]
enemy_speed = 3

# Score setup
start_ticks = pygame.time.get_ticks()

# Movement logic for enemy
def move_enemy(enemy_pos, player_pos):
    if enemy_pos[0] < player_pos[0]:
        enemy_pos[0] += enemy_speed
    elif enemy_pos[0] > player_pos[0]:
        enemy_pos[0] -= enemy_speed

    if enemy_pos[1] < player_pos[1]:
        enemy_pos[1] += enemy_speed
    elif enemy_pos[1] > player_pos[1]:  # <-- Fixed logic bug here (was repeating < instead of >)
        enemy_pos[1] -= enemy_speed

# Collision detection
def detect_collision(p_pos, e_pos):
    px, py = p_pos
    ex, ey = e_pos
    return (
        px < ex + enemy_size and
        px + player_size > ex and
        py < ey + enemy_size and
        py + player_size > ey
    )

# Game loop
running = True
while running:
    clock.tick(60)
    screen.fill(WHITE)

    # Handle events
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

    # Handle player movement
    keys = pygame.key.get_pressed()
    if keys[pygame.K_LEFT] and player_pos[0] > 0:
        player_pos[0] -= player_speed
    if keys[pygame.K_RIGHT] and player_pos[0] < WIDTH - player_size:
        player_pos[0] += player_speed
    if keys[pygame.K_UP] and player_pos[1] > 0:
        player_pos[1] -= player_speed
    if keys[pygame.K_DOWN] and player_pos[1] < HEIGHT - player_size:
        player_pos[1] += player_speed

    # Update enemy position
    move_enemy(enemy_pos, player_pos)

    # Draw player and enemy
    pygame.draw.rect(screen, PLAYER_COLOR, (*player_pos, player_size, player_size))
    pygame.draw.rect(screen, ENEMY_COLOR, (*enemy_pos, enemy_size, enemy_size))

    # Display score
    seconds = (pygame.time.get_ticks() - start_ticks) // 1000
    score_text = font.render(f"Score: {seconds}", True, (0, 0, 0))
    screen.blit(score_text, (10, 10))

    # Check for collision
    if detect_collision(player_pos, enemy_pos):
        game_over_text = font.render("Game Over!!", True, (200, 0, 0))
        screen.blit(game_over_text, (WIDTH // 2 - 80, HEIGHT // 2))
        pygame.display.flip()
        pygame.time.wait(2000)
        running = False

    pygame.display.flip()

# Exit
pygame.quit()
sys.exit()