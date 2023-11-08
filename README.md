import pygame
import time
import random

# Initialize Pygame
pygame.init()

# Colors
white = (255, 255, 255)
black = (0, 0, 0)
red = (213, 50, 80)
green = (0, 255, 0)
blue = (50, 153, 213)

# Screen dimensions
dis_width = 800
dis_height = 600
dis = pygame.display.set_mode((dis_width, dis_height))
pygame.display.set_caption('Snake Game')

clock = pygame.time.Clock()

snake_block = 10
snake_speed = 15
speed_increase_interval = 10  # Increase speed every 10 seconds
last_speed_increase = time.time()  # Record the time of the last speed increase

font_style = pygame.font.SysFont(None, 50)

score = 0
start_time = time.time()

def message(msg, color):
    mesg = font_style.render(msg, True, color)
    dis.blit(mesg, [dis_width / 6, dis_height / 3])

def show_score(score):
    score_font = font_style.render("Score: " + str(score), True, black)
    dis.blit(score_font, (10, 10))

def show_time(start_time):
    elapsed_time = int(time.time() - start_time)
    time_font = font_style.render("Time: " + str(elapsed_time), True, black)
    dis.blit(time_font, (dis_width - 150, 10))

def our_snake(snake_block, snake_list):
    for x in snake_list:
        pygame.draw.rect(dis, black, [x[0], x[1], snake_block, snake_block])

def draw_obstacles(obstacles):
    for obstacle in obstacles:
        pygame.draw.rect(dis, red, [obstacle[0], obstacle[1], snake_block, snake_block])

def gameLoop():
    global snake_speed, last_speed_increase, score, start_time

    game_over = False
    game_close = False

    x1 = dis_width / 2
    y1 = dis_height / 2

    x1_change = 0
    y1_change = 0

    snake_List = []
    Length_of_snake = 1

    foodx = round(random.randrange(0, dis_width - snake_block) / 10.0) * 10.0
    foody = round(random.randrange(0, dis_height - snake_block) / 10.0) * 10.0

    obstacles = []
    for _ in range(10):
        obstacle_x = round(random.randrange(0, dis_width - snake_block) / 10.0) * 10.0
        obstacle_y = round(random.randrange(0, dis_height - snake_block) / 10.0) * 10.0
        obstacles.append((obstacle_x, obstacle_y))

    while not game_over:
        while game_close == True:
            dis.fill(blue)
            message("You lost! Press C-Play Again or Q-Quit", red)
            pygame.display.update()

            for event in pygame.event.get():
                if event.type == pygame.KEYDOWN:
                    if event.key == pygame.K_q:
                        game_over = True
                        game_close = False
                    if event.key == pygame.K_c:
                        gameLoop()

        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                game_over = True
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_a:
                    x1_change = -snake_block
                    y1_change = 0
                elif event.key == pygame.K_d:
                    x1_change = snake_block
                    y1_change = 0
                elif event.key == pygame.K_w:
                    y1_change = -snake_block
                    x1_change = 0
                elif event.key == pygame.K_s:
                    y1_change = snake_block
                    x1_change = 0

        if x1 >= dis_width or x1 < 0 or y1 >= dis_height or y1 < 0:
            game_close = True

        for obstacle in obstacles:
            if (x1, y1) == obstacle:
                game_close = True  # Snake hit an obstacle

        x1 += x1_change
        y1 += y1_change
        dis.fill(blue)
        draw_obstacles(obstacles)
        snake_Head = [x1, y1]
        snake_List.append(snake_Head)
        if len(snake_List) > Length_of_snake:
            del snake_List[0]

        our_snake(snake_block, snake_List)
        pygame.draw.rect(dis, green, [foodx, foody, snake_block, snake_block])

        if x1 == foodx and y1 == foody:
            foodx = round(random.randrange(0, dis_width - snake_block) / 10.0) * 10.0
            foody = round(random.randrange(0, dis_height - snake_block) / 10.0) * 10.0
            Length_of_snake += 1
            score += 1  # Increase the score when the snake eats food
            # Increase speed each time the snake eats food
            snake_speed += 2  # Increase speed by 2 units

        current_time = time.time()
        if current_time - last_speed_increase > speed_increase_interval:
            snake_speed += 2  # Increase speed every 10 seconds
            last_speed_increase = current_time  # Update the last speed increase time

        show_score(score)
        show_time(start_time)
        pygame.display.update()
        clock.tick(snake_speed)

    pygame.quit()
    quit()

gameLoop()

#Features / OBS / SPEED / TIME AND SCORE / WONT LOSE EVEN IT TOUCHES IT SELF
