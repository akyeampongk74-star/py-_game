# py _game
simple runner and jump game
import pygame, random, sys

pygame.init()
WIDTH, HEIGHT = 800, 400
screen = pygame.display.set_mode((WIDTH, HEIGHT))
clock = pygame.time.Clock()
font = pygame.font.SysFont(None, 48)

WHITE = (255,255,255)
BLACK = (0,0,0)
GREEN = (50,205,50)
BLUE = (30,144,255)
RED = (255,69,58)

player = pygame.Rect(100, 300, 50, 50)
player_vel = 0
gravity = 1
jump_power = -18

obstacles = []
spawn_timer = 0
score = 0
game_over = False

def reset_game():
    global player, obstacles, score, game_over, player_vel
    player.y = 300
    obstacles.clear()
    score = 0
    game_over = False
    player_vel = 0

def draw_text(text, x, y, color=BLACK):
    img = font.render(text, True, color)
    screen.blit(img, (x, y))

while True:
    screen.fill(WHITE)

    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
            sys.exit()
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_SPACE and not game_over:
                if player.bottom >= 350:
                    player_vel = jump_power
            if event.key == pygame.K_r and game_over:
                reset_game()

    if not game_over:
        player_vel += gravity
        player.y += player_vel
        if player.bottom >= 350:
            player.bottom = 350

        spawn_timer += 1
        if spawn_timer > 60:
            spawn_timer = 0
            obstacles.append(pygame.Rect(WIDTH, 320, 40, 40))

        for ob in obstacles:
            ob.x -= 7

        obstacles = [ob for ob in obstacles if ob.x > -50]

        for ob in obstacles:
            if player.colliderect(ob):
                game_over = True

        score += 1

        pygame.draw.rect(screen, BLUE, player)
        for ob in obstacles:
            pygame.draw.rect(screen, RED, ob)
        pygame.draw.line(screen, GREEN, (0, 350), (WIDTH, 350), 3)
        draw_text(f"Score: {score}", 10, 10)
    else:
        draw_text("GAME OVER", 300, 150, RED)
        draw_text("Press R to Restart", 260, 220, BLACK)
        draw_text(f"Final Score: {score}", 300, 280, BLUE)

    pygame.display.flip()
    clock.tick(30)
