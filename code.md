# Kóði 
'''python
from random import randint, randrange
from time import sleep_ms
from machine import Pin, PWM
from neopixel import NeoPixel

p1LjosB = Pin(15, Pin.OUT)
p1LjosR = Pin(16, Pin.OUT)
p2LjosB = Pin(17, Pin.OUT)
p2LjosR = Pin(18, Pin.OUT)

p1TakkiB = Pin(11, Pin.IN, Pin.PULL_UP)
p1TakkiR = Pin(12, Pin.IN, Pin.PULL_UP)
p2TakkiB = Pin(13, Pin.IN, Pin.PULL_UP)
p2TakkiR = Pin(14, Pin.IN, Pin.PULL_UP)

hatalari_passive = PWM(Pin(9), freq=20000)
hatalari_passive.duty(0)

neoPixle1 = Pin(4, Pin.OUT)
neoPixle2 = Pin(5, Pin.OUT)
neoPixleMain = Pin(6, Pin.OUT)
p1NeoLif = NeoPixel(neoPixle1, 5)
p2NeoLif = NeoPixel(neoPixle2, 5)
mainDisplay = NeoPixel(neoPixleMain, 8)

p1Turn = True
byrjandi = 0
gameOn = True
gun = []
curentShot = ""

p1Lif = 5
p2Lif = 5

def shuffle(array):
    "Fisher–Yates shuffle"
    for i in range(len(array)-1, 0, -1):
        j = randrange(i+1)
        array[i], array[j] = array[j], array[i]

def blankSound():
    hatalari_passive.duty(512)
    hatalari_passive.freq(988)
    sleep_ms(450)
    hatalari_passive.freq(880)
    sleep_ms(450)
    hatalari_passive.duty(0)
    
def liveSound():
    hatalari_passive.duty(512)
    hatalari_passive.freq(247)
    sleep_ms(450)
    hatalari_passive.freq(220)
    sleep_ms(450)
    hatalari_passive.duty(0)

def hurtSound():
    hatalari_passive.duty(512)
    hatalari_passive.freq(247)
    sleep_ms(300)
    hatalari_passive.freq(196)
    sleep_ms(300)
    hatalari_passive.freq(220)
    sleep_ms(300)
    hatalari_passive.duty(0)
    
def p1Ljos():
    mainDisplay.fill([0,0,0])
    mainDisplay.write()
    mainDisplay[0] = [200,0,0]
    mainDisplay[1] = [200,0,0]
    mainDisplay[7] = [200,0,0]
    mainDisplay.write()
    p1LjosB.value(1)
    p1LjosR.value(1)
    
def p2Ljos():
    mainDisplay.fill([0,0,0])
    mainDisplay[3] = [200,0,0]
    mainDisplay[4] = [200,0,0]
    mainDisplay[5] = [200,0,0]
    mainDisplay.write()
    p2LjosB.value(1)
    p2LjosR.value(1)

def clearMain():
    mainDisplay.fill([0,0,0])
    mainDisplay.write()

for x in range(5):
    for i in range(8):
        mainDisplay.fill([0,0,0]) 
        mainDisplay[i] = [200,0,0]
        mainDisplay.write()
        sleep_ms(50)

while True:
    
    clearMain()
    gameOn = True
    gun.clear()

    for x in range(5):
        p1NeoLif[x] = [0,200,0]
        p2NeoLif[x] = [0,200,0]
        p1NeoLif.write()
        p2NeoLif.write()
        sleep_ms(250)
        
    for x in range(3):
        hatalari_passive.duty(512)
        hatalari_passive.freq(261)
        sleep_ms(100)
        hatalari_passive.freq(293)
        sleep_ms(100)
        hatalari_passive.freq(329)
        sleep_ms(100)
        hatalari_passive.freq(349)
        sleep_ms(100)
        hatalari_passive.freq(392)
        sleep_ms(100)
        hatalari_passive.freq(440)
        sleep_ms(100)
        hatalari_passive.freq(493)
        sleep_ms(100)
        hatalari_passive.duty(0)
    for x in range(3):
        hatalari_passive.duty(512)
        hatalari_passive.freq(1046)
        sleep_ms(85)
        hatalari_passive.duty(0)
        sleep_ms(85)

        
    while gameOn == True:
        p1LjosR.value(0)
        p1LjosB.value(0)
        p2LjosR.value(0)
        p2LjosB.value(0)
        
        if len(gun) == 0:
            liveAmount = randint(1,6)
            print(liveAmount)
            for x in range(liveAmount):
                gun.append("LIVE")
            for x in range(8-liveAmount):
                gun.append("BLANK")
            sleep_ms(2000)
            print(gun)
            
            clearMain()
        
            for x in range(8):
                if x < liveAmount:
                    mainDisplay[x] = [50,0,0]
                    mainDisplay.write()
                else:
                    mainDisplay[x] = [0,50,0]
                    mainDisplay.write()
                sleep_ms(250)
            
            sleep_ms(750)
            clearMain()
            
            shuffle(gun)
            print(gun)
     
        else:
             print(gun)
     
        if p1Turn == True:
            turnActive = True
            p1Ljos()
            
            
            while turnActive == True:
                
                if p1TakkiB.value() == 0:
                    currentShot = gun[0]
                    gun.pop(0)
                    
                    if currentShot == "LIVE":
                        hurtSound()
                        p1Lif -= 1
                        print(p1Lif)
                        p1NeoLif.fill([0,0,0])
                        p1NeoLif.write()
                        for x in range(p1Lif):
                            p1NeoLif[x] = [0,200,0]
                            p1NeoLif.write()
                        turnActive = False
                    elif currentShot == "BLANK":
                        blankSound()
                        if len(gun) == 0:
                            turnActive = False
                        
                elif p1TakkiR.value() == 0:
                    currentShot = gun[0]
                    gun.pop(0)
                    
                    if currentShot == "LIVE":
                        hurtSound()
                        p2Lif -= 1
                        print(p2Lif)
                        p2NeoLif.fill([0,0,0])
                        p2NeoLif.write()
                        for x in range(p2Lif):
                            p2NeoLif[x] = [0,200,0]
                            p2NeoLif.write()
                        turnActive = False
                    elif currentShot == "BLANK":
                        blankSound()
                        turnActive = False
                    
            p1Turn = False
            
        else:
            turnActive = True
            p2Ljos()
            
            while turnActive == True:
                
                if p2TakkiB.value() == 0:
                    currentShot = gun[0]
                    gun.pop(0)
                    
                    if currentShot == "LIVE":
                        hurtSound()
                        p2Lif -= 1
                        print(p2Lif)
                        p2NeoLif.fill([0,0,0])
                        p2NeoLif.write()
                        for x in range(p2Lif):
                            p2NeoLif[x] = [0,200,0]
                            p2NeoLif.write()
                        turnActive = False
                    elif currentShot == "BLANK":
                        blankSound()
                        if len(gun) == 0:
                            turnActive = False
                        
                elif p2TakkiR.value() == 0:
                    currentShot = gun[0]
                    gun.pop(0)
                    print("TakkiR")
                    
                    if currentShot == "LIVE":
                        hurtSound()
                        p1Lif -= 1
                        print(p1Lif)
                        p1NeoLif.fill([0,0,0])
                        p1NeoLif.write()
                        for x in range(p1Lif):
                            p1NeoLif[x] = [0,200,0]
                            p1NeoLif.write()
                        turnActive = False
                    elif currentShot == "BLANK":
                        blankSound()
                        turnActive = False
            
            p1Turn = True

        if p1Lif == 0:
            print("virkar")
            mainDisplay.fill([0,0,0])
            mainDisplay.write()
            mainDisplay[3] = [255,145,0]
            mainDisplay[4] = [255,145,0]
            mainDisplay[5] = [255,145,0]
            mainDisplay.write()
            for x in range(3):
                hatalari_passive.duty(512)
                hatalari_passive.freq(493)
                sleep_ms(100)
                hatalari_passive.freq(440)
                sleep_ms(100)
                hatalari_passive.freq(392)
                sleep_ms(100)
                hatalari_passive.freq(349)
                sleep_ms(100)
                hatalari_passive.freq(329)
                sleep_ms(100)
                hatalari_passive.freq(293)
                sleep_ms(100)
                hatalari_passive.freq(261)
                sleep_ms(100)
                hatalari_passive.duty(0)
            for x in range(3):
                hatalari_passive.duty(512)
                hatalari_passive.freq(329)
                sleep_ms(85)
                hatalari_passive.duty(0)
                sleep_ms(85)
            gameOn = False
            p1Lif = 5
            p2Lif = 5
        elif p2Lif == 0:
            print("virkar")
            mainDisplay.fill([0,0,0])
            mainDisplay[0] = [255,145,0]
            mainDisplay[1] = [255,145,0]
            mainDisplay[7] = [255,145,0]
            mainDisplay.write()
            for x in range(3):
                hatalari_passive.duty(512)
                hatalari_passive.freq(493)
                sleep_ms(100)
                hatalari_passive.freq(440)
                sleep_ms(100)
                hatalari_passive.freq(392)
                sleep_ms(100)
                hatalari_passive.freq(349)
                sleep_ms(100)
                hatalari_passive.freq(329)
                sleep_ms(100)
                hatalari_passive.freq(293)
                sleep_ms(100)
                hatalari_passive.freq(261)
                sleep_ms(100)
                hatalari_passive.duty(0)
            for x in range(3):
                hatalari_passive.duty(512)
                hatalari_passive.freq(329)
                sleep_ms(85)
                hatalari_passive.duty(0)
                sleep_ms(85)
            gameOn = False
            p1Lif = 5
            p2Lif = 5
"""
