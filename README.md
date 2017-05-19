# tc2_td8
# coding=utf-8
from threading import *
import queue 
from random import randint, choice
import math
import time
from tkinter import *

class ZoneAffichage(Canvas):
    def __init__(self,w,h):
        Canvas.__init__(self,width=w, height=h, bg='black')
        self.__balles = []

    def afficher(self):
        for b in self.__balles:
            b.deplacement()

        self.after(10,self.afficher)

    def ajout_balle(self,x,y,rayon,couleur,angle,vitesse):
        self.__balles.append(BalleMobile(x,y,rayon,couleur,angle,vitesse,self))

class FenPrincipale(Tk):
    def __init__(self):
        Tk.__init__(self)
        self.title('Balles aléatoires')
        self.__zoneAffichage = ZoneAffichage(400,300)
        self.__zoneAffichage.pack(side=TOP, padx=5, pady=5)
        f = Frame()
        f.pack(side=TOP, padx=5, pady=5)
        Button(f, text='Démarrer',width=8, command=self.demarrer).pack(side=LEFT, padx=5, pady=5)
        Button(f, text='Quitter',width=8, command=self.quitter).pack(side=LEFT, padx=5, pady=5)

        self.__generateur = GenerateurBalles(3000)

    def verifie_generateur(self):
        try:
            x,y,rayon,couleur,angle,vitesse = self.__generateur.get_queue().get(block=False)
        except queue.Empty:
            pass
        else:
            self.__zoneAffichage.ajout_balle(x,y,rayon,couleur,angle,vitesse)
        self.__zoneAffichage.after(50, self.verifie_generateur)

    
    def demarrer(self):
        self.__generateur.start()
        self.__zoneAffichage.afficher()
        self.verifie_generateur()

    def quitter(self):
        self.__generateur.stop()
        self.destroy()

class BalleMobile:
    def __init__(self, x, y, r, c, a, v, can):
        self.__x = x
        self.__y = y
        self.__rayon = r
        self.__couleur = c
        self.__angle = a
        self.__vitesse_x = v
        self.__vitesse_y= v
        self.__can = can
        self.__can_id = self.__can.create_oval(x - r, y - r, x + r, y + r, outline=c)

    def deplacement(self):

        dx = self.__vitesse_x  * math.cos(self.__angle)
        dy = self.__vitesse_y  * math.sin(self.__angle)

        if (self.__y + dy + self.__rayon> self.__can.winfo_height()) or (self.__y + dy - self.__rayon < 0):
            self.__vitesse_y = -self.__vitesse_y
        if (self.__x + dx + self.__rayon > self.__can.winfo_width()) or (self.__x + dx - self.__rayon < 0):
            self.__vitesse_x = -self.__vitesse_x

        self.__x += dx
        self.__y += dy

        self.__can.move(self.__can_id, dx, dy)

class GenerateurBalles(Thread):
    def __init__(self,capaciteMax):
        Thread.__init__(self)
        self.__queue = queue.Queue(capaciteMax)
        self.__running = True

    def run(self):
        while self.__running:
            if randint(0,1000) < 60:
                print("Création d'une nouvelle balle")
                x, y = randint(100, 150), randint(100, 150)
                rayon = randint(10,30)
                couleur = choice(['green', 'white', 'yellow', 'blue'])
                angle = math.radians(randint(1, 360))
                vitesse = randint(2, 5)
                self.__queue.put([x,y,rayon,couleur,angle,vitesse])

            time.sleep(0.01) 

    def stop(self):
        self.__running = Fal
    def get_queue(self):
        return self.__queue


if __name__ == '__main__':
    fen = FenPrincipale()
    fen.mainloop()

