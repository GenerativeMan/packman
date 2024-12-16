# packman
pack eat Man
import pygame
import sys
import random

# Ініціалізація Pygame
pygame.init()

# Параметри вікна
WIDTH, HEIGHT = 800, 600
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Pac-Man")

# Кольори
BLACK = (0, 0, 0)
YELLOW = (255, 255, 0)
BLUE = (0, 0, 255)
RED = (255, 0, 0)
WHITE = (255, 255, 255)

# FPS і таймер
FPS = 60
clock = pygame.time.Clock()

# Параметри гравця (Pac-Man)
pacman_size = 40
pacman_x = WIDTH // 2
pacman_y = HEIGHT // 2
pacman_speed = 5

# Вороги (примари)
ghost_size = 40
ghosts = []
for _ in range(4):
    ghost_x = random.randint(0, WIDTH - ghost_size)
    ghost_y = random.randint(0, HEIGHT - ghost_size)
    ghost_speed = random.choice([2, 3])
    ghosts.append({'rect': pygame.Rect(ghost_x, ghost_y, ghost_size, ghost_size), 'speed': ghost_speed})

# Точки для збирання (схожі на "пельмені")
dot_radius = 5
dots = []
for _ in range(50):
    dot_x = random.randint(dot_radius, WIDTH - dot_radius)
    dot_y = random.randint(dot_radius, HEIGHT - dot_radius)
    dots.append(pygame.Rect(dot_x, dot_y, dot_radius * 2, dot_radius * 2))

# Рахунок
score = 0
font = pygame.font.SysFont(None, 35)

def display_score():
    score_text = font.render(f"Score: {score}", True, WHITE)
    screen.blit(score_text, (10, 10))

# Основний цикл гри
def game_loop():
    global pacman_x, pacman_y, score

    running = True
    while running:
        screen.fill(BLACK)

        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()

        # Керування гравцем
        keys = pygame.key.get_pressed()
        if keys[pygame.K_LEFT]:
            pacman_x -= pacman_speed
        if keys[pygame.K_RIGHT]:
            pacman_x += pacman_speed
        if keys[pygame.K_UP]:
            pacman_y -= pacman_speed
        if keys[pygame.K_DOWN]:
            pacman_y += pacman_speed

        # Обмеження руху гравця
        if pacman_x < 0:
            pacman_x = 0
        if pacman_x > WIDTH - pacman_size:
            pacman_x = WIDTH - pacman_size
        if pacman_y < 0:
            pacman_y = 0
        if pacman_y > HEIGHT - pacman_size:
            pacman_y = HEIGHT - pacman_size

        # Рух привидів
        for ghost in ghosts:
            ghost_rect = ghost['rect']
            ghost_speed = ghost['speed']

            if ghost_rect.x < pacman_x:
                ghost_rect.x += ghost_speed
            elif ghost_rect.x > pacman_x:
                ghost_rect.x -= ghost_speed

            if ghost_rect.y < pacman_y:
                ghost_rect.y += ghost_speed
            elif ghost_rect.y > pacman_y:
                ghost_rect.y -= ghost_speed

            # Перевірка на зіткнення з Pac-Man
            if ghost_rect.colliderect(pygame.Rect(pacman_x, pacman_y, pacman_size, pacman_size)):
                running = False

        # Перевірка на зіткнення з точками
        pacman_rect = pygame.Rect(pacman_x, pacman_y, pacman_size, pacman_size)
        for dot in dots[:]:
            if pacman_rect.colliderect(dot):
                dots.remove(dot)
                score += 10

        # Малювання Pac-Man
        pygame.draw.circle(screen, YELLOW, (pacman_x + pacman_size // 2, pacman_y + pacman_size // 2), pacman_size // 2)

        # Малювання привидів
        for ghost in ghosts:
            pygame.draw.rect(screen, RED, ghost['rect'])

        # Малювання точок
        for dot in dots:
            pygame.draw.circle(screen, BLUE, (dot.x + dot_radius, dot.y + dot_radius), dot_radius)

        # Відображення рахунку
        display_score()

        # Оновлення екрану
        pygame.display.flip()
        clock.tick(FPS)

    # Кінець гри
    screen.fill(BLACK)
    game_over_text = font.render("Game Over! Press R to Restart or Q to Quit", True, WHITE)
    screen.blit(game_over_text, (WIDTH // 2 - 200, HEIGHT // 2))
    pygame.display.flip()

    while True:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_r:
                    main()
                if event.key == pygame.K_q:
                    pygame.quit()
                    sys.exit()

def main():
    game_loop()

if __name__ == "__main__":
    main()
