import pygame
import random

pygame.init()

BLACK = (0, 0, 0)
RED = (213, 50, 80)
GREEN = (0, 255, 0)
BLUE = (50, 153, 213)

WIDTH = 800
HEIGHT = 600
SNAKE_BLOCK = 10
NORMAL_SPEED = 15

dis = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption('Snake Trial')

clock = pygame.time.Clock()

font_style = pygame.font.SysFont(None, 50)

def message(msg, color):
    msg = font_style.render(msg, True, color)
    dis.blit(msg, [WIDTH / 6, HEIGHT / 3])

def show_score(score):
    score_font = font_style.render("Score: " + str(score), True, BLACK)
    dis.blit(score_font, (10, 10))

def our_snake(snake_block, snake_list):
    for x in snake_list:
        pygame.draw.rect(dis, BLACK, [x[0], x[1], snake_block, snake_block])

def draw_obstacles(obstacles):
    for obstacle in obstacles:
        pygame.draw.rect(dis, RED, [obstacle[0], obstacle[1], SNAKE_BLOCK, SNAKE_BLOCK])

def game_loop():
    snake_speed = NORMAL_SPEED
    score = 0

    game_over = False
    game_close = False

    x1 = WIDTH / 2
    y1 = HEIGHT / 2

    x1_change = 0
    y1_change = 0

    snake_list = []
    length_of_snake = 4

    foodx = round(random.randrange(0, WIDTH - SNAKE_BLOCK) / 10.0) * 10.0
    foody = round(random.randrange(0, HEIGHT - SNAKE_BLOCK) / 10.0) * 10.0

    obstacles = []
    for _ in range(50):
        obstacle_x = round(random.randrange(0, WIDTH - SNAKE_BLOCK) / 10.0) * 10.0
        obstacle_y = round(random.randrange(0, HEIGHT- SNAKE_BLOCK) / 10.0) * 10.0
        obstacles.append((obstacle_x, obstacle_y))

    while not game_over:
        while game_close:
            dis.fill(BLUE)
            message("You lost! R = Restart Q = Quit", RED)

            final_score_text = font_style.render("Final Score: " + str(score), True, BLACK)
            dis.blit(final_score_text, [WIDTH / 6, HEIGHT / 3 + 70])

            pygame.display.update()

            for event in pygame.event.get():
                if event.type == pygame.KEYDOWN:
                    if event.key == pygame.K_q:
                        game_over = True
                        game_close = False
                    if event.key == pygame.K_r:
                        score = 0
                        snake_speed = NORMAL_SPEED
                        game_loop()

        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                game_over = True
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_a:
                    x1_change = -SNAKE_BLOCK
                    y1_change = 0
                elif event.key == pygame.K_d:
                    x1_change = SNAKE_BLOCK
                    y1_change = 0
                elif event.key == pygame.K_w:
                    y1_change = -SNAKE_BLOCK
                    x1_change = 0
                elif event.key == pygame.K_s:
                    y1_change = SNAKE_BLOCK
                    x1_change = 0
                    
        if x1 >= WIDTH or x1 < 0 or y1 >= HEIGHT or y1 < 0:
            game_close = True

        for obstacle in obstacles:
            if (x1, y1) == obstacle:
                game_close = True

        x1 += x1_change
        y1 += y1_change
        dis.fill(BLUE)
        draw_obstacles(obstacles)
        snake_head = [x1, y1]
        snake_list.append(snake_head)
        if len(snake_list) > length_of_snake:
            del snake_list[0]

        our_snake(SNAKE_BLOCK, snake_list)
        pygame.draw.rect(dis, GREEN, [foodx, foody, SNAKE_BLOCK, SNAKE_BLOCK])

        if x1 == foodx and y1 == foody:
            foodx = round(random.randrange(0, WIDTH - SNAKE_BLOCK) / 10.0) * 10.0
            foody = round(random.randrange(0, HEIGHT - SNAKE_BLOCK) / 10.0) * 10.0
            length_of_snake += 1
            score += 1
            snake_speed += 2

        if not game_close:
            show_score(score)
        pygame.display.update()
        clock.tick(snake_speed)

    pygame.quit()
    quit()

game_loop()
