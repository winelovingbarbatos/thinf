
############################################ Main Screen, Game Screen, Options Screen Code, Snake Game ###############################################################################
import pygame
import sys
import time
import random
import json
# Initialize pygame
pygame.init()

# Constants
WIDTH, HEIGHT = 800, 600
WHITE = (255, 255, 255)
CYAN = (0, 255, 255)
BLACK = (0, 0, 0)
SELECTED_COLOR = (0, 255, 255)  # Light blue for selection
UNSELECTED_COLOR = (255, 255, 255)  # White for unselected items
red = (255, 0, 0)
black = (0, 0, 0)
brick=(188, 74, 60)
white=(255,255,255) 
gray=(128, 128, 128)
green=(0,255,0)
blue=(0,0,255)
speed = [10,10]
BRICK_WIDTH = 25
BRICK_HEIGHT = 20
SPACING = 5
# Colors for the options menu
GREEN = (0, 255, 0)
RED = (255, 0, 0)
YELLOW = (255, 255, 0)
MAGENTA = (255, 0, 255)
CYAN = (0, 255, 255)
WHITE = (255, 255, 255)

# Snake Game Setup
snake_speed = 15
window_x = 720
window_y = 480
snake_position = [100, 100]
snake_body = [[100, 200], [90, 200], [80, 200], [70, 200]]
fruit_position = [random.randrange(1, (window_x//10)) * 10, 
                  random.randrange(1, (window_y//10)) * 10]
fruit_spawn = True
direction = 'RIGHT'
change_to = direction
score = 0

#block pong thing
ballx=100
bally=100
ballr=10
ball_obj = pygame.Rect(ballx - ballr, bally - ballr, ballr*2,ballr*2)
lives = 3
current_screen ="game"
#time veriable
time_last=0
time_interval=300
block_move_down=20
# define speed of ball
# speed = [X direction speed, Y direction speed]
speed = [10,10]
BRICK_WIDTH = 25
BRICK_HEIGHT = 20
SPACING = 5
exesleghth=0
exleghth=0
#platfrom veriable 
plat_x=75
plat_width=75
plat_speed=15
point=0
money=0
#brick wall 
block_hight=25
block_width= 65
blocks = []
lines=3
columes=650//block_width
# Pong Game Setup
pong_ball_position = [window_x // 2, window_y // 2]
pong_ball_velocity = [6, 6]
paddle_height = 100
paddle_width = 15
paddle_velocity = 5
left_paddle_position = [30, window_y // 2 - paddle_height // 2]
right_paddle_position = [window_x - 30 - paddle_width, window_y // 2 - paddle_height // 2]

# Game options menu
game_options = ["Pong", "Snake", "Flappy Bird","brick pong"]
selected_option = 0  # Start with "Pong" selected

font = pygame.font.Font(None, 60)
font_small = pygame.font.Font(None, 40)

# Main menu items
menu_items = ["Start Game", "Options", "Quit"]
menu_rects = []
for i, item in enumerate(menu_items):
    text = font.render(item, True, WHITE)
    rect = text.get_rect(center=(WIDTH // 2, HEIGHT // 2 - 100 + i * 80))
    menu_rects.append(rect)

# Game options menu
game_options = ["Pong", "Snake", "Flappy Bird","brick pong"]
selected_option = 0  # Start with "Pong" selected

# Options menu state
selected_snake_color = 0
selected_pong_ball_color = 0
color_names_ball = ['White', 'Cyan', 'Magenta', 'Green', 'Yellow']
color_names_snake = ['Green', 'Red', 'Yellow', 'Cyan', 'Magenta']

# Initialize the screen
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Retro Style Menu")

# FPS Clock
clock = pygame.time.Clock()

# Draw the main menu
def draw_main_menu(selected_index):
    screen.fill(BLACK)
    for i, rect in enumerate(menu_rects):
        if i == selected_index:
            pygame.draw.rect(screen, CYAN, rect.inflate(20, 20), 5)
        text = font.render(menu_items[i], True, WHITE)
        screen.blit(text, rect)
    pygame.display.flip()

def draw_game_menu(selected_option):
    screen.fill(BLACK)
    title = font.render("SELECT A GAME", True, WHITE)
    title_rect = title.get_rect(center=(WIDTH // 2, HEIGHT // 4))
    screen.blit(title, title_rect)

    for i, option in enumerate(game_options):
        option_text = font.render(option, True, UNSELECTED_COLOR)
        option_rect = option_text.get_rect(center=(WIDTH // 2, HEIGHT // 2 + (i * 80)))
        if i == selected_option:
            pygame.draw.rect(screen, SELECTED_COLOR, option_rect.inflate(30, 30), 3)
        screen.blit(option_text, option_rect)

    pygame.display.flip()

def draw_options_menu():
    screen.fill(BLACK)

    title = font.render("Options", True, WHITE)
    title_rect = title.get_rect(center=(WIDTH // 2, HEIGHT // 4))
    screen.blit(title, title_rect)

    snake_text = font_small.render("Snake Color", True, WHITE)
    snake_text_rect = snake_text.get_rect(center=(WIDTH // 2, HEIGHT // 3 + 40))
    screen.blit(snake_text, snake_text_rect)
    
    pygame.draw.rect(screen, color_names_snake[selected_snake_color], (WIDTH // 2 - 50, HEIGHT // 3 + 90, 100, 40))

    pong_text = font_small.render("Pong Ball Color", True, WHITE)
    pong_text_rect = pong_text.get_rect(center=(WIDTH // 2, HEIGHT // 2 + 100))
    screen.blit(pong_text, pong_text_rect)
    
    pygame.draw.rect(screen, color_names_ball[selected_pong_ball_color], (WIDTH // 2 - 50, HEIGHT // 2 + 150, 100, 40))

    instructions = font_small.render("Use Arrow Keys to select colors and ENTER to confirm", True, WHITE)
    instructions_rect = instructions.get_rect(center=(WIDTH // 2, HEIGHT - 50))
    screen.blit(instructions, instructions_rect)

    pygame.display.flip()


# Snake Game Logic
def show_score(choice, color, font, size, game_window):
    score_font = pygame.font.SysFont(font, size)
    score_surface = score_font.render('Score : ' + str(score), True, color)
    score_rect = score_surface.get_rect()
    game_window.blit(score_surface, score_rect)

def game_over(game_window):
    my_font = pygame.font.SysFont('Retro Style Menu', 50)
    game_over_surface = my_font.render('Your Score is : ' + str(score), True, RED)
    game_over_rect = game_over_surface.get_rect()
    game_over_rect.midtop = (window_x / 2, window_y / 4)
    game_window.blit(game_over_surface, game_over_rect)
    pygame.display.flip()
    time.sleep(2)

for row in range(lines):
    for col in range(columes):
        blocks.append(pygame.Rect(col * block_width+75, row * block_hight,block_width ,block_hight))

def new_row():
    for col in range(columes):
        blocks.append(pygame.Rect(col * block_width+75, 0, block_width, block_hight))


def draw_brick_wall():
    rows = HEIGHT // (BRICK_HEIGHT + SPACING)
    cols = 75 // (BRICK_WIDTH + SPACING)

    for row in range(rows):
        for col in range(cols):
            # Offset every other row for staggered bricks
            x_offset = (BRICK_WIDTH // 2) if row % 2 else 0
            brick_x = col * (BRICK_WIDTH + SPACING)+x_offset
            y = row * (BRICK_HEIGHT + SPACING)

            # Ensure bricks stay within the screen
            if  brick_x + BRICK_WIDTH <= WIDTH:
                pygame.draw.rect(screen, brick, ( brick_x, y, BRICK_WIDTH, BRICK_HEIGHT))
                
                if brick_x == 0:
                    pygame.draw.rect(screen,brick, (60, y, 15, BRICK_HEIGHT))
               
                else:
                    pygame.draw.rect(screen, brick, (0, y, 7, BRICK_HEIGHT))
                    pygame.draw.rect(screen, brick, (72, y, 3, BRICK_HEIGHT))
def draw_brick_wall2():
    rows = HEIGHT // (BRICK_HEIGHT + SPACING)
    cols = 75 // (BRICK_WIDTH + SPACING)

    for row in range(rows):
        for col in range(cols):
            # Offset every other row for staggered bricks
            x_offset = (BRICK_WIDTH // 2) if row % 2 else 0
            brick_x  = col * (BRICK_WIDTH + SPACING)+x_offset
            y = row * (BRICK_HEIGHT + SPACING)

            # Ensure bricks stay within the screen
            if brick_x  + BRICK_WIDTH <= WIDTH:
                pygame.draw.rect(screen, brick, (brick_x +725, y, BRICK_WIDTH, BRICK_HEIGHT))
                
                if brick_x == 0:
                    pygame.draw.rect(screen, brick, (60+725, y, 15, BRICK_HEIGHT))
               
                else:
                    pygame.draw.rect(screen, brick, (0+725, y, 7, BRICK_HEIGHT))
                    pygame.draw.rect(screen, brick, (72+725, y, 3, BRICK_HEIGHT))
def print_score():
    font = pygame.font.SysFont(None, 36)
    point_text = font.render(f"Score: {point}", True, white)
    screen.blit(point_text, (10, 10))
    lives_text = font.render(f"Lives: {lives}", True, white)
    screen.blit(lives_text, (10, 50))
    money_text = font.render(f"Money: {money}", True, white)
    screen.blit(money_text, (10, 90))

def pong_game_over():
    global point
    font = pygame.font.SysFont(None, 100)
    point_text = font.render("Your Score is: " + str(point), True, white)
    end_thing = point_text.get_rect()
    end_thing.midtop = (WIDTH / 2, HEIGHT / 4)
    screen.blit(point_text, end_thing)
    pygame.display.flip()

    # Save the game state
    save_game_data("game_data.json", {
        "point": point,
        "money": money,
        "lives": lives,
        "plat_speed": plat_speed,
        "plat_width": plat_width,
        "ball_size": ballr,
        "exesleghth":exesleghth,
        "exleghth":exleghth,
        "blocks": [[block.x, block.y, block.width, block.height] for block in blocks],
    })

    time.sleep(2)
    pygame.quit()
    sys.exit()
# Save data to a JSON file
def save_game_data(filename, data):
    with open(filename, 'w') as file:
        json.dump(data, file, indent=4)

# Load data from a JSON file
def load_game_data(filename):
    try:
        with open(filename, 'r') as file:
            return json.load(file)
    except FileNotFoundError:
        # Return default data if the file doesn't exist
        return {
            "point": 0,
            "money": 0,
            "lives": 3,
            "plat_speed": 15,
            "plat_width": 75,
            "ball_size": 10,
            "exesleghth":0,
            "exleghth":0,
            "blocks": [],
        }

class Button:
    def __init__(self, x, y, width, height, text, color, hover_color, text_color=black):
        self.rect = pygame.Rect(x, y, width, height)
        self.text = text
        self.color = color
        self.hover_color = hover_color
        self.text_color = text_color
        self.font = pygame.font.SysFont(None, 25)

    def draw(self, surface):
        mouse_pos = pygame.mouse.get_pos()
        is_hovered = self.rect.collidepoint(mouse_pos)
        
        # Change color on hover
        pygame.draw.rect(surface, self.hover_color if is_hovered else self.color, self.rect)
        
        # Render text
        text_surface = self.font.render(self.text, True, self.text_color)
        text_rect = text_surface.get_rect(center=self.rect.center)
        surface.blit(text_surface, text_rect)

    def is_clicked(self, event):
        if event.type == pygame.MOUSEBUTTONDOWN:
            if self.rect.collidepoint(event.pos):
                return True
        return False
buttons = [
    Button(150, 200, 200, 50, "Increase Paddle Speed:15 point", gray, green),
    Button(150, 300, 200, 50, "Increase Paddle Size:40 point", gray, blue),
    Button(450, 200, 200, 50, "Increase Ball size:20 point", gray,blue ),
    Button(450, 300, 200, 50, "Add lives:10 point", gray,red),
    Button(300, 450, 200, 50, "Back to Game", gray,white),
]
game_data = load_game_data("game_data.json")

# Initialize game state variables
point = game_data.get("point", 0)
money = game_data.get("money", 0)
lives = game_data.get("lives", 3)
plat_speed = game_data.get("plat_speed", 15)
plat_width = game_data.get("plat_width", 75)
ballr = game_data.get("ball_size", 10)
exesleghth = game_data.get("exesleghth",0)
exleghth = game_data.get("exleghth",0)
blocks = [pygame.Rect(*block) for block in game_data.get("blocks", [])]


def replaygame():
    global point, money, lives, plat_speed, plat_width, ballr, blocks, ball_obj, time_last,exesleghth,exleghth
    point = 0
    money = 0
    lives =  3
    plat_speed = 15
    plat_width = 75
    ballr = 10
    exesleghth = 0
    exleghth =0 
    ball_obj = pygame.Rect(100, 100, ballr*2, ballr*2)
    blocks=[]
    lines = 3
    columes = 650 // block_width
    for row in range(lines):
        for col in range(columes):
            blocks.append(pygame.Rect(col * block_width + 75, row * block_hight, block_width, block_hight))
    save_game_data("game_data.json", {
        "point": point,
        "money": money,
        "lives": lives,
        "plat_speed": plat_speed,
        "plat_width": plat_width,
        "ball_size": ballr,
        "exesleghth":exesleghth,
        "exleghth":exleghth,
        "blocks": [[block.x, block.y, block.width, block.height] for block in blocks],
    })

#Running Snake Game
def run_snake_game(selected_snake_color):
    global snake_position, snake_body, fruit_position, fruit_spawn, direction, change_to, score

    # Use the original WIDTH and HEIGHT from the main menu
    game_window = pygame.display.set_mode((WIDTH, HEIGHT))
    pygame.display.set_caption('Snake Game')
    fps = pygame.time.Clock()

    game_paused = False  # Pause flag
    game_over_flag = False  # Game over flag

    while True:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                quit()
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_SPACE:
                    game_paused = not game_paused  # Toggle pause
                    pygame.time.wait(200)  # Prevent rapid toggling (debouncing)
                if event.key == pygame.K_0:
                    return  # Return to the game menu
                if not game_paused and not game_over_flag:  # Only accept input if game is not paused or over
                    if event.key == pygame.K_UP and direction != 'DOWN':
                        change_to = 'UP'
                    if event.key == pygame.K_DOWN and direction != 'UP':
                        change_to = 'DOWN'
                    if event.key == pygame.K_LEFT and direction != 'RIGHT':
                        change_to = 'LEFT'
                    if event.key == pygame.K_RIGHT and direction != 'LEFT':
                        change_to = 'RIGHT'

        if game_paused:
            # Pause screen display
            game_window.fill(BLACK)

            # Display the "PAUSED" text centered on the screen
            pause_text = font.render("PAUSED", True, WHITE)
            pause_rect = pause_text.get_rect(center=(WIDTH // 2, HEIGHT // 2 - 50))
            game_window.blit(pause_text, pause_rect)

            # Display the "Press 'Space' to Resume" text centered below "PAUSED"
            resume_text = font_small.render("Press 'Space' to Resume", True, WHITE)
            resume_rect = resume_text.get_rect(center=(WIDTH // 2, HEIGHT // 2 + 10))
            game_window.blit(resume_text, resume_rect)

            # Display the "Press '0' to Leave" text centered below the resume text
            leave_text = font_small.render("Press '0' to Leave", True, WHITE)
            leave_rect = leave_text.get_rect(center=(WIDTH // 2, HEIGHT // 2 + 70))
            game_window.blit(leave_text, leave_rect)

            pygame.display.update()
            continue  # Skip updating game state while paused

        # Update the snake's direction
        direction = change_to

        # Update the snake's position
        if direction == 'UP':
            snake_position[1] -= 10
        if direction == 'DOWN':
            snake_position[1] += 10
        if direction == 'LEFT':
            snake_position[0] -= 10
        if direction == 'RIGHT':
            snake_position[0] += 10

        # Rest of the snake game logic
        snake_body.insert(0, list(snake_position))
        if snake_position[0] == fruit_position[0] and snake_position[1] == fruit_position[1]:
            score += 10
            fruit_spawn = False
        else:
            snake_body.pop()

        if not fruit_spawn:
            while True:
                fruit_position = [random.randrange(1, (WIDTH // 10)) * 10, 
                                  random.randrange(1, (HEIGHT // 10)) * 10]
                if fruit_position not in snake_body:
                    break
        fruit_spawn = True

        if snake_position[0] < 0 or snake_position[0] > WIDTH - 10:
            game_over_flag = True  # Set the game over flag
            game_over(game_window)  # Call game over display
        if snake_position[1] < 0 or snake_position[1] > HEIGHT - 10:
            game_over_flag = True  # Set the game over flag
            game_over(game_window)  # Call game over display
        for block in snake_body[1:]:
            if snake_position[0] == block[0] and snake_position[1] == block[1]:
                game_over_flag = True  # Set the game over flag
                game_over(game_window)  # Call game over display

        # If game is over, freeze the snake's movement
        if game_over_flag:
            continue  # Skip further movement updates, effectively freezing the snake

        # Drawing on the game window
        game_window.fill(BLACK)
        for pos in snake_body:
            pygame.draw.rect(game_window, color_names_snake[selected_snake_color], pygame.Rect(pos[0], pos[1], 10, 10))
        pygame.draw.rect(game_window, WHITE, pygame.Rect(fruit_position[0], fruit_position[1], 10, 10))

        # Display score
        show_score(1, WHITE, 'Retro Style Menu', 20, game_window)
        pygame.display.update()
        fps.tick(snake_speed)

# Pong Game Logic
def run_pong_game(selected_pong_ball_color):
    window_x=720
    window_y=480
    
    global pong_ball_position, pong_ball_velocity, left_paddle_position, right_paddle_position
    game_window = pygame.display.set_mode((window_x,window_y))  # Set the window size for Pong
    pygame.display.set_caption('Pong Game')

    left_score = 0
    right_score = 0

    # Flag to check if the game is paused
    game_paused = False

    # Main game loop
    while True:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                quit()

        # Check if the space key is pressed to toggle pause
        keys = pygame.key.get_pressed()
        if keys[pygame.K_SPACE]:
            game_paused = not game_paused  # Toggle the pause state
            pygame.time.wait(200)  # Prevent multiple toggles within a short time (debouncing)
        if keys[pygame.K_0]:
            window_x=800
            window_y=600
            return

        game_window.fill(BLACK)  # Fill screen with black at the start of each frame

        if game_paused:
            # Pause screen display
            game_window.fill(BLACK)

            # Display the "PAUSED" text centered on the screen
            pause_text = font.render("PAUSED", True, WHITE)
            pause_rect = pause_text.get_rect(center=(window_x // 2, window_y // 2 - 50))  # Slightly adjusted vertical position
            game_window.blit(pause_text, pause_rect)

            # Display the "Press 'Space' to Resume" text centered below "PAUSED"
            resume_text = font_small.render("Press 'Space' to Resume", True, WHITE)
            resume_rect = resume_text.get_rect(center=(window_x // 2, window_y // 2 + 10))  # Adjusted for center alignment
            game_window.blit(resume_text, resume_rect)

            # Display the "Press '0' to Leave" text centered below the resume text
            leave_text = font_small.render("Press '0' to Leave", True, WHITE)
            leave_rect = leave_text.get_rect(center=(window_x // 2, window_y // 2 + 70))  # Adjusted for center alignment
            game_window.blit(leave_text, leave_rect)
                
        else:
            # Move paddles and ball if not paused
            if keys[pygame.K_w] and left_paddle_position[1] > 0:
                left_paddle_position[1] -= paddle_velocity
            if keys[pygame.K_s] and left_paddle_position[1] < window_y - paddle_height:
                left_paddle_position[1] += paddle_velocity
            if keys[pygame.K_UP] and right_paddle_position[1] > 0:
                right_paddle_position[1] -= paddle_velocity
            if keys[pygame.K_DOWN] and right_paddle_position[1] < window_y - paddle_height:
                right_paddle_position[1] += paddle_velocity

            # Move ball
            pong_ball_position[0] += pong_ball_velocity[0]
            pong_ball_position[1] += pong_ball_velocity[1]

            # Collision with top/bottom walls
            if pong_ball_position[1] <= 0 or pong_ball_position[1] >= window_y - 10:
                pong_ball_velocity[1] = -pong_ball_velocity[1]

            # Collision with paddles
            if pong_ball_position[0] <= left_paddle_position[0] + paddle_width and left_paddle_position[1] < pong_ball_position[1] < left_paddle_position[1] + paddle_height:
                pong_ball_velocity[0] = -pong_ball_velocity[0]
            if pong_ball_position[0] >= right_paddle_position[0] - 10 and right_paddle_position[1] < pong_ball_position[1] < right_paddle_position[1] + paddle_height:
                pong_ball_velocity[0] = -pong_ball_velocity[0]

            # Score update
            if pong_ball_position[0] <= 0:
                right_score += 1
                pong_ball_position = [window_x // 2, window_y // 2]
                pong_ball_velocity = [-pong_ball_velocity[0], pong_ball_velocity[1]]
            elif pong_ball_position[0] >= window_x - 10:
                left_score += 1
                pong_ball_position = [window_x // 2, window_y // 2]
                pong_ball_velocity = [-pong_ball_velocity[0], pong_ball_velocity[1]]

            # Draw paddles and ball
            pygame.draw.rect(game_window, WHITE, pygame.Rect(left_paddle_position[0], left_paddle_position[1], paddle_width, paddle_height))
            pygame.draw.rect(game_window, WHITE, pygame.Rect(right_paddle_position[0], right_paddle_position[1], paddle_width, paddle_height))
            pygame.draw.rect(game_window, color_names_ball[selected_pong_ball_color], pygame.Rect(pong_ball_position[0], pong_ball_position[1], 10, 10))

            # Draw score
            left_score_text = font.render(str(left_score), True, WHITE)
            game_window.blit(left_score_text, (window_x // 4, 20))

            right_score_text = font.render(str(right_score), True, WHITE)
            game_window.blit(right_score_text, (window_x * 3 // 4, 20))

        pygame.display.update()
        clock.tick(60)

#Flappy Bird
def run_flappy_bird_game():
    game_window = pygame.display.set_mode((WIDTH, HEIGHT))
    pygame.display.set_caption("Flappy Bird")

    bird = pygame.Rect(WIDTH // 4, HEIGHT // 2, 30, 30)
    gravity = 0.5
    bird_velocity = 0
    jump_strength = -10

    pipe_width = 70
    pipe_gap = 200
    pipe_speed = 5
    pipes = []
    spawn_timer = 0

    score = 0

    running = True
    game_paused = False  # New flag to track if the game is paused

    while running:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_UP:  # Jump with the up arrow key
                    bird_velocity = jump_strength
                elif event.key == pygame.K_SPACE:  # Pause the game with spacebar
                    game_paused = not game_paused  # Toggle the pause state
                elif event.key == pygame.K_0:  # Go back to the main menu when '0' is pressed
                    return # Stop the game loop to return to main menu

        if game_paused:
            # Display the paused screen
            game_window.fill(BLACK)

            # Display the "PAUSED" text centered on the screen
            pause_text = font.render("PAUSED", True, WHITE)
            pause_rect = pause_text.get_rect(center=(WIDTH // 2, HEIGHT // 2 - 50))  # Slightly adjusted vertical position
            game_window.blit(pause_text, pause_rect)

            # Display the "Press 'Space' to Resume" text centered below "PAUSED"
            resume_text = font_small.render("Press 'Space' to Resume", True, WHITE)
            resume_rect = resume_text.get_rect(center=(WIDTH // 2, HEIGHT // 2 + 10))  # Adjusted for center alignment
            game_window.blit(resume_text, resume_rect)

            # Display the "Press '0' to Leave" text centered below the resume text
            leave_text = font_small.render("Press '0' to Leave", True, WHITE)
            leave_rect = leave_text.get_rect(center=(WIDTH // 2, HEIGHT // 2 + 70))  # Adjusted for center alignment
            game_window.blit(leave_text, leave_rect)

            pygame.display.update()
            continue  # Skip updating game state while paused

        # Update bird movement
        bird_velocity += gravity
        bird.y += bird_velocity

        # Spawn pipes
        spawn_timer += 1
        if spawn_timer >= 90:
            spawn_timer = 0
            pipe_height = random.randint(100, HEIGHT - pipe_gap - 100)
            pipes.append((WIDTH, pipe_height))

        # Move pipes
        pipes = [(x - pipe_speed, y) for x, y in pipes if x > -pipe_width]

        # Check collisions
        for x, y in pipes:
            if bird.colliderect(pygame.Rect(x, 0, pipe_width, y)) or bird.colliderect(pygame.Rect(x, y + pipe_gap, pipe_width, HEIGHT - y - pipe_gap)):
                running = False
        if bird.y <= 0 or bird.y >= HEIGHT:
            running = False

        # Update score
        for x, y in pipes:
            if x + pipe_width // 2 == bird.x:
                score += 1

        # Draw everything
        game_window.fill(BLACK)
        
        # Draw the bird
        pygame.draw.rect(game_window, CYAN, bird)

        # Add the yellow beak to the bird (relative to bird position)
        beak_points = [(bird.centerx + 15, bird.centery - 5), (bird.centerx + 25, bird.centery), (bird.centerx + 15, bird.centery + 5)]
        pygame.draw.polygon(game_window, YELLOW, beak_points)

        # Draw pipes
        for x, y in pipes:
            pygame.draw.rect(game_window, GREEN, pygame.Rect(x, 0, pipe_width, y))
            pygame.draw.rect(game_window, GREEN, pygame.Rect(x, y + pipe_gap, pipe_width, HEIGHT - y - pipe_gap))

        score_text = font.render(f"Score: {score}", True, WHITE)
        game_window.blit(score_text, (10, 10))

        pygame.display.flip()
        clock.tick(60)

    # Game over screen
    game_window.fill(BLACK)
    game_over_text = font.render("GAME OVER", True, WHITE)
    score_text = font.render(f"Final Score: {score}", True, WHITE)
    game_over_rect = game_over_text.get_rect(center=(WIDTH // 2, HEIGHT // 2 - 40))
    score_rect = score_text.get_rect(center=(WIDTH // 2, HEIGHT // 2 + 40))
    game_window.blit(game_over_text, game_over_rect)
    game_window.blit(score_text, score_rect)
    pygame.display.flip()
    pygame.time.wait(3000)

def run_brick_pong():
    game_data = load_game_data("game_data.json")

# Initialize game state variables
    point = game_data.get("point", 0)
    money = game_data.get("money", 0)
    lives = game_data.get("lives", 3)
    plat_speed = game_data.get("plat_speed", 15)
    plat_width = game_data.get("plat_width", 75)
    ballr = game_data.get("ball_size", 10)
    exesleghth = game_data.get("exesleghth",0)
    exleghth = game_data.get("exleghth",0)
    blocks = [pygame.Rect(*block) for block in game_data.get("blocks", [])]
    current_screen="game"
    ballx=100
    bally=100
    plat_x=75
    time_last=0
    ball_obj = pygame.Rect(ballx - ballr, bally - ballr, ballr*2,ballr*2)
    running=True
# game loop
    while running:
        if lives<=0:
                replaygame()
        if current_screen=="game":
            for event in pygame.event.get():
            # check if a user wants to exit the game or not
                if event.type == pygame.QUIT:
                    exit()
                if event.type == pygame.KEYDOWN:
                    if event.key == pygame.K_SPACE:
                        current_screen="menu"
        # move the ball
            screen.fill(black)
            ball_obj = ball_obj.move(speed)
            keys = pygame.key.get_pressed() 

            if keys[pygame.K_LEFT] and plat_x>75:       
                # decrement in x co-ordinate 
                plat_x -= plat_speed
                
            # if left arrow key is pressed 
            if keys[pygame.K_RIGHT] and plat_x<WIDTH-155-exesleghth: 
                # increment in x co-ordinate 
                plat_x+=plat_speed 
            
            # if ball goes out of screen then change direction of movement
            if ball_obj.left <= 75+exleghth or ball_obj.right >= WIDTH-75-exleghth:
                speed[0] = -speed[0]
            if ball_obj.top <= 10 :
                speed[1] = -speed[1]
            if ball_obj.bottom >= HEIGHT:
                lives -= 1
                if lives == 0:
                    game_over()
                    pygame.quit()
                    sys.exit()
                ball_obj = pygame.Rect(100+ ballr, 100 +ballr, 20, 20)

            #ball hit the platform
            if ball_obj.colliderect(pygame.Rect(plat_x, 540, plat_width, 20 + ballr)):
                speed[1] = -speed[1]

            
            for block in blocks[:]:
                if (
            ball_obj.colliderect(block) or
            (block.left <= ball_obj.centerx <= block.right and
            block.top <= ball_obj.centery + ballr <= block.bottom) or
            (block.left <= ball_obj.centerx <= block.right and
            block.top <= ball_obj.centery - ballr <= block.bottom)
                ):
                    blocks.remove(block)
                    speed[1]= -speed[1]
                    point += 1
                    money+=1
                    break
            
            if time_last >= time_interval:
                for block in blocks:
                    block.move_ip(0, block_move_down)
                new_row()
                time_last = 0
            pygame.draw.circle(surface=screen, color=red,
                        center=ball_obj.center, radius=ballr)
        
            point=(
                (0,600),
                (75,600),
                (75,0),
                (0,0)
            )
            pygame.draw.polygon(screen,(115,35,25),(point))
            point1=(
                (800,600),
                (725,600),
                (725,0),
                (800,0)
            )
            pygame.draw.polygon(screen,(115,35,25),(point1))
            draw_brick_wall()
            draw_brick_wall2()
            
            for block in blocks :
                pygame.draw.rect(screen, red,block,)
            
            pygame.draw.rect(screen, white, (plat_x, 540,plat_width, 10))
            print_score()
        
        #upgrade menu 
        elif current_screen == "menu":
            screen.fill(white)
            for event in pygame.event.get():
                if event.type == pygame.QUIT:
                    running = False
        # Draw buttons

            # Event handling for menu
            for button in buttons:
                    button.draw(screen)
                    if button.is_clicked(event):
                        if button.text == "Increase Paddle Speed:15 point" and money >= 15:
                            plat_speed += 5
                            money -= 15
                        elif button.text == "Increase Paddle Size:40 point" and money >= 50:
                            plat_width += 10
                            exesleghth+=10
                            money -= 50
                        elif button.text == "Increase Ball size:20 point" and money >= 20:
                            ballr += 5
                            exleghth+=5
                            money -= 20
                        elif button.text == "Add lives:10 point" and money >= 10:
                            lives += 1
                            money -= 10
                        elif button.text == "Back to Game":
                            current_screen = "game"
                        save_game_data("game_data.json", {
                                        "point": point,
                                        "money": money,
                                        "lives": lives,
                                        "plat_speed": plat_speed,
                                        "plat_width": plat_width,
                                        "ball_size": ballr,
                                        "exesleghth":exesleghth,
                                        "exleghth":exleghth,
                                        "blocks": [[block.x, block.y, block.width, block.height] for block in blocks],
                                    })
                                        
        
        # draw ball at new centers that are obtained after moving ball_obj
    
        # update screen
        pygame.display.flip()
        time_last+=1
        clock.tick(30)
# Main Menu loop
def main():
    global selected_option, selected_snake_color, selected_index, selected_pong_ball_color
    in_main_menu = True
    in_game_menu = False
    in_options_menu = False
    selected_index = 0

    while True:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()

            if in_main_menu:
                if event.type == pygame.KEYDOWN:
                    if event.key == pygame.K_DOWN:
                        selected_index = (selected_index + 1) % len(menu_items)
                    elif event.key == pygame.K_UP:
                        selected_index = (selected_index - 1) % len(menu_items)
                    elif event.key == pygame.K_RETURN:
                        if selected_index == 0:
                            in_main_menu = False
                            in_game_menu = True
                        elif selected_index == 1:
                            in_main_menu = False
                            in_options_menu = True
                        elif selected_index == 2:
                            pygame.quit()
                            sys.exit()

                draw_main_menu(selected_index)

            elif in_game_menu:
                if event.type == pygame.KEYDOWN:
                    if event.key == pygame.K_UP:
                        selected_option = (selected_option - 1) % len(game_options)
                    elif event.key == pygame.K_DOWN:
                        selected_option = (selected_option + 1) % len(game_options)
                    elif event.key == pygame.K_RETURN:
                        if selected_option == 0:
                            print("Pong selected")
                            run_pong_game(selected_pong_ball_color)
                        elif selected_option == 1:
                            print("Snake selected")
                            run_snake_game(selected_snake_color)
                        elif selected_option == 2:
                            print("Flappy Bird selected")
                            run_flappy_bird_game()
                        elif selected_option == 3:
                            print("brick pong selected")
                            run_brick_pong()
                    elif event.key == pygame.K_0:
                        in_game_menu = False
                        in_main_menu = True

                draw_game_menu(selected_option)

            elif in_options_menu:
                if event.type == pygame.KEYDOWN:
                    if event.key == pygame.K_UP:
                        selected_snake_color = (selected_snake_color - 1) % len(color_names_snake)
                    elif event.key == pygame.K_DOWN:
                        selected_snake_color = (selected_snake_color + 1) % len(color_names_snake)
                    elif event.key == pygame.K_LEFT:
                        selected_pong_ball_color = (selected_pong_ball_color - 1) % len(color_names_ball)
                    elif event.key == pygame.K_RIGHT:
                        selected_pong_ball_color = (selected_pong_ball_color + 1) % len(color_names_ball)
                    elif event.key == pygame.K_RETURN:
                        print(f"Selected Snake Color: {color_names_snake[selected_snake_color]}")
                        print(f"Selected Pong Ball Color: {color_names_ball[selected_pong_ball_color]}")
                    elif event.key == pygame.K_0:
                        in_options_menu = False
                        in_game_menu = True

                draw_options_menu()

        pygame.time.Clock().tick(60)

# Run the program
if __name__ == "__main__":
    main()
