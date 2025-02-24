# Tarea 1: Problema de Empaquetamiento y Corte 3D (3D-CPP) con Rotación

El presente contiene una implementación en Python del **Problema de Empaquetamiento y Corte 3D (3D-CPP)** utilizando la biblioteca **PuLP**. El problema consiste en empacar un conjunto de cajas rectangulares y tridimensionales dentro de un único contenedor, maximizando la cantidad de cajas empacadas.

## Descripción del Problema

El **Problema de Empaquetamiento y Corte 3D (3D-CPP)** es un problema de optimización en estudio, el cual tiene como objetivo la ubicación de elementos rectangulares tridimensionales más pequeños dentro de un contenedor de mayor tamaño, sin que dichos elementos se solapen. En este caso, el problema se amplía para permitir la **rotación** de los elementos, lo que significa que cada uno puede ser colocado en cualquiera de las seis orientaciones posibles.

### Entradas del modelo
- **Dimensiones del contenedor**: Largo `L`, Ancho `W` y Alto `H`.
- **Cajas**: Una lista de cajas rectangulares tridimensionales, donde cada uno está definido por sus dimensiones `(p_i, q_i, r_i)`.

### Salidas de la implementación del modelo
- **Posiciones**: Coordenadas `(x_i, y_i, z_i)` de la esquina inferior izquierda frontal de cada elemento.
- **Orientaciones**: Orientación de cada elemento, indicando qué dimensión (largo, ancho o alto) se alinea con los ejes X, Y o Z.

### Objetivo de la implementación
Maximizar la cantidad de elementos empaquetados dentro del contenedor, sin que estos se solapen.

---

## Formulación Matemática

El problema se modela como un **Problema de Programación Lineal Entera Mixta (MILP)**. La formulación se compone principalmente por:

### Variables de Decisión
- `x_i, y_i, z_i`: Coordenadas de la esquina inferior izquierda frontal de la caja `i`.
- `lx_i, ly_i, lz_i`: Variables binarias que indican si el largo de la caja `i` está alineada con los ejes X, Y o Z.
- `wx_i, wy_i, wz_i`: Variables binarias que indican si el ancho de la caja `i` está alineado con los ejes X, Y o Z.
- `hx_i, hy_i, hz_i`: Variables binarias que indican si la altura de la caja `i` está alineada con los ejes X, Y o Z.
- `a_ij, b_ij, c_ij, d_ij, e_ij, f_ij`: Variables binarias que indican la posición relativa de las cajas `i` y `j`.

### Restricciones
1. **Restricciones del Contenedor**:
   - Cada caja debe caber dentro del contenedor (no puede quedar ninguna por fuera).
2. **Restricciones de Orientación**:
   - Cada dimensión (largo, ancho y alto) de un elemento debe estar alineada con exactamente un eje (X, Y o Z).
   - Cada eje (X, Y, Z) debe ser asignado a exactamente una dimensión del objeto.
3. **Restricciones de No Solapamiento**:
   - Las cajas no deben solaparse. Esto se garantiza para este caso usando el método de la gran-M, así como variables binarias de posicionamiento relativo.

### Función Objetivo
Maximizar la cantidad de elementos empacados dentro del contenedor, esto a partir de la suma de las variables que indican la asignación de las dimensiones a los ejes X, Y y Z, 

- Siendo estas variables `lx_i`, `ly_i`, `lz_i`, `wx_i`, `wy_i`, `wz_i`, `hx_i`, `hy_i`, `hz_i` las que asignan las dimensiones de la caja `i` a los ejes del contenedor.

## Implementación del Código

Se implementa en Python llamando a la biblioteca **PuLP** para resolver problemas de programación lineal. El solver utilizado es el predeterminado de PuLP (CBC).

### Dependencias
- Python 3.x
- PuLP (`pip install pulp`)
- Matplotlib (opcional para la visualización de los resultados)

### Estructura General de la Codificación
1. **Parámetros**:
   - Definimos las dimensiones del contenedor `L, W, H`.
   - Definimos la lista de cajas con sus dimensiones.

2. **Variables de Decisión**:
   - Variables para las coordenadas `(x_i, y_i, z_i)`.
   - Variables para la orientación de las cajas `(lx_i, ly_i, lz_i, wx_i, wy_i, wz_i, hx_i, hy_i, hz_i)`.
   - Variables para el posicionamiento relativo entre dos cajas`(a_ij, b_ij, c_ij, d_ij, e_ij, f_ij)`.

3. **Restricciones**:
   - Restricciones de orientación.
   - Restricciones del contenedor.
   - Restricciones de no solapamiento.

4. **Función Objetivo**:
   - Maximizar la cantidad de cajas empacadas.

5. **Solver**:
   - Resolver el problema utilizando el solucionador predeterminado de PULP.

