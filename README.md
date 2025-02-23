# Tarea 1: Problema de Empaquetamiento y Corte 3D (3D-CPP) con Rotación

Este repositorio contiene una implementación en Python del **Problema de Empaquetamiento y Corte 3D (3D-CPP)** utilizando la biblioteca **PuLP**. El problema consiste en empaquetar un conjunto de elementos rectangulares tridimensionales dentro de un único contenedor, maximizando la cantidad de elementos empaquetados. Los elementos pueden rotarse en cualquiera de las seis orientaciones posibles.

## Descripción del Problema

El **Problema de Empaquetamiento y Corte 3D (3D-CPP)** es un problema de optimización ampliamente estudiado, cuyo objetivo es colocar un conjunto de elementos rectangulares tridimensionales más pequeños dentro de un contenedor de mayor tamaño, sin que se solapen. En este caso, el problema se amplía para permitir la **rotación** de los elementos, lo que significa que cada uno puede ser colocado en cualquiera de las seis orientaciones posibles.

### Entradas
- **Dimensiones del contenedor**: Largo `L`, Ancho `W` y Alto `H`.
- **Elementos**: Una lista de objetos rectangulares tridimensionales, donde cada uno está definido por sus dimensiones `(l_i, w_i, h_i)`.

### Salidas
- **Posiciones**: Coordenadas `(x_i, y_i, z_i)` de la esquina inferior izquierda frontal de cada elemento.
- **Orientaciones**: Orientación de cada elemento, indicando qué dimensión (largo, ancho o alto) se alinea con los ejes X, Y o Z.

### Objetivo
Maximizar la cantidad de elementos empaquetados dentro del contenedor sin que se solapen.

---

## Formulación Matemática

El problema se modela como un **Problema de Programación Lineal Entera Mixta (MILP)**. Los principales componentes de la formulación son:

### Variables de Decisión
- `x_i, y_i, z_i`: Coordenadas de la esquina inferior izquierda frontal del elemento `i`.
- `lx_i, ly_i, lz_i`: Variables binarias que indican si la longitud del elemento `i` está alineada con los ejes X, Y o Z.
- `wx_i, wy_i, wz_i`: Variables binarias que indican si el ancho del elemento `i` está alineado con los ejes X, Y o Z.
- `hx_i, hy_i, hz_i`: Variables binarias que indican si la altura del elemento `i` está alineada con los ejes X, Y o Z.
- `a_ij, b_ij, c_ij, d_ij, e_ij, f_ij`: Variables binarias para la posición relativa de los elementos `i` y `j`.

### Restricciones
1. **Restricciones de Orientación**:
   - Cada dimensión (largo, ancho y alto) de un elemento debe estar alineada con exactamente un eje.
   - Cada eje (X, Y, Z) debe ser asignado a exactamente una dimensión del objeto.

2. **Restricciones del Contenedor**:
   - Cada elemento debe caber dentro del contenedor.

3. **Restricciones de No Solapamiento**:
   - Los elementos no deben superponerse. Esto se garantiza mediante el método **big-M** y el uso de variables binarias de posicionamiento relativo.

### Función Objetivo
Maximizar la cantidad de elementos empaquetados dentro del contenedor.

donde `s_i` es una variable binaria que indica si el elemento `i` está colocado en el contenedor.

---

## Implementación del Código

El código está implementado en Python utilizando la biblioteca **PuLP** para programación lineal. El solucionador utilizado es el solucionador predeterminado incluido con PuLP (CBC).

### Dependencias
- Python 3.x
- PuLP (`pip install pulp`)
- Matplotlib (para visualización, opcional)

### Estructura del Código
1. **Entradas**:
   - Definir las dimensiones del contenedor `L, W, H`.
   - Definir la lista de elementos con sus dimensiones.

2. **Variables de Decisión**:
   - Variables para las posiciones `(x_i, y_i, z_i)`.
   - Variables para la orientación `(lx_i, ly_i, lz_i, wx_i, wy_i, wz_i, hx_i, hy_i, hz_i)`.
   - Variables para el posicionamiento relativo `(a_ij, b_ij, c_ij, d_ij, e_ij, f_ij)`.

3. **Restricciones**:
   - Restricciones de orientación.
   - Restricciones del contenedor.
   - Restricciones de no solapamiento.

4. **Función Objetivo**:
   - Maximizar la cantidad de elementos empaquetados.

5. **Solucionador**:
   - Resolver el problema utilizando el solucionador CBC.

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
     - a: izquierda, b: derecha, c: atrás, d: adelante, e: abajor, f: arriba
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
      model += pulp.lpSum([lx[i]+ly[i]+lz[i]+wx[i]+wy[i]+wz[i]+hx[i]+hy[i]+hz[i] for i in range(n)]), "Total-scheduling"
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
  8. **Ejecutamos la solución del modelo**: 
