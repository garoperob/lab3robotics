# Laboratorio 3 Introducción a ROS
### Gustavo Adolfo Ropero Bastidas
### Joan Sebastián Arcila Cardozo
______________________________________
### Configuración con MATLAB
- Siguiendo las instrucciones del laboratorio se instala ROS en una máquina virtual con sistema operativo Ubuntu 20.04 LTS y se descarga matlab en la misma. Con esto se abren dos terminales usando los comandos, estos comandos permiten inicializar el simulador de turtle
![Image](https://github.com/garoperob/lab3robotics/blob/main/imagenes/rosinit.png)
![Image](https://github.com/garoperob/lab3robotics/blob/main/imagenes/turtleUp.png) 
```MATLAB
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
```
El siguiente ejercicio es crear un script para suscribirse a la pose mediante la función rossubscriber(), mediante el siguiente código. Adicionalmente, se envían todos los valores usando los atritbutos de la función rosmessage()
```MATLAB
%%
rosinit
%%
posSub = rossubscriber('/turtle1/pose','turtlesim/Pose')
posMsg = rosmessage(posSub)
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
pause(1)
```
![Image](https://github.com/garoperob/lab3robotics/blob/main/imagenes/resultsROSpublisher.png)

Finalmente, la forma en que se apaga la consola global es usando el comando rosshutdown
![Image](https://github.com/garoperob/lab3robotics/blob/main/imagenes/Screenshot%20from%202024-04-12%2015-35-22.png)
### Configuración con Python
La última parte del laboratorio requiere manipular de forma dinámica el simulador turtle mediante un archivo .py. Esto se hace aplicando las librerías mostradas en el laboratorio
```Python
#!/usr/bin/env python

import rospy
from geometry_msgs.msg import Twist
from turtlesim.srv import TeleportAbsolute, TeleportRelative
import termios, sys, os
from numpy import pi
```
Por consiguiente se usa el código del blog compartido para la lectura de teclado
```Python
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
```
Finalmente, se construye la función de movimiento que incluye, el desplazamiento mediante las teclas W y S, y la rotación en sentido antihorario y horario con las teclas A y D, respectivamente. Cada una de los movimientos están basados en las propiedades de la función Twist() que permite modificar las parámetros de avance en x y rotación alrededor de z. El avance se eligió de 1 unidad y la rotación de pi/4 se en un sentido o en el otro. Además, la barra espaciadora teletransporta al punto de origen trasladado 5.5 tanto en x como en y, r permite rotación de 180° y q finaliza la ejecución del script.
```Python
def move():
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
        move()
    except rospy.ROSInterruptException:
        pass

```
![Image](https://github.com/garoperob/lab3robotics/blob/main/imagenes/catkin_pkg.png)
![Image](https://github.com/garoperob/lab3robotics/blob/main/imagenes/pythonTurtle.png)
## Análisis
Mediante el tópico '/turtle1/cmd_vel' se puede modificar la velocidad de la tortuga, usando el mensaje Twist. Esto permite cambiar elvalor de las propiedades que como los son avance linear, rotación angular. Por otra parte, en MATLAB se puede modificar la pose con el tópico '/turtle1/pose' que altera la posición en ambos ejes, ángulo, velocidad linear y angular, pero no altera la posición de la tortuga.
## Conclusiones
- La creación de un workspace usando la librería catkin no fue clara. Sin embargo, mediante investigación se pudo crear el paquete hello_turtle requerido y conectarlo al archivo .py.
- La implementación del laboratorio se pudo llevar a cabo sin problema en Oracle VM. No obstante, se evidencia que carece de capacidad para mayores proyectos.
- El uso de ROS se puede llevar a cabo mediante terminales de Linux como ejecuatando MATLAB, permitiendo el último una ventaja al ser más fácil de usar.
