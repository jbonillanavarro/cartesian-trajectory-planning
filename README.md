# LAB 1: cartesian_trajectory_planning

The package is structured as follows

* bringup: launch files and ros2_controller configuration
* config/poses.yaml: YAML file with the poses to test the linear
* controller: a controller for the 6-DOF robot
* description: the 6-DOF robot description
* hardware: ros2_control hardware interface
* reference_generator: A KDL-based reference generator for a fixed trajectory

## Luanch trajectory 

In one terminal, run:

```bash
ros2 launch cartesian_trajectory_planning r6bot_controller.launch.py
```

In another terminal, run:

```bash
ros2 launch cartesian_trajectory_planning send_trajectory.launch.py
```
## Realización

A continuación, se detalla de forma concisa la secuencia de trabajo llevada a cabo según el código y el enunciado:
1. Configuración del entorno: Preparación del espacio de trabajo en ROS 2 Humble, instalación de las dependencias requeridas (KDL, Eigen, tf2) y clonación del paquete cartesian_trajectory_planning.
2. Interpolación Cartesiana (Ejercicio 1): Se completó la función PoseInterpolation para calcular los puntos intermedios entre dos extremos. Esto implica una interpolación lineal para la posición espacial y una interpolación esférica (SLERP) para la orientación utilizando cuaterniones, asegurando siempre tomar el camino de rotación más corto.
3. Transiciones Suaves (Ejercicio 2): Se implementó la función ComputeNextCartesianPose empleando el método de Taylor. Esto permite concatenar dos segmentos rectilíneos evitando cambios bruscos de velocidad en el punto de unión, aplicando para ello ecuaciones cuadráticas en un intervalo de tiempo de suavizado $[-\tau, \tau]$.
4. Cinemática Inversa y Trayectoria ROS: A través de un bucle temporal, se genera cada punto cartesiano y se convierte a coordenadas articulares mediante el solver de cinemática inversa (IK) de la librería KDL. Estos ángulos se empaquetan y publican como un mensaje estándar de ROS (JointTrajectory) dirigido al controlador del robot.
5. Registro y Visualización: Finalmente, los datos de posición espacial y orientación (convertida a ángulos Roll, Pitch, Yaw) se escriben iterativamente en un archivo .csv para poder graficarlos externamente y validar la suavidad de las curvas resultantes.

Se pueden observar los resultados de las simulaciones en las tres imagenes siguientes:

<img width="886" height="236" alt="image" src="https://github.com/user-attachments/assets/e69d2496-e454-48d7-814d-84a36de583d0" />

<img width="886" height="798" alt="image" src="https://github.com/user-attachments/assets/7322df5a-0f87-4e01-8180-2aa8c9d8cd02" />



<img width="886" height="812" alt="image" src="https://github.com/user-attachments/assets/c4e28bc9-4653-409e-b652-7f586c8e4503" />





## Questions

### What happens when you change the value of τ?
El parámetro $\tau$ define la ventana de tiempo en la que se aplica el suavizado alrededor del punto intermedio $P_1$.
* Si aumentas $\tau$: La curva de transición es más suave (requiere menor aceleración), pero el robot se desvía más del punto de paso exacto $P_1$.
* Si disminuyes $\tau$: El robot pasa más cerca de $P_1$, pero la curva es más cerrada, lo que exige cambios de velocidad más bruscos y mayor esfuerzo a los motores.

### What happens when you change the value of T?
El parámetro $T$ representa el tiempo total asignado para recorrer un segmento lineal de la trayectoria.
* Si aumentas $T$: El robot dispone de más tiempo para cubrir la misma distancia, por lo que se mueve más despacio.
* Si disminuyes $T$: El robot debe cubrir la misma distancia en menos tiempo, por lo que se mueve más rápido.

### Can you change the velocity of the robot? How?
Sí. En este tipo de interpolación, la velocidad no se define directamente, sino que es el resultado del espacio a recorrer dividido por el tiempo. Se puede cambiar dos maneras:
* Modificando el tiempo ($T$): Reducir $T$ para el mismo segmento hará que el robot vaya más rápido.
* Modificando la distancia: Alejar los puntos espaciales definidos en el archivo poses.yaml manteniendo el mismo valor de $T$ obligará al robot a moverse a mayor velocidad para llegar a tiempo.