6. **Salida**:
   - Imprimir las posiciones y orientaciones de los elementos empaquetados.

---

## Cómo Usar

1. **Instalar Dependencias**:
   - Instalamos e importamos pulp para ejecutar un modelo MIP para la solución exacta del 3D-CCP
   ```bash
   pip install pulp
   import pulp
2. **Definimos las dimensiones del contenedor**:
   - Damos los respectivos valores al largo L, ancho W y alto H
   ```bash
    # Definimos las dimensiones del contenedor
    L = 5  # Largo del contenedor
    W = 5  # Ancho del contenedor
    H = 5  # Alto del contenedor
3. **Definimos las dimensiones de las cajas a empaquetar**:
   - Damos los respectivos valores al largo p_i, ancho q_i y alto r_i. Asimismo, definimos la cantidad de cajas n
   ```bash
   # Definimos las cajas (p_i, q_i, r_i)
    items = [
      (3, 2, 2),  # Item 0
      (3, 2, 2),  # Item 1
      (3, 2, 2),  # Item 2
      (3, 2, 2),  # Item 3
      (3, 2, 2),  # Item 4
      (3, 2, 2),  # Item 5
      (4, 2, 1),  # Item 6
      (4, 2, 1),  # Item 7
      (4, 2, 1),  # Item 8
      (4, 2, 1),  # Item 9
      (4, 2, 1),  # Item 10
      (4, 2, 1),  # Item 11
    ]
    n = len(items)  # Número de cajas
4. **Creamos el modelo de optimización**:
   ```bash
   # Creamos el modelo
   model = pulp.LpProblem("3D_Cutting_and_Packing_Problem", pulp.LpMaximize)
