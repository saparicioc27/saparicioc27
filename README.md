# Tarea 1: Problema de Empaquetamiento y Corte 3D (3D-CPP) con Rotación

El presente contiene una implementación en Python del **Problema de Empaquetamiento y Corte 3D (3D-CPP)** utilizando la biblioteca **PuLP**. El problema consiste en empacar un conjunto de cajas rectangulares tridimensionales dentro de un único contenedor, maximizando el volumen utilizado sin permitir solapamientos.

## Descripción del Problema

El **Problema de Empaquetamiento y Corte 3D (3D-CPP)** es un problema de optimización en estudio, el cual tiene como objetivo la ubicación de elementos rectangulares tridimensionales dentro de un contenedor sin que se solapen. En este caso, se permite la **rotación** de los elementos, lo que significa que cada uno puede ser colocado en cualquiera de las rotaciones permitidas.

### Entradas del modelo
- **Dimensiones del contenedor**: Largo `L`, Ancho `W` y Alto `H`.
- **Cajas**: Lista de cajas tridimensionales, cada una definida por sus dimensiones `(p_i, q_i, r_i)`.

### Salidas de la implementación del modelo
- **Posiciones**: Coordenadas `(x_i, y_i, z_i)` de la esquina inferior izquierda frontal de cada caja.
- **Orientaciones**: Rotación elegida de cada caja.

### Objetivo de la implementación
Maximizar el volumen total utilizado dentro del contenedor sin que las cajas se solapen.

---

## Formulación Matemática

El problema se modela como un **Problema de Programación Entera Mixta (MIP)**.

### Variables de Decisión
- `a_{i, r, x, y, z} \in \{0,1\}`: 
  - 1 si la caja `i` con rotación `r` está colocada en `(x,y,z)`.
  - 0 en caso contrario.

### Restricciones
1. **Ubicación única de cada caja**:
   - Cada caja debe ser colocada una sola vez en alguna posición y con una única rotación.

2. **No solapamiento entre cajas**:
   - Cada unidad del espacio solo puede ser ocupada por una única caja.

### Función Objetivo
Maximizar el volumen total utilizado.

---

## Implementación del Código en Python

El código se estructura en varias partes:

### 1️**Definición de Parámetros**
```python
# Importamos las librerías necesarias
from pulp import LpMaximize, LpProblem, LpVariable, lpSum
import itertools
import matplotlib.pyplot as plt
from mpl_toolkits.mplot3d.art3d import Poly3DCollection

# Definimos dimensiones del contenedor
L, W, H = 5, 5, 5

# Definimos las cajas con sus dimensiones
cajas = [(3, 2, 2), (3, 2, 2), (4, 2, 1), (4, 2, 1)]
```

### 2️**Generación de Rotaciones y Variables de Decisión**
```python
# Generamos todas las rotaciones posibles para cada caja
def obtener_rotaciones(caja):
    return list(set(itertools.permutations(caja)))

# Modelo de optimización
modelo = LpProblem("Empaquetamiento_3D", LpMaximize)

# Definimos variables de decisión
a = {}
for i, caja in enumerate(cajas):
    for r, (li, wi, hi) in enumerate(obtener_rotaciones(caja)):
        for x in range(L - li + 1):
            for y in range(W - wi + 1):
                for z in range(H - hi + 1):
                    a[i, r, x, y, z] = LpVariable(f"a_{i}_{r}_{x}_{y}_{z}", cat="Binary")
```

### 3️**Función Objetivo**
```python
modelo += lpSum(li * wi * hi * a[i, r, x, y, z] for i, caja in enumerate(cajas) for r, (li, wi, hi) in enumerate(obtener_rotaciones(caja)) for x in range(L - li + 1) for y in range(W - wi + 1) for z in range(H - hi + 1))
```

### 4️**Restricciones**
```python
# Restricción de posición única
for i in range(len(cajas)):
    modelo += lpSum(a[i, r, x, y, z] for r, rotacion in enumerate(obtener_rotaciones(cajas[i])) for x in range(L - rotacion[0] + 1) for y in range(W - rotacion[1] + 1) for z in range(H - rotacion[2] + 1)) == 1
```

