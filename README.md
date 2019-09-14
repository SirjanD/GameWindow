# GameWindow
import pygame,sys
from pygame.locals import *
from playsound import *
pygame.init()
clock = pygame.time.Clock()

pause = False


x = 900
y = 600
win = pygame.display.set_mode((x,y))
pygame.display.set_caption('DOODLES')
pygame.display.init()

red = (200,0,0)
green = (0,200,0)
bright_red = (255,0,0)
bright_green = (0,255,0)
block_color = (53,115,255)
black = (0,0,0)
white = (255,255,255)

def GameOver():
    ####################################
    #pygame.mixer.Sound.play("smb_gameover.wav")
    pygame.mixer.music.stop()
    ####################################
    font = pygame.font.Font("freesansbold.ttf",115)
    largeText = font.render("Game Over",True,red,black)
    TextRect = largeText.get_rect()
    TextRect.center = ((x/2),(y/2))
    win.blit(largeText, TextRect)

    while True:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                quit()

        button("Play Again",150,450,100,50,green,bright_green,game_loop)
        button("Quit",550,450,100,50,red,bright_red,quitgame)

        pygame.display.update()
        clock.tick(15)

def text_objects(text, font):
    textSurface = font.render(text, True, black)
    return textSurface, textSurface.get_rect()

def button(msg,x,y,w,h,ic,ac,action=None):
    mouse = pygame.mouse.get_pos()
    click = pygame.mouse.get_pressed()
    
    if x+w > mouse[0] > x and y+h > mouse[1] > y:
        pygame.draw.rect(win, ac,(x,y,w,h))
        if click[0] == 1 and action != None:
            pygame.mixer.music.stop()
            playsound('Button_Push-Mike_Koenig-1659525069.wav')
            action()

    else:
        pygame.draw.rect(win, ic,(x,y,w,h))
        
    smallText = pygame.font.SysFont("comicsansms",20)
    textSurf, textRect = text_objects(msg, smallText)
    textRect.center = ( (x+(w/2)), (y+(h/2)) )
    win.blit(textSurf, textRect)

def quitgame():
    pygame.quit()
    sys.exit()
    quit()

def music():
    pygame.mixer.music.load('Friday-night-synthwave-electronic-music.mp3')
    pygame.mixer.music.play()

def unpause():
    global pause
    music()
    pygame.mixer.music.unpause()
    pause = False

def paused():
    ############
    music()
    pygame.mixer.music.pause()
    global pause
    largeText = pygame.font.SysFont("comicsansms",115)
    TextSurf, TextRect = text_objects("Paused", largeText)
    TextRect.center = ((x/2),(y/2))
    win.blit(TextSurf, TextRect)
    pause = True

    while pause:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                quit()

        button("Continue",150,450,100,50,green,bright_green,unpause)
        button("Quit",550,450,100,50,red,bright_red,quitgame)
        pygame.display.update()
        clock.tick(15)

def game_intro():

    intro = True
    pygame.mixer.music.load('News_Intro-Maximilien_-1801238420.wav')
    pygame.mixer.music.play()
    
    while intro:
        

        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                quit()

        pygame.display.flip()
        button("Start",150,450,100,50,green,bright_green,run)
        button("Quit",550,450,100,50,red,bright_red,quitgame)
        pygame.display.update()

def run():
    global pause
    gameExit = False

    while not gameExit:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()
                quit()

        win.fill(black)
        #gameDisplaypic = pygame.image.load('back.jpg').convert()
        #gameDisplay.blit(gameDisplaypic, [0,0])
        tekst = "This game will go as far as you choose!"
        meie_font = pygame.font.SysFont("Arial", 36)
        teksti_pilt = meie_font.render(tekst, False, (50,50,155))
        win.blit(teksti_pilt, (100, 250))
        tekst2 = "READY!"
        meie_font = pygame.font.SysFont("Arial", 36)
        teksti_pilt = meie_font.render(tekst2, False, (50,50,155))
        win.blit(teksti_pilt, (100, 400))
        
        button("Start playing",300,500,150,50,green,bright_green,game_loop)
        pygame.display.update()

        win.fill((black))
        
def game_loop():

    music()
    bg = pygame.image.load('ground.jpg').convert()
    img = pygame.image.load('doodle.png').convert_alpha()
    b1 = pygame.image.load('block1.jpg').convert()
    sea = pygame.image.load('sea.jpg').convert()
    img_x = 300
    img_y = 350
    vel = 20
    count = 0
    jump = False
    fall = False
    global pause
    done = False
       
    while not done:
        win.blit(bg,(150,400))
        win.blit(b1,(0,300))
        win.blit(sea,(0,530))
        win.blit(sea,(750,530))
        win.blit(img,(img_x,img_y))
        button("PAUSE",0,0,100,50,green,red,paused)
        button("Quit",800,0,100,50,red,bright_red,quitgame)
        pygame.display.update()
    
        for event in pygame.event.get():
            #print(event)
            if event.type == pygame.QUIT:
                pygame.quit()
                done = True
            if event.type == pygame.KEYDOWN:
                if event.key ==pygame.K_RIGHT and img_y <= 350:
                    img_x = img_x + vel
                    if img_x >= 750 and img_y==350:
                        img_y = img_y + 200
                        count = count+1
                        
                    if img_x >=120 and img_y==250:
                        img_y = img_y + 100

                    
                elif event.key == pygame.K_LEFT and img_y <= 350 and img_x>0:
                    img_x = img_x - vel
                    if img_x <= 110 and img_y==350:
                        img_y = img_y +200
                        count = count+1

                    
                elif event.key == pygame.K_SPACE and img_y<= 350:
                    if not fall:
                        jump = True

                elif count == 1:
                    GameOver()

                
            win.fill(black)      
        
        if jump:
            img_y -= 3
            if img_y >=320 and img_x<=120:
                img_y = 320
                jump = False
                fall = True
            if img_y <= 200 and img_x>120:
                img_y = 200
                jump = False
                fall = True
            if img_y <= 100 and img_x<=120 and img_x>=0:
                img_y = 100
                jump = False
                fall = True
            win.fill(black)

        if fall:
            img_y += 3
            if img_y >=250 and img_x<=120:
                img_y = 250
                fall = False
            if img_y >= 350:
                img_y = 350
                fall = False
            win.fill(black)
                
        
        win.blit(bg,(150,400))
        win.blit(sea,(0,530))
        win.blit(sea,(750,530))
        win.blit(b1,(0,300))
        pygame.display.flip()
    
game_intro()

pygame.quit()
quit()
