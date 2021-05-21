# Configurar_fake_turtle_bot

A continuacion se describira un paso a paso para poder hacer funcionar el RBX1 junto con el nodo de teleoperate del turtle_bot

## 1) Instalacion del RBX1

* Se abre un terminal, vamos al workspace donde descargaremos el RBX1 
```
cd ~/catkin_ws/src/
```
* Una vez en el workspace podemos copiar los archivos de github de la siguiente manera 
```
git clone https://github.com/pirobot/rbx1.git
```
* o se puede instalar desde los repositorios de ros de la siguiente manera
```
sudo apt-get install ros-kinetic-rbx1  // Esta sentencia solo es valida para la version Kinetic de ROS
```

## 2) Instalacion de dependencias de Ubuntu

Para que todo funciones de manera adecuada y se pueda ejecutar el launch se deben instalar las dependencias de python de ROS para realizar esto se debe ejecutar el siguiente codigo en un temrinal
```
sudo apt-get install python-rosinstall
```

## 3) Ejecucion y prueba del Fake_turtlebot y el comando teleop

Ejecutamos cada una de las siguientes sentencias en terminales diferentes

```
roscore
roslaunch rbx1_bringup fake_turtlebot.launch
rosrun turtlesim turtle_teleop_key
rostopic list
```

Al ejecutar el comando "rostopic list" veremos que aparecen dos topics que son los siguientes

```
"/cmd_vel" 
"/turtle1/cmd_vel"
```

Por lo que se debe realizar un remap en el launch para permitir que se comuniquen estos topics

## 4) Cambios en el archivo launch

Para realizar el cambio se debe dirigir a la carpeta donde instalamos el RBX1 y buscamos el archivo "fake_turtlebot.launch" en este veremos lo siguiente

```
  <node name="arbotix" pkg="arbotix_python" type="arbotix_driver" output="screen" clear_params="true">
      <rosparam file="$(find rbx1_bringup)/config/fake_turtlebot_arbotix.yaml" command="load" />
      <param name="sim" value="true"/>
 </node>
 ```
 
 y se debe cambiar a lo siguiente:
 
 ```
   <node name="arbotix" pkg="arbotix_python" type="arbotix_driver" output="screen" clear_params="true">
      <!-- Se especifican parametros especificos para el nodo -->
      <rosparam file="$(find rbx1_bringup)/config/fake_turtlebot_arbotix.yaml" command="load" />
      <param name="sim" value="true"/>
      <!-- Se remapea el "topic" del RBX1 para poder leer la informacion del teleop -->
      <remap from="/cmd_vel" to="/turtle1/cmd_vel" /> 
 </node>
 ```
 
 O como segunda opcion eliminar el archivo y remplazarlo por el que se encuentra en el repositorio.
 
 ## 5) Prueba Final 
 
 Para probar los cambios realizados anteriormente volvemos a ejecutar las siguientes sentencias
 
```
roscore
roslaunch rbx1_bringup fake_turtlebot.launch
rosrun turtlesim turtle_teleop_key
```

Posteriormente procedemos a ejecutar el rviz para observar graficamente el robot moverse, esto se realiza de la siguiente manera

```
rosrun rviz rviz -d`rospack find rbx1_nav`/sim.rviz
```

Finalmente, en caso de que el robot se mueva a mucha velocidad podemos variar los parametros de la siguiente manera

``` 
rosparam set teleop_turtle/scale_linear 0.5 && rosrun turtlesim turtle_teleop_key
```

## Autores 

* Santiago Vasquez
