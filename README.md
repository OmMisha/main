from random import randint
from pygame import *

lost = 0
number = 0

image_background = "galaxy.jpg"
image_hero = "rocket.png" 
image_enemy = "ufo.png"

font.init()
font1 = font.Font(None, 36)

window_width = 700
window_height = 500

window = display.set_mode((window_width, window_height))
display.set_caption("Shooter")

FPS = 30
clock = time.Clock()



clock = time.Clock()

background = transform.scale(image.load(image_background), (window_width, window_height))

class GameSprite(sprite.Sprite):
    def __init__(self, sprite_image, sprite_position_x, sprite_position_y, sprite_width, sprite_height, sprite_speed):
        super().__init__()
        self.image = transform.scale(image.load(sprite_image), (sprite_width, sprite_height))
        self.rect = self.image.get_rect()
        self.rect.x = sprite_position_x
        self.rect.y = sprite_position_y
        self.width = sprite_width
        self.height = sprite_height
        self.speed = sprite_speed
    
    def reset(self):
        window.blit(self.image, (self.rect.x, self.rect.y))

class Player(GameSprite):
    def update(self):
        keys = key.get_pressed()
        if keys[K_RIGHT] and self.rect.x < window_width - 100:
            self.rect.x += self.speed
        if keys[K_LEFT] and self.rect.x > 20:
            self.rect.x -= self.speed
        if keys[K_UP] and self.rect.y > window_height - 200:
            self.rect.y -= self.speed
        if keys[K_DOWN] and self.rect.y < window_height - 100:
            self.rect.y += self.speed
        
    def fire(self):
        pass

class Enemy(GameSprite):
    def update(self):
        self.rect.y += self.speed
        global lost
        if self.rect.y > window_height:
            self.rect.x = randint(50, window_width - 80)
            self.rect.y = 0
            lost += 1

monsters = sprite.Group()
for i in range(1, 6):
    monster = Enemy(image_enemy, randint(50, window_width - 80), 0, 80, 50, randint(1, 4))
    monsters.add(monster)

player = Player(image_hero, window_width / 2, window_height - 100, 80, 100, 10)

run = True
finish = False

while run:
    for e in event.get():
        if e.type == QUIT:
            run = False

    if not finish:
        window.blit(background, (0, 0))
        text_shooted = font1.render("Рахунок: " + str(number), 1, (255, 255, 255))
        window.blit(text_shooted, (10, 20))
        text_missed = font1.render("Пропущено: " + str(lost), 1, (255, 255, 255))
        window.blit(text_missed, (10, 50))

        player.reset()
        monsters.draw(window)

        monsters.update()
        player.update()
        display.update()


    clock.tick(FPS)
