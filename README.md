import pygame
import random

# Initialiseren van Pygame
pygame.init()

# Instellingen
WIDTH, HEIGHT = 400, 600
GRID_SIZE = 20
GRID_WIDTH, GRID_HEIGHT = WIDTH // GRID_SIZE, HEIGHT // GRID_SIZE
GRID = [[0 for _ in range(GRID_WIDTH)] for _ in range(GRID_HEIGHT)]
WHITE = (255, 255, 255)
SHAPES = [
    [[1, 1, 1, 1]],
    [[1, 1], [1, 1]],
    [[1, 1, 1], [0, 1, 0]],
    [[1, 1, 1], [1, 0, 0]],
    [[1, 1, 1], [0, 0, 1]],
    [[1, 1, 1], [0, 1, 0]],
    [[1, 1, 1], [1, 0, 0]]
]

# Pygame-venster
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Tetris")

# Functie om een nieuwe vorm te genereren
def new_shape():
    shape = random.choice(SHAPES)
    color = (random.randint(0, 255), random.randint(0, 255), random.randint(0, 255))
    return shape, color, GRID_WIDTH // 2 - len(shape[0]) // 2, 0

# Functie om een vorm op het raster te tekenen
def draw_shape(shape, x, y, color):
    for row in range(len(shape)):
        for col in range(len(shape[row]):
            if shape[row][col]:
                pygame.draw.rect(screen, color, (x + col * GRID_SIZE, y + row * GRID_SIZE, GRID_SIZE, GRID_SIZE))
                pygame.draw.rect(screen, WHITE, (x + col * GRID_SIZE, y + row * GRID_SIZE, GRID_SIZE, GRID_SIZE), 2)

# Functie om een vorm te controleren op botsingen
def check_collision(shape, x, y):
    for row in range(len(shape)):
        for col in range(len(shape[row]):
            if shape[row][col]:
                if x + col < 0 or x + col >= GRID_WIDTH or y + row >= GRID_HEIGHT or GRID[y + row][x + col]:
                    return True
    return False

# Functie om een rij te controleren op volledigheid
def check_line_complete(line):
    for cell in line:
        if cell == 0:
            return False
    return True

# Functie om een rij te verwijderen en het raster te verschuiven
def remove_line(line):
    del GRID[line]
    return [[0 for _ in range(GRID_WIDTH)]] + GRID

# Spelvariabelen
current_shape, current_color, current_x, current_y = new_shape()
score = 0

# Spelloop
running = True
while running:
    screen.fill((0, 0, 0))

    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_LEFT:
                if not check_collision(current_shape, current_x - 1, current_y):
                    current_x -= 1
            elif event.key == pygame.K_RIGHT:
                if not check_collision(current_shape, current_x + 1, current_y):
                    current_x += 1
            elif event.key == pygame.K_DOWN:
                if not check_collision(current_shape, current_x, current_y + 1):
                    current_y += 1

    # Beweeg de huidige vorm naar beneden
    if not check_collision(current_shape, current_x, current_y + 1):
        current_y += 1
    else:
        # De vorm kan niet verder naar beneden, dus voeg hem toe aan het raster
        for row in range(len(current_shape)):
            for col in range(len(current_shape[row]):
                if current_shape[row][col]:
                    GRID[current_y + row][current_x + col] = current_color

        # Controleer op voltooide rijen en verwijder deze
        complete_lines = [i for i, row in enumerate(GRID) if check_line_complete(row)]
        if complete_lines:
            score += len(complete_lines)
            GRID = remove_line(complete_lines[0])  # Verwijder alleen de eerste voltooide lijn

        # Genereer een nieuwe vorm
        current_shape, current_color, current_x, current_y = new_shape()

        # Controleer of de nieuwe vorm direct na het genereren botst
        if check_collision(current_shape, current_x, current_y):
            # Het spel is afgelopen
            running = False

    # Teken de huidige vorm en het raster
    draw_shape(current_shape, current_x * GRID_SIZE, current_y * GRID_SIZE, current_color)
    for row in range(GRID_HEIGHT):
        for col in range(GRID_WIDTH):
            if GRID[row][col]:
                draw_shape([[1]], col * GRID_SIZE, row * GRID_SIZE, GRID[row][col])

    pygame.display.update()

pygame.quit()

