# Laboratorio 3 Introducci[on a ROS
### Gustavo Adolfo Ropero Bastidas
### Joan Sebasti[an Arcila Cardozo
______________________________________
### Configuraci[on con MATLAB
'''
%%
rosinit
%%
velPub = rospublisher('/turtle1/cmd_vel','geometry_msgs/Twist')
velMsg = rosmessage(velPub)
%%
velMsg.Linear.X = 2;
velMsg.Linear.Y = 2;
send(velPub, velMsg);
pause(1)
'''
%%
rosinit
%%
posPub = rospublisher('/turtle1/pose','turtlesim/Pose'); 
posMsg = rosmessage(posPub)
%%
%
%
%
%
posMsg.X = 1;
posMsg.Y = 1;
posMsg.Theta=2.304;
posMsg.LinearVelocity=3;
posMsg.AngularVelocity=1.2;
send(posPub,posMsg) 
pause(1)
'''
### Configuraci[on con Python
'''
#!/usr/bin/env python

import rospy
from geometry_msgs.msg import Twist
from turtlesim.srv import TeleportAbsolute, TeleportRelative
import termios, sys, os
from numpy import pi

def getkey():
    fd = sys.stdin.fileno()
    old = termios.tcgetattr(fd)
    new = termios.tcgetattr(fd)
    new[3] = new[3] & ~termios.ICANON & ~termios.ECHO
    new[6][termios.VMIN] = 1
    new[6][termios.VTIME] = 0
    termios.tcsetattr(fd, termios.TCSANOW, new)
    c = None
    try:
        key = os.read(fd, 1)
    finally:
        termios.tcsetattr(fd, termios.TCSAFLUSH, old)
    return key

def rotate():
    rospy.init_node('my_turtle', anonymous=True)
    vel_pub = rospy.Publisher('/turtle1/cmd_vel', Twist, queue_size=10)
    vel_msg = Twist()

    while True:
        key = getkey()
        key = key.decode('utf-8', errors='replace')
        
        try:
            if key.lower() == 'w':
                vel_msg.linear.x = 1
                vel_msg.angular.z = 0

            elif key.lower() == 'a':
                vel_msg.angular.z = pi/4
                vel_msg.linear.x = 0

            elif key.lower() == 'd':
                vel_msg.angular.z = -pi/4
                vel_msg.linear.x = 0

            elif key.lower() == 's':     
                vel_msg.linear.x = -1
                vel_msg.angular.z = 0

            elif key.lower() == 'q':    # Para finalizar ejecución del código
                break

            elif key.lower() == ' ':
                turtle_teleport = rospy.ServiceProxy('turtle1/teleport_absolute',TeleportAbsolute)
                turtle_teleport(5.5,5.5,0)  

            elif key.lower() == 'r':
                vel_msg.linear.x = 0        # Rotación de 180° o sea pi radianes
                vel_msg.angular.z = pi
                
            vel_pub.publish(vel_msg)

        except rospy.ROSInterruptException:
            pass

if __name__ == '__main__':
    try:
        rotate()
    except rospy.ROSInterruptException:
        pass

'''Python



