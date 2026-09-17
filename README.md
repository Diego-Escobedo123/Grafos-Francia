# Grafos-Francia

## Analisis de redes de pases de Francia

Este proyecto analiza los pases realizados por la seleccion de Francia durante la fase de grupos, usando datos almacenados en el archivo `pases_francia.csv`.

El objetivo principal es construir grafos dirigidos para visualizar como se conectaron los jugadores mediante pases completados en cada partido de la fase de grupos.

## Partidos analizados

El codigo separa los datos de Francia en tres partidos de fase de grupos:

- Francia vs Tunez
- Francia vs Dinamarca
- Francia vs Australia

Cada partido se analiza por separado para poder comparar la estructura de pases, la cantidad de jugadores involucrados y las conexiones entre ellos.

## Requisitos

Para ejecutar el proyecto se necesitan las siguientes librerias de Python:

```python
import pandas as pd
import networkx as nx
import matplotlib.pyplot as plt
```

Si `networkx` no esta instalado, se puede instalar con:

```python
%pip install networkx
```

Tambien es necesario tener el archivo:

```text
pases_francia.csv
```

Este archivo debe estar en la misma carpeta donde se ejecuta el notebook o script.

## Estructura esperada del CSV

El codigo usa las siguientes columnas del archivo:

| Columna | Uso |
| --- | --- |
| `fase` | Filtrar solamente los partidos de fase de grupos |
| `oponente` | Separar los partidos contra Tunez, Dinamarca y Australia |
| `resultado` | Filtrar solamente pases completados |
| `jugador_nombre` | Identificar al jugador que realiza el pase |
| `receptor_nombre` | Identificar al jugador que recibe el pase |
| `inicio_x` | Coordenada X desde donde sale el pase |
| `inicio_y` | Coordenada Y desde donde sale el pase |
| `fin_x` | Coordenada X donde termina el pase |
| `fin_y` | Coordenada Y donde termina el pase |

## Flujo del analisis

### 1. Carga de datos

Primero se carga el archivo CSV usando `pandas`:

```python
df = pd.read_csv("pases_francia.csv")
```

Luego se filtran solamente los registros correspondientes a fase de grupos:

```python
df_grupos = df[df["fase"] == "Group Stage"]
```

Despues, se separan los datos por oponente:

```python
df_tunisia = df_grupos[df_grupos["oponente"] == "Tunisia"]
df_denmark = df_grupos[df_grupos["oponente"] == "Denmark"]
df_australia = df_grupos[df_grupos["oponente"] == "Australia"]
```

Esto permite trabajar cada partido como una red independiente.

### 2. Filtrado de pases completados

Para construir las redes, se usan solamente los pases cuyo resultado fue `Complete`.

```python
df_completos = df_tunisia[df_tunisia["resultado"] == "Complete"]
```

El mismo proceso se repite para Dinamarca y Australia.

Esto es importante porque la red busca representar conexiones reales entre jugadores, no intentos fallidos de pase.

### 3. Construccion de grafos dirigidos

Se crea un grafo dirigido para cada partido usando `networkx.DiGraph()`.

```python
G1 = nx.DiGraph()
G2 = nx.DiGraph()
G3 = nx.DiGraph()
```

Cada nodo representa un jugador y cada arista representa un pase completado desde un jugador hacia otro.

Ejemplo:

```text
Antoine Griezmann -> Kylian Mbappe
```

Esto significa que Griezmann realizo un pase completado hacia Mbappe.

### 4. Verificacion de los grafos

El codigo imprime informacion basica para revisar que los grafos se hayan construido correctamente:

- Numero de jugadores o nodos
- Numero de conexiones o aristas
- Ejemplos de pases entre jugadores

Esta parte sirve como control rapido antes de pasar a la visualizacion.

## Posiciones promedio de los jugadores

La funcion `obtener_posiciones()` calcula la posicion promedio de cada jugador en la cancha.

Para hacerlo, toma en cuenta dos tipos de coordenadas:

- La posicion desde donde el jugador hizo un pase
- La posicion donde el jugador recibio un pase

Luego une ambas fuentes de informacion y calcula el promedio de posicion para cada jugador.

Esto permite ubicar a cada nodo en una zona aproximada de la cancha, en lugar de colocarlo de forma aleatoria.

## Visualizacion de la cancha

La funcion `dibujar_cancha()` dibuja una cancha de futbol simple usando `matplotlib`.

Incluye:

- Bordes de la cancha
- Linea de medio campo
- Circulo central
- Areas grandes

Esta cancha funciona como fondo para colocar encima la red de pases.

## Visualizacion de las redes

Finalmente, el codigo crea una figura con tres graficas, una por partido:

```python
fig, axes = plt.subplots(1, 3, figsize=(24, 10))
```

En cada grafica:

- Los nodos representan jugadores
- Las flechas representan pases completados
- La direccion de la flecha indica quien paso el balon y quien lo recibio
- El tamano del nodo aumenta segun la cantidad de pases recibidos
- La posicion del nodo representa la ubicacion promedio del jugador en la cancha

## Interpretacion

La grafica permite observar visualmente como se organizo Francia en cada partido.

Algunas preguntas que se pueden responder con esta visualizacion son:

- Que jugadores participaron mas en la circulacion del balon
- Que jugadores recibieron mas pases
- Que zonas de la cancha concentraron mayor actividad
- Si el equipo jugo mas por una banda, por el centro o de forma equilibrada
- Como cambio la red de pases entre un partido y otro

## Resultado esperado

Al ejecutar el codigo, se espera obtener:

1. Un resumen numerico de filas, jugadores y conexiones.
2. Ejemplos de conexiones entre jugadores.
3. Una visualizacion con tres canchas, cada una mostrando la red de pases de Francia contra Tunez, Dinamarca y Australia.

## Posibles mejoras

El analisis se puede mejorar agregando:

- Peso en las aristas para representar cuantas veces se repite un pase entre dos jugadores.
- Colores distintos segun posicion del jugador.
- Filtros por minuto o periodo del partido.
- Comparacion entre pases intentados y pases completados.
- Medidas de centralidad para identificar a los jugadores mas importantes en la red.

## Conclusion

Este proyecto usa `pandas`, `networkx` y `matplotlib` para transformar datos de pases en redes visuales de juego.

La visualizacion ayuda a entender la estructura colectiva de Francia durante la fase de grupos, mostrando no solo cuantos pases se hicieron, sino tambien entre quienes circularon y en que zonas del campo ocurrieron.