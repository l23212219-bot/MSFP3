[![Open in MATLAB Online](https://www.mathworks.com/images/responsive/global/open-in-matlab-online.svg)](https://matlab.mathworks.com/open/github/v1?repo=l23212219-bot/MSFP3)

# Práctica 3: Modelo del sistema musculoesquelético

## Información del estudiante

**Nombre:** Sergio Andree Navarro Rodriguez  
**Número de control:** 23212219  
**Correo institucional:** l23212219@tijuana.tecnm.mx  
**Asignatura:** Modelado de Sistemas Fisiológicos  
**Carrera:** Ingeniería Biomédica  
**Institución:** Tecnológico Nacional de México / Instituto Tecnológico de Tijuana

## Docente

**Dr. Paul Antonio Valle Trujillo**  
**Correo:** paul.valle@tectijuana.edu.mx  
**Departamento:** Ingeniería Eléctrica y Electrónica  
**Dirección:** Blvd. Alberto Limón Padilla s/n, Tijuana, C. P. 22454, B. C., México.

## Descripción de la asignatura

El modelado de sistemas fisiológicos permite estudiar el comportamiento de diferentes sistemas del cuerpo humano mediante ecuaciones matemáticas y simulaciones. En la asignatura se utilizan herramientas de programación, funciones de transferencia y sistemas de control para analizar cómo responde un modelo ante distintas entradas.

En esta práctica se estudia un **modelo simplificado del sistema musculoesquelético**, comparando un sistema de control con un caso de estudio. Después se incorpora un controlador proporcional-integral (PI) para modificar la respuesta del caso y compararla con la referencia.

## Objetivos

1. Representar el sistema musculoesquelético mediante funciones de transferencia.
2. Identificar las diferencias entre el sistema de control y el caso de estudio.
3. Analizar las respuestas de ambos modelos ante la misma señal de entrada.
4. Implementar un controlador PI para el caso de estudio.
5. Comparar la respuesta tratada con la respuesta del sistema de control.
6. Realizar las simulaciones en MATLAB, Simulink y Python.
7. Documentar el modelo, el análisis y los resultados obtenidos.

## Descripción del sistema

Se utilizan **dos funciones de transferencia**: una para el sistema de control, que se toma como referencia, y otra para el caso de estudio. Ambas reciben la misma entrada, por lo que sus respuestas pueden compararse directamente.

La siguiente imagen muestra la representación del sistema utilizada en la práctica:

![Modelo del sistema musculoesquelético](Musculoesqueletico.png)

Las señales del modelo son:

| Señal | Descripción |
|---|---|
| $F(t)$ | Señal de entrada aplicada a los dos modelos |
| $F_{sx}(t)$ | Respuesta del sistema de control |
| $F_{sY}(t)$ | Respuesta del caso de estudio |
| $y_{PI}(t)$ | Respuesta del caso con el controlador PI |

En el código de Python, la respuesta del sistema de control se guarda como `Fsx`, la del caso como `FsY` y la del tratamiento como `PID`.

## Modelo matemático

### Sistema de control

La función de transferencia del sistema de control es:

$$
G_{control}(s)=\frac{0.001s+0.75}{0.011s+1}
$$

### Caso de estudio

La función de transferencia del caso es:

$$
G_{caso}(s)=\frac{0.1s+0.75}{1.1s+1}
$$

Las dos funciones tienen una estructura de primer orden con un cero. Aunque presentan la misma ganancia en estado estacionario, sus coeficientes son diferentes y, por lo tanto, **su respuesta temporal no es igual**.

### Comparación de los modelos

| Característica | Sistema de control | Caso de estudio |
|---|---:|---:|
| Numerador | $0.001s+0.75$ | $0.1s+0.75$ |
| Denominador | $0.011s+1$ | $1.1s+1$ |
| Ganancia estática $G(0)$ | 0.75 | 0.75 |
| Polo | $-1/0.011$ | $-1/1.1$ |

La respuesta del sistema de control se utiliza como referencia para estudiar el efecto del tratamiento sobre el caso.

El desarrollo correspondiente a la práctica se encuentra en [`Analisis-matematico.pdf`](Analisis-matematico.pdf).

## Parámetros utilizados

Los coeficientes de las funciones de transferencia están definidos en el código de Python y en el modelo de simulación.

| Modelo | Numerador | Denominador |
|---|---|---|
| Control | `[0.001, 0.75]` | `[0.011, 1]` |
| Caso | `[0.1, 0.75]` | `[1.1, 1]` |

También se incluye una captura de los parámetros utilizados:

![Parámetros de la simulación](parametros.jpeg)

## Controlador PI

Para el tratamiento se utiliza un **controlador proporcional-integral (PI)**. Su función de transferencia es:

$$
C(s)=K_P+\frac{K_I}{s}
$$

### Ganancias del controlador

| Ganancia | Valor |
|:---:|---:|
| $K_P$ | 0.019136 |
| $K_I$ | 47422.9366 |

Al sustituir los valores:

$$
C(s)=0.019136+\frac{47422.9366}{s}
$$

En Python se construye el controlador con `ctrl.tf([Kp,Ki],[1,0])` y se conecta al modelo del caso mediante retroalimentación negativa unitaria. La función de transferencia del sistema tratado es:

$$
T(s)=\frac{C(s)G_{caso}(s)}{1+C(s)G_{caso}(s)}
$$

**Importante:** en el programa, la entrada del sistema tratado es **la respuesta del sistema de control** (`Fsx`), no directamente el pulso original `F`. Así se busca que la salida tratada siga la referencia del control.

Aunque algunas variables y etiquetas del programa utilizan el nombre `PID`, el controlador implementado tiene únicamente las acciones proporcional e integral. Por eso, **el controlador utilizado es PI**.

## Simulación

Las simulaciones permiten visualizar la señal de entrada, las respuestas originales de los dos modelos y la respuesta del caso después del tratamiento.

### Condiciones de la simulación en Python

| Parámetro | Valor |
|---|---:|
| Tiempo inicial | 0 s |
| Tiempo final | 10 s |
| Paso de tiempo | 0.001 s |
| Condición inicial | 0 |
| Amplitud de la entrada | 1 |

La señal aplicada es un pulso de amplitud 1 que comienza en el segundo 1 y termina en el segundo 2:

$$
F(t)=\begin{cases}
1, & 1\le t<2\\
0, & \text{en otro caso}
\end{cases}
$$

Se comparan las siguientes respuestas:

1. **Entrada:** pulso $F(t)$.
2. **Control:** respuesta del modelo de referencia.
3. **Caso:** respuesta del modelo que se desea tratar.
4. **Tratamiento:** respuesta del caso utilizando el controlador PI.

### Simulación en MATLAB y Simulink

El modelo se encuentra en [`unidad4.slx`](unidad4.slx) y el desarrollo en MATLAB en [`Navarro23212219.mlx`](Navarro23212219.mlx). La exportación del cuaderno se incluye como [`Navarro23212219.pdf`](Navarro23212219.pdf).

El resultado gráfico puede consultarse en [`musculoesqueletico_matlab.pdf`](musculoesqueletico_matlab.pdf).

### Simulación en Python

El archivo [`Navarro23212219.py`](Navarro23212219.py) permite reproducir las respuestas de los modelos. El programa:

1. Define un vector de tiempo de 0 a 10 segundos.
2. Genera el pulso de entrada.
3. Construye las funciones de transferencia del control y del caso.
4. Define el controlador PI con las ganancias indicadas.
5. Cierra el lazo de control del sistema tratado.
6. Calcula las respuestas utilizando `forced_response`.
7. Grafica las cuatro señales y guarda el resultado en PDF.

El resultado se encuentra en [`Musculoesqueletico_python.pdf`](Musculoesqueletico_python.pdf).

## Resultados y comparación

Los resultados de MATLAB/Simulink y Python se presentan en los siguientes archivos:

| Resultado | Archivo |
|---|---|
| Análisis matemático | [`Analisis-matematico.pdf`](Analisis-matematico.pdf) |
| Simulación en MATLAB | [`musculoesqueletico_matlab.pdf`](musculoesqueletico_matlab.pdf) |
| Simulación en Python | [`Musculoesqueletico_python.pdf`](Musculoesqueletico_python.pdf) |
| Cuaderno de MATLAB exportado | [`Navarro23212219.pdf`](Navarro23212219.pdf) |

El análisis consiste en observar las diferencias de respuesta entre el control y el caso, y después revisar cómo cambia la respuesta cuando se aplica el controlador PI.

Las funciones de transferencia muestran que, aunque ambos modelos tienen la misma ganancia estática, **no reaccionan de la misma manera durante el transitorio**. El controlador permite estudiar cómo se puede modificar esa dinámica para intentar seguir la respuesta de referencia.

MATLAB/Simulink y Python ofrecen dos maneras de comprobar el comportamiento del modelo y comparar las gráficas obtenidas bajo las condiciones utilizadas.

## Lista de archivos incluidos en el repositorio

1. **Cuaderno computacional de MATLAB:**
   - [`Navarro23212219.mlx`](Navarro23212219.mlx)
   - [`Navarro23212219.pdf`](Navarro23212219.pdf)

2. **Modelo de Simulink:**
   - [`unidad4.slx`](unidad4.slx)

3. **Código de Python:**
   - [`Navarro23212219.py`](Navarro23212219.py)

4. **Análisis matemático:**
   - [`Analisis-matematico.pdf`](Analisis-matematico.pdf)

5. **Representación del sistema musculoesquelético:**
   - [`Musculoesqueletico.png`](Musculoesqueletico.png)

6. **Imagen de parámetros:**
   - [`parametros.jpeg`](parametros.jpeg)

7. **Resultados de las simulaciones:**
   - [`musculoesqueletico_matlab.pdf`](musculoesqueletico_matlab.pdf)
   - [`Musculoesqueletico_python.pdf`](Musculoesqueletico_python.pdf)

8. **Documentación:**
   - [`README.md`](README.md)
   - [`LICENSE`](LICENSE)

## Requisitos y ejecución

**MATLAB:** se requiere MATLAB con Simulink y las herramientas de control utilizadas por el modelo. El botón **Open in MATLAB Online**, ubicado al inicio del README, permite abrir el repositorio desde MATLAB Online.

**Python:** el código utiliza `numpy`, `control` y `matplotlib`. Se pueden instalar con:

```bash
pip install numpy control matplotlib
```

Para ejecutar la simulación, descargar el repositorio, abrir una terminal en su carpeta y utilizar:

```bash
python Navarro23212219.py
```

Para reproducirla en MATLAB, abrir el Live Script `Navarro23212219.mlx` y el modelo `unidad4.slx`, revisar los parámetros y ejecutar la simulación.

## Conclusiones

Esta práctica permitió comparar dos modelos del sistema musculoesquelético mediante funciones de transferencia. Se observó que cambiar los coeficientes del modelo modifica su respuesta en el tiempo, incluso cuando la ganancia estática es la misma.

También se trabajó con un controlador PI para estudiar el tratamiento del caso de estudio tomando como referencia el sistema de control. El uso de MATLAB, Simulink y Python permitió representar las señales, visualizar los resultados y comparar distintas implementaciones del mismo modelo.

El ejercicio ayuda a comprender cómo se pueden aplicar las herramientas de modelado y control a un sistema fisiológico simplificado. Los modelos son parte de una simulación académica y no representan por sí mismos un tratamiento clínico real.

## Palabras clave

Sistema musculoesquelético; Modelado de sistemas fisiológicos; Función de transferencia; Sistema de control; Caso de estudio; Controlador PI; MATLAB; Simulink; Python; Simulación.

## Referencias

[1] P. A. Valle, *Syllabus para Modelado de Sistemas Fisiológicos*, Tecnológico Nacional de México / Instituto Tecnológico de Tijuana, Tijuana, B. C., México. [Repositorio de la asignatura](https://github.com/DrPaulValle/MSF-IBF1020).

[2] S. A. Navarro Rodriguez, *Práctica 3: Modelo del sistema musculoesquelético*, repositorio académico MSFP3. [GitHub](https://github.com/l23212219-bot/MSFP3).

## Licencia

El repositorio incluye el archivo [`LICENSE`](LICENSE), correspondiente a la licencia BSD 2-Clause.