5. **Definimos las variables de decisión del modelo**:
   - Variables continuas que representan las coordenadas de la esquinza del frente inferior izquierdo de la caja i al interior del contenedor
   ```bash
    # x_i, y_i, z_i: Coordenadas de la esquina del fondo-izquierda-enfrente de la caja i
    x = pulp.LpVariable.dicts("x", range(n), lowBound=0, upBound=L, cat="Continuous")
    y = pulp.LpVariable.dicts("y", range(n), lowBound=0, upBound=W, cat="Continuous")
    z = pulp.LpVariable.dicts("z", range(n), lowBound=0, upBound=H, cat="Continuous")
   ```
   - Variables binarias que representan a qué eje del contenedor (cuando la caja es ubicada en su interior) es paralelo el largo. ancho y alto de la caja i, respectivamente
   ```bash
    # Variables binarias de orientación
    # lx_i, ly_i, lz_i: Largo de la caja i es paralelo al eje X-, Y-, o Z-
      lx = pulp.LpVariable.dicts("lx", range(n), cat="Binary")
      ly = pulp.LpVariable.dicts("ly", range(n), cat="Binary")
      lz = pulp.LpVariable.dicts("lz", range(n), cat="Binary")
      
    # wx_i, wy_i, wz_i: Ancho de la caja i es paralelo al eje X-, Y-, o Z-
      wx = pulp.LpVariable.dicts("wx", range(n), cat="Binary")
      wy = pulp.LpVariable.dicts("wy", range(n), cat="Binary")
      wz = pulp.LpVariable.dicts("wz", range(n), cat="Binary")
      
    # hx_i, hy_i, hz_i: Alto de la caja i es paralelo al eje X-, Y-, o Z-
      hx = pulp.LpVariable.dicts("hx", range(n), cat="Binary")
      hy = pulp.LpVariable.dicts("hy", range(n), cat="Binary")
      hz = pulp.LpVariable.dicts("hz", range(n), cat="Binary")
      ```
   - Variables binarias que representan la posición relativa entre dos cajas i y j.
     - a: izquierda, b: derecha, c: atrás, d: adelante, e: abajo, f: arriba-
     - Ejemplo: Si `a_ij` = 1, la caja `i` debe estar situada a la izquierda de la caja `k`
    ```bash
      # Variables binarias para el posicionamiento relativo de la caja i y la caja j
      a = pulp.LpVariable.dicts("a", [(i, j) for i in range(n) for j in range(n) if i != j], cat="Binary")
      b = pulp.LpVariable.dicts("b", [(i, j) for i in range(n) for j in range(n) if i != j], cat="Binary")
      c = pulp.LpVariable.dicts("c", [(i, j) for i in range(n) for j in range(n) if i != j], cat="Binary")
      d = pulp.LpVariable.dicts("d", [(i, j) for i in range(n) for j in range(n) if i != j], cat="Binary")
      e = pulp.LpVariable.dicts("e", [(i, j) for i in range(n) for j in range(n) if i != j], cat="Binary")
      f = pulp.LpVariable.dicts("f", [(i, j) for i in range(n) for j in range(n) if i != j], cat="Binary")

  5. **Definimos la función objetivo del modelo**:
   - Corresponde a la maximización de sumatoria de las variables de decisión referentes a la orientación de la caja i. Esto dado que cada caja i debe tener una orientación con respecto a los ejes X-, Y- y Z-, asegurando así la asignación de las cajas al contenedor
     ```bash
      # Función objetivo: Maximizar la asignación de las cajas en el contenedor
      model += pulp.lpSum([lx[i] + ly[i] + lz[i] + wx[i] + wy[i] + wz[i] + hx[i] + hy[i] + hz[i] for i in range(n)]), "Total-scheduling"
  7. **Definimos las restricciones del modelo**:
   - Establecemos que cada dimensión de la caja i debe ser paralela a uno de los ejes X-, Y- y Z- exactamente una vez. Una vez escogida la rotación con la que se ubica la caja i, su respectiva dimensión solo puede ser paralela a un eje a la vez
     ```bash
     for i in range(n):
      # Cada dimensión de la caja i debe ser paralela exactamente a uno de los ejes (X, Y, or Z)
        model += lx[i] + ly[i] + lz[i] == 1, f"parallel_p_{i}"
        model += wx[i] + wy[i] + wz[i] == 1, f"parallel_q_{i}"
        model += hx[i] + hy[i] + hz[i] == 1, f"parallel_r_{i}"
        model += lx[i] + wx[i] + hx[i] == 1, f"parallel_x_{i}"
        model += ly[i] + wy[i] + hy[i] == 1, f"parallel_y_{i}"
        model += lz[i] + wz[i] + hz[i] == 1, f"parallel_z_{i}"
        ```
  - Dentro del ciclo for anterior: Las dimensiones de la caja i sumadas a las de las cajas contiguas no pueden superar las medidas L, W y H del contenedor
      ```bash
          model += x[i] + items[i][0] * lx[i] + items[i][1] * wx[i] + items[i][2] * hx[i] <= L, f"fit_x_{i}"
          model += y[i] + items[i][0] * ly[i] + items[i][1] * wy[i] + items[i][2] * hy[i] <= W, f"fit_y_{i}"
          model += z[i] + items[i][0] * lz[i] + items[i][1] * wz[i] + items[i][2] * hz[i] <= H, f"fit_z_{i}"

  - Dentro del ciclo, agregamos otro tal que: Se restringen los solapamientos entre dos cajas i y j seleccionadas, dependiendo de si son o no contiguas en el eje a evaluar. Definimos M como el máximo entre las dimensiones del contenedor, pero se le podría asignar un valor mayor para múltiples contenedores o para un modelo con una mayor cantidad de parámetros
      ```bash
        for j in range(n):
          if i < j:
              # Restricciones de no solapamiento
              M = max(L, W, H)
              model += x[i] + items[i][0] * lx[i] + items[i][1] * wx[i] + items[i][2] * hx[i] <= x[j] + M * (1 - a[i, j]), f"no_overlap_x_{i}_{j}"
              model += x[j] + items[j][0] * lx[j] + items[j][1] * wx[j] + items[j][2] * hx[j] <= x[i] + M * (1 - b[i, j]), f"no_overlap_x_{j}_{i}"
              model += y[i] + items[i][0] * ly[i] + items[i][1] * wy[i] + items[i][2] * hy[i] <= y[j] + M * (1 - c[i, j]), f"no_overlap_y_{i}_{j}"
              model += y[j] + items[j][0] * ly[j] + items[j][1] * wy[j] + items[j][2] * hy[j] <= y[i] + M * (1 - d[i, j]), f"no_overlap_y_{j}_{i}"
              model += z[i] + items[i][0] * lz[i] + items[i][1] * wz[i] + items[i][2] * hz[i] <= z[j] + M * (1 - e[i, j]), f"no_overlap_z_{i}_{j}"
              model += z[j] + items[j][0] * lz[j] + items[j][1] * wz[j] + items[j][2] * hz[j] <= z[i] + M * (1 - f[i, j]), f"no_overlap_z_{j}_{i}"
  
              # Al menos una de las restricciones de no solapamiento se activa
              model += a[i, j] + b[i, j] + c[i, j] + d[i, j] + e[i, j] + f[i, j] >= 1, f"no_overlap_abcdef_{i}_{j}"
      ```
  8. **Ejecutamos la solución del modelo e imprimimos los resultados**:
     ```bash
        # Resolvemos el modelo
         model.solve()
         
         # Resultados impresos
         if pulp.LpStatus[model.status] == "Optimal":
             print("Solución óptima:")
             for i in range(n):
                 print(f"Item {i}: Posición ({x[i].varValue}, {y[i].varValue}, {z[i].varValue}), Orientación (lx={lx[i].varValue}, ly={ly[i].varValue}, lz={lz[i].varValue}, wx={wx[i].varValue}, wy={wy[i].varValue}, wz={wz[i].varValue}, hx={hx[i].varValue}, hy={hy[i].varValue}, hz={hz[i].varValue})")
         else:
             print("No se encontró una solución óptima.")
     
  9. **Visualización (Opcional)**:
      - Usamos matplotlib para visualizar los resultados
      ```bash
        import matplotlib.pyplot as plt
      from mpl_toolkits.mplot3d.art3d import Poly3DCollection
      
      # Posiciones finales de las cajas
      sol_x = {i: pulp.value(x[i]) for i in range(n)}
      sol_y = {i: pulp.value(y[i]) for i in range(n)}
      sol_z = {i: pulp.value(z[i]) for i in range(n)}
      
      # Dimensiones reales según la orientación
      sol_l = {i: items[i][0] * pulp.value(lx[i]) + items[i][1] * pulp.value(wx[i]) + items[i][2] * pulp.value(hx[i]) for i in range(n)}
      sol_w = {i: items[i][0] * pulp.value(ly[i]) + items[i][1] * pulp.value(wy[i]) + items[i][2] * pulp.value(hy[i]) for i in range(n)}
      sol_h = {i: items[i][0] * pulp.value(lz[i]) + items[i][1] * pulp.value(wz[i]) + items[i][2] * pulp.value(hz[i]) for i in range(n)}
      
      # Figura 3D
      fig = plt.figure(figsize=(8, 8))
      ax = fig.add_subplot(111, projection="3d")
      
      # Dibujamos el contenedor
      ax.set_xlim([0, L])
      ax.set_ylim([0, W])
      ax.set_zlim([0, H])
      ax.set_xlabel("X")
      ax.set_ylabel("Y")
      ax.set_zlabel("Z")
      
      # Función para dibujar cubo
      def dibujar_cubo(ax, x, y, z, dx, dy, dz, color):
          vertices = [
              [[x, y, z], [x + dx, y, z], [x + dx, y + dy, z], [x, y + dy, z]],
              [[x, y, z+ dz], [x + dx, y, z + dz], [x + dx, y + dy, z + dz], [x, y + dy, z + dz]],
              [[x, y, z], [x, y, z + dz], [x + dx, y, z + dz], [x + dx, y, z]],
              [[x, y + dy, z], [x, y + dy, z + dz], [x + dx, y + dy, z + dz], [x + dx, y + dy, z]],
              [[x, y, z], [x, y + dy, z], [x, y + dy, z + dz], [x, y, z + dz]],
              [[x + dx, y, z], [x + dx, y + dy, z], [x + dx, y + dy, z + dz], [x + dx, y, z + dz]],
          ]
          ax.add_collection3d(Poly3DCollection(vertices, facecolors=color, linewidths=1, edgecolors="k", alpha=0.5))
      
      # Dibujar cada caja en la solución
      colores = plt.cm.get_cmap("tab10", n)
      for i in range(n):
          if pulp.value(lx[i]+ly[i]+lz[i]+wx[i]+wy[i]+wz[i]+hx[i]+hy[i]+hz[i])/9 > 0:  # Si el ítem está dentro del contenedor
              dibujar_cubo(ax, sol_x[i], sol_y[i], sol_z[i], sol_l[i], sol_w[i], sol_h[i], colores(i))
      
      plt.title("Visualización 3D de la Solución")
      plt.show()
      ```
   ## Análisis y Posibles Mejoras
   - Aproximarse a la solución haciendo uso de otros métodos de optimización o algoritmos exactos y obtener una mejor solución a partir de los resultados.
   - Hacer uso de métodos constructivos para aproximarnos a la factibilidad, seguido de métodos heurísticos para encontrar una buena solución más eficientemente al ser este un problema NP-Hard.
   - Graficar la solución para entender la dinámica del posicionamiento de las cajas, así como para verificar su no solapamiento.

   ## Autoría
   - Este modelo y su respectivo código fueron desarrollados como parte de una ejercicio de optimización entera-mixta del 3DCPP.
   - Su formulación obedece a una adaptación propia a un (1) contenedor a partir del modelo generalizado de múltiples contenedores propuesto por Chen et al. (1995), y expuesto por Fernandes et al. (2019) en su artículo "Exact methods for three-dimensional cutting and packing: A
comparative study concerning single container problems".

   ## Referencias Bibliográficas
   - Chen, C., Lee, S., & Shen, Q. (1995). "An analytical model for the container loading problem."
   - Fernandes, E., Machado, T., & Wauters, T. (2019)."Exact methods for three-dimensional cutting and packing: A comparative study concerning single container problems."
   - PuLP Documentation: https://coin-or.github.io/pulp/
