# Space-raiders
import turtle
import os
import math
import random

#User Help
#to move left Press LEFT ARROW key
#To move right press RIGHT ARROW key
#Press SPACEBAR to fire bullets

#Set up the screen
wn = turtle.Screen()
wn.bgcolor("black")
wn.title("Space Invaders")
wn.bgpic("space_invader_background.gif")

#Picture for characters
turtle.register_shape("invader.gif")

#Draw border
border_pen = turtle.Turtle()
border_pen.speed(0)
border_pen.color("white")
border_pen.penup()
border_pen.setposition(-300,-300)
border_pen.pendown()
border_pen.pensize(3)
for side in range(4):
	border_pen.fd(600)
	border_pen.lt(90)
border_pen.hideturtle()	

#Set the score to 0
score = 0

#Draw the score
score_pen = turtle.Turtle()
score_pen.speed(0)
score_pen.color("white")
score_pen.penup()
score_pen.setposition(-290, 280)
scorestring = "Score: %s" %score
score_pen.write(scorestring, False, align="left", font=("Arial", 14, "normal"))
score_pen.hideturtle()

#Create the player turtle
player = turtle.Turtle()
player.color("yellow")
player.shape("triangle")
player.penup()
player.speed(0)
player.setposition(0, -250)
player.setheading(90)

playerspeed = 15

#Number of enemies
number_of_enemies = 5
#Create an empty list of enemies
enemies = []

#Add enemies to the list
for i in range(number_of_enemies):
	#Create the enemy
	enemies.append(turtle.Turtle())

for enemy in enemies:
	enemy.color("red")
	enemy.shape("invader.gif")
	enemy.penup()
	enemy.speed(0)
	x = random.randint(-200, 200)
	y = random.randint(100, 250)
	enemy.setposition(x, y)

enemyspeed = 2


#Player's weapon
bullet = turtle.Turtle()
bullet.color("orange")
bullet.shape("circle")
bullet.penup()
bullet.speed(0)
bullet.setheading(90)
bullet.shapesize(0.5, 0.5)
bullet.hideturtle()

bulletspeed = 20

#Define bullet state
#ready - ready to fire
#fire - bullet is firing
bulletstate = "ready"


#Player Movement
def move_left():
	x = player.xcor()
	x -= playerspeed
	if x < -280:
		x = - 280
	player.setx(x)
	
def move_right():
	x = player.xcor()
	x += playerspeed
	if x > 280:
		x = 280
	player.setx(x)
	
def fire_bullet():
	#Declare bulletstate as a global 
	global bulletstate
	if bulletstate == "ready":
		bulletstate = "fire"
		#Move the bullet to the just above the player
		x = player.xcor()
		y = player.ycor() + 10
		bullet.setposition(x, y)
		bullet.showturtle()

def isCollision(t1, t2):
	distance = math.sqrt(math.pow(t1.xcor()-t2.xcor(),2)+math.pow(t1.ycor()-t2.ycor(),2))
	if distance < 15:
		return True
	else:
		return False
#Keyboard controls
turtle.listen()
turtle.onkey(move_left, "Left")
turtle.onkey(move_right, "Right")
turtle.onkey(fire_bullet, "space")

#Main game loop
while True:
	
	for enemy in enemies:
		#Move the enemy
		x = enemy.xcor()
		x += enemyspeed
		enemy.setx(x)

		#Move the enemy back and down
		if enemy.xcor() > 280:
			#Move all enemies down
			for e in enemies:
				y = e.ycor()
				y -= 40
				e.sety(y)
			#Change enemy direction
			enemyspeed *= -1
		
		if enemy.xcor() < -280:
			#Move all enemies down
			for e in enemies:
				y = e.ycor()
				y -= 40
				e.sety(y)
			#Change enemy direction
			enemyspeed *= -1
			
		#Collision between the bullet and the enemy
		if isCollision(bullet, enemy):
			#Reset the bullet
			bullet.hideturtle()
			bulletstate = "ready"
			bullet.setposition(0, -400)
			#Reset the enemy
			x = random.randint(-200, 200)
			y = random.randint(100, 250)
			enemy.setposition(x, y)
			#Update the score
			score += 10
			scorestring = "Score: %s" %score
			score_pen.clear()
			score_pen.write(scorestring, False, align="left", font=("Arial", 14, "normal"))
		
		if isCollision(player, enemy):
			player.hideturtle()
			enemy.hideturtle()
			print ("Game Over")
			break

		
	#Move the bullet
	if bulletstate == "fire":
		y = bullet.ycor()
		y += bulletspeed
		bullet.sety(y)
	
	#Check to see if the bullet has gone to the top
	if bullet.ycor() > 275:
		bullet.hideturtle()
		bulletstate = "ready"

