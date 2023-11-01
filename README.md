# pygame-airplane-game
一个基于Pygame库的打飞机游戏示例，玩家控制飞机躲避敌机的攻击并发射子弹击落敌机。包含玩家飞机、敌机、子弹等游戏元素的实现，并具有碰撞检测和计分功能。适合初学者学习Pygame游戏开发。
import pygame
import random

# 初始化游戏
pygame.init()

# 游戏窗口尺寸
window_width = 800
window_height = 600

# 颜色定义
white = (255, 255, 255)
black = (0, 0, 0)

# 创建游戏窗口
window = pygame.display.set_mode((window_width, window_height))
pygame.display.set_caption("打飞机游戏")

# 加载飞机图片
player_img = pygame.image.load("player.png")

# 飞机类
class Player(pygame.sprite.Sprite):
    def __init__(self):
        super().__init__()
        self.image = player_img
        self.rect = self.image.get_rect()
        self.rect.centerx = window_width // 2
        self.rect.bottom = window_height - 10
        self.speed_x = 0

    def update(self):
        self.rect.x += self.speed_x
        if self.rect.left < 0:
            self.rect.left = 0
        elif self.rect.right > window_width:
            self.rect.right = window_width

# 子弹类
class Bullet(pygame.sprite.Sprite):
    def __init__(self, x, y):
        super().__init__()
        self.image = pygame.Surface((5, 15))
        self.image.fill(white)
        self.rect = self.image.get_rect()
        self.rect.centerx = x
        self.rect.centery = y
        self.speed_y = -10

    def update(self):
        self.rect.y += self.speed_y
        if self.rect.bottom < 0:
            self.kill()

# 敌机类
class Enemy(pygame.sprite.Sprite):
    def __init__(self):
        super().__init__()
        self.image = pygame.Surface((50, 40))
        self.image.fill((random.randint(50, 255), random.randint(50, 255), random.randint(50, 255)))
        self.rect = self.image.get_rect()
        self.rect.x = random.randint(0, window_width - self.rect.width)
        self.rect.y = random.randint(-100, -40)
        self.speed_y = random.randint(1, 3)

    def update(self):
        self.rect.y += self.speed_y
        if self.rect.top > window_height:
            self.rect.x = random.randint(0, window_width - self.rect.width)
            self.rect.y = random.randint(-100, -40)
            self.speed_y = random.randint(1, 3)

# 创建精灵组
all_sprites = pygame.sprite.Group()
bullets = pygame.sprite.Group()
enemies = pygame.sprite.Group()

# 创建玩家飞机对象
player = Player()
all_sprites.add(player)

# 创建敌机对象
for _ in range(8):
    enemy = Enemy()
    all_sprites.add(enemy)
    enemies.add(enemy)

# 游戏主循环
running = True
clock = pygame.time.Clock()
while running:
    clock.tick(60)

    # 处理事件
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.KEYDOWN:
            if event.key == pygame.K_SPACE:
                bullet = Bullet(player.rect.centerx, player.rect.top)
                all_sprites.add(bullet)
                bullets.add(bullet)

    # 更新游戏状态
    all_sprites.update()

    # 子弹与敌机碰撞检测
    hits = pygame.sprite.groupcollide(bullets, enemies, True, True)
    for hit in hits:
        enemy = Enemy()
        all_sprites.add(enemy)
        enemies.add(enemy)

    # 玩家飞机与敌机碰撞检测
    hits = pygame.sprite.spritecollide(player, enemies, False)
    if hits:
        running = False

    # 绘制游戏画面
    window.fill(black)
    all_sprites.draw(window)
    pygame.display.flip()

# 游戏结束
pygame.quit()
