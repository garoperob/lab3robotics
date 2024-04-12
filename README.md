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
