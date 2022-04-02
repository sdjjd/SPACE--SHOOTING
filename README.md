# SPACE--SHOOTING
My first game i guess
from pygame import * 
from random import randint
font.init()
font1 = font.SysFont('Arial', 80)
win = font1.render('ТЫ ВЫИГРАЛ! МОЛОДЕЦ!!', True, (255, 255, 255))
lose = font1.render('ТЫ ПРОИГРАЛ, ХАXA!', True, (180, 180, 180))
font2 = font.SysFont('Arial', 36)
mixer.init()
mixer.music.load('space.ogg')
mixer.music.play()
fire_sound = mixer.Sound('fire.ogg')
class GameSprite(sprite.Sprite):
    def __init__(self, player_image, player_x, player_y, player_speed):
        super().__init__()
        self.image = transform.scale(image.load(player_image), (55, 55))
        self.speed = player_speed
        self.rect = self.image.get_rect()
        self.rect.x = player_x
        self.rect.y = player_y
    def reset(self):
        window.blit(self.image, (self.rect.x, self.rect.y))
class Player(GameSprite):
    def update(self):
        keys = key.get_pressed()
        if keys[K_LEFT] and self.rect.x > 1:
            self.rect.x -= self.speed
        if keys[K_RIGHT] and self.rect.x < win_width - 80:
            self.rect.x += self.speed
    def fire(self):
        bullet = Bullet('bullet.png', self.rect.centerx, self.rect.top, 15)
        bullets.add(bullet)
class Enemy(GameSprite):
    def update(self):
        self.rect.y += self.speed
        global lost
        if self.rect.y > win_height:
            self.rect.x = randint(80, win_width - 80)
            self.rect.y = 0
            lost = lost + 1
class Bullet(GameSprite):
    def update(self):
        self.rect.y -= self.speed
        if self.rect.y < 0:
            self.kill()
win_width = 700
win_height = 500
display.set_caption("шутер")
window = display.set_mode((win_width, win_height))
background = transform.scale(image.load("galaxy.jpg"), (700,500))
ship = Player('rocket.png', 5, 500 - 100, 10)
monsters = sprite.Group()
max_lost=10
max_win=10
score=0 
lost=0
for i in range(1, 6):
    monster = Enemy("ufo.png", randint(80, win_width), -40, randint(1, 3))
    monsters.add(monster)
bullets = sprite.Group()

finish = False
run = True
while run:
    for e in event.get():
        if e.type == QUIT:
            run = False
        elif e.type == KEYDOWN:
            if e.key == K_SPACE:
                fire_sound.play()
                ship.fire()
    if not finish:
        window.blit(background,(0,0))
        ship.update()
        ship.reset()
        monsters.draw(window)
        monsters.update()
        bullets.update()

        monsters.draw(window)
        bullets.draw(window)
        collides = sprite.groupcollide(monsters, bullets, True, True)
        for c in collides:
            score = score + 1
            monster = Enemy("ufo.png", randint(80, win_width - 80), -40, randint(5,8))
            monsters.add(monster)
        if sprite.spritecollide(ship, monsters, False) or lost >= max_lost:
            finish = True
            window.blit(lose, (200, 200))
        if sprite.groupcollide(monsters, bullets, True, True) or win == max_win:
            finish = True
            window.blit(win, (200, 200))
    display.update()
