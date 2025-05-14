
import pygame
import random
import sys

pygame.init()
pygame.mixer.init()

# Constants
WIDTH, HEIGHT = 800, 400
FPS = 60
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
NEON_BLUE = (0, 255, 255)
GROUND_HEIGHT = HEIGHT - 40

# Setup
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Neon Runner")
clock = pygame.time.Clock()
font = pygame.font.SysFont("Consolas", 30)

# Sounds
jump_sound = pygame.mixer.Sound("assets/jump.wav")
powerup_sound = pygame.mixer.Sound("assets/powerup.wav")
pygame.mixer.music.load("assets/music.mp3")
pygame.mixer.music.play(-1)

# Player
player = pygame.Rect(100, GROUND_HEIGHT - 50, 30, 50)
player_vel_y = 0
gravity = 1
jump_force = -15

# Trail
trail = []

# Obstacles
obstacle_timer = 0
obstacles = []

# Power-ups
powerups = []

# Score
score = 0

# Game state
running = True
game_over = False
paused = False

def reset_game():
    global player, player_vel_y, obstacles, powerups, score, game_over
    player.y = GROUND_HEIGHT - 50
    player_vel_y = 0
    obstacles.clear()
    powerups.clear()
    score = 0
    game_over = False

def draw_main_menu():
    screen.fill(BLACK)
    title_text = font.render("NEON RUNNER", True, NEON_BLUE)
    start_text = font.render("Press SPACE to Start", True, WHITE)
    screen.blit(title_text, (WIDTH//2 - title_text.get_width()//2, 100))
    screen.blit(start_text, (WIDTH//2 - start_text.get_width()//2, 180))
    pygame.display.flip()

def draw_game():
    screen.fill(BLACK)
    pygame.draw.rect(screen, NEON_BLUE, player)
    
    # Draw trail
    for pos in trail:
        pygame.draw.rect(screen, (0, 100, 255), pos)
    
    for obs in obstacles:
        pygame.draw.rect(screen, (255, 50, 50), obs)
    for power in powerups:
        pygame.draw.rect(screen, (50, 255, 50), power)
    
    score_text = font.render(f"Score: {score}", True, WHITE)
    screen.blit(score_text, (10, 10))
    pygame.display.flip()

def draw_game_over():
    screen.fill(BLACK)
    over_text = font.render("GAME OVER", True, (255, 50, 50))
    retry_text = font.render("Press R to Retry", True, WHITE)
    screen.blit(over_text, (WIDTH//2 - over_text.get_width()//2, 150))
    screen.blit(retry_text, (WIDTH//2 - retry_text.get_width()//2, 220))
    pygame.display.flip()

show_menu = True

# Game loop
while running:
    clock.tick(FPS)
    keys = pygame.key.get_pressed()

    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        if show_menu and event.type == pygame.KEYDOWN and event.key == pygame.K_SPACE:
            show_menu = False
            reset_game()
        if game_over and event.type == pygame.KEYDOWN and event.key == pygame.K_r:
            reset_game()
        if not show_menu and not game_over and event.type == pygame.KEYDOWN and event.key == pygame.K_p:
            paused = not paused

    if show_menu:
        draw_main_menu()
        continue

    if paused or game_over:
        if game_over:
            draw_game_over()
        continue

    # Player jump
    if keys[pygame.K_SPACE] and player.bottom >= GROUND_HEIGHT:
        player_vel_y = jump_force
        jump_sound.play()

    # Physics
    player_vel_y += gravity
    player.y += player_vel_y
    if player.bottom > GROUND_HEIGHT:
        player.bottom = GROUND_HEIGHT

    # Trail effect
    trail.append(player.copy())
    if len(trail) > 15:
        trail.pop(0)

    # Obstacles
    obstacle_timer += 1
    if obstacle_timer > 60:
        obstacle_timer = 0
        if random.random() < 0.7:
            obstacles.append(pygame.Rect(WIDTH, GROUND_HEIGHT - 30, 30, 30))
        else:
            powerups.append(pygame.Rect(WIDTH, GROUND_HEIGHT - 50, 20, 20))

    for obs in obstacles:
        obs.x -= 5
    for power in powerups:
        power.x -= 5

    obstacles = [o for o in obstacles if o.right > 0]
    powerups = [p for p in powerups if p.right > 0]

    # Collision
    for obs in obstacles:
        if player.colliderect(obs):
            game_over = True

    for power in powerups[:]:
        if player.colliderect(power):
            score += 10
            powerup_sound.play()
            powerups.remove(power)

    score += 1
    draw_game()

pygame.quit()
sys.exit()