```python
# Restricción de no solapamiento
for x in range(L):
    for y in range(W):
        for z in range(H):
            modelo += lpSum(a[i, r, x_, y_, z_] for i in range(len(cajas)) for r, rotacion in enumerate(obtener_rotaciones(cajas[i])) for x_ in range(max(0, x - rotacion[0] + 1), min(x + 1, L - rotacion[0] + 1)) for y_ in range(max(0, y - rotacion[1] + 1), min(y + 1, W - rotacion[1] + 1)) for z_ in range(max(0, z - rotacion[2] + 1), min(z + 1, H - rotacion[2] + 1))) <= 1
```

### 5️**Ejecutar el Solver y Mostrar Resultados**
```python
modelo.solve()
print("Estado de la solución:", LpStatus[modelo.status])
```

---
### 6️**Mostrar Resultados y Graficar Solución**
```python
# Mostramos los resultados
# Imprimimos función objetivo, tiempo de solución, las coordenadas y si hay solapamientos
print("Estado de la solución:", LpStatus[modelo.status])
print("Cajas colocadas:")
solucion = []
espacios_ocupados = set()
solapamiento_detectado = False

for i, caja in enumerate(cajas):
    for r, rotacion in enumerate(obtener_rotaciones(caja)):
        for x in range(L - rotacion[0] + 1):
            for y in range(W - rotacion[1] + 1):
                for z in range(H - rotacion[2] + 1):
                    if a[i, r, x, y, z].varValue == 1:
                        print(f"Caja {i} colocada en ({x}, {y}, {z}) con dimensiones {rotacion}")
                        solucion.append((x, y, z, rotacion))
                        
                        # Verificar solapamiento
                        for dx in range(rotacion[0]):
                            for dy in range(rotacion[1]):
                                for dz in range(rotacion[2]):
                                    if (x + dx, y + dy, z + dz) in espacios_ocupados:
                                        print(f"¡Solapamiento detectado en ({x + dx}, {y + dy}, {z + dz})!")
                                        solapamiento_detectado = True
                                    espacios_ocupados.add((x + dx, y + dy, z + dz))

if not solapamiento_detectado:
    print("No se detectaron solapamientos.")

# Graficamos la solución
def graficar_solucion():
    fig = plt.figure()
    ax = fig.add_subplot(111, projection='3d')
    ax.set_xlim([0, L])
    ax.set_ylim([0, W])
    ax.set_zlim([0, H])
    ax.set_xlabel('X')
    ax.set_ylabel('Y')
    ax.set_zlabel('Z')
    
    colores = plt.cm.get_cmap("tab10", len(cajas))
    
    for idx, (x, y, z, rotacion) in enumerate(solucion):
        verts = [
            [x, y, z], [x + rotacion[0], y, z], [x + rotacion[0], y + rotacion[1], z], [x, y + rotacion[1], z],
            [x, y, z + rotacion[2]], [x + rotacion[0], y, z + rotacion[2]], [x + rotacion[0], y + rotacion[1], z + rotacion[2]], [x, y + rotacion[1], z + rotacion[2]]
        ]
        caras = [[verts[j] for j in [0, 1, 2, 3]], [verts[j] for j in [4, 5, 6, 7]],
                 [verts[j] for j in [0, 1, 5, 4]], [verts[j] for j in [2, 3, 7, 6]],
                 [verts[j] for j in [1, 2, 6, 5]], [verts[j] for j in [0, 3, 7, 4]]]
        poly = Poly3DCollection(caras, alpha=0.5, edgecolor='k')
        poly.set_facecolor(colores(idx))
        ax.add_collection3d(poly)
    
    plt.show()

graficar_solucion()
```

---


## Autoría y Referencias
- Basado en modelos de optimización de empaquetamiento 3D.
- Adapatación propia utilizando **PuLP** de la implementación propuesta por Junqueira et al. (2012).
## Conclusión
- Se ubican todas las cajas sin solapamientos.
## Referencias
- Fernandes, E., Machado, T., & Wauters, T. (2019). *Exact methods for three-dimensional cutting and packing: A comparative study concerning single container problems.*
- Junqueira, L., Morabito, R., & Yamashita, D. S. (2012). *Three-dimensional container loading models with cargo stability and load bearing constraints.*
- ChatGPT (2024). Asistente de IA para formulación matemática.
