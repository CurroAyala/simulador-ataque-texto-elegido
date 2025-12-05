# 🔐 Simulador de Ataque de Texto Elegido

![Python](https://img.shields.io/badge/Python-blue)
![Streamlit](https://img.shields.io/badge/Streamlit-App-red)

Este proyecto es una aplicación educativa interactiva desarrollada en **Python** con **Streamlit**. Simula un sistema de criptografía áurea, vulnerable a un **Ataque de Texto Elegido (_Chosen-Plaintext Attack_)** . Su objetivo es demostrar cómo un atacante puede deducir la clave privada ($n$) enviando mensajes concretos y analizando la respuesta cifrada.


## 📋 Partes del simulador

* **Generación de claves**: el sistema genera una clave privada aleatoria $n$ (un entero entre 5 y 20).

* **Cifrar de mensajes**: permite al usuario introducir los cuatro valores de la matriz a cifrar. Una vez introducidos, muestra el resultado del cifrado.

* **Adivinar clave**: pregunta por la clave secreta, para que el usuario compruebe si es capaz de adivinarla.

* **Sucesión de Fibonacci**: es una pequeña ayuda que muestra, en forma de tabla, los primeros 20 elementos de la sucesión de Fibonacci.

## 🚀 Acceso a la aplicación

La aplicación se encuentra desplegada [aquí](https://simulador-ataque-texto-elegido.streamlit.app/).


## 🧮 Fundamento matemático: criptografía urea

Alexey Petrovich Stakhov (7 de mayo de 1939, Ucrania) desarrolló el criptosistema áureo (*golden cryptography*) basándose en la matriz $Q$:

$$
Q = \begin{pmatrix}
   1 & 1 \\
   1 & 0
\end{pmatrix}
$$

Al elevar esta matriz a la potencia $n$, sus elementos corresponden a las posiciones $F_{n-1}$, $F_n$ y $F_{n+1}$ de la sucesión de Fibonacci, de la forma:

$$
Q^n = \begin{pmatrix}
   F_{n+1} & F_n \\
   F_n & F_{n-1}
\end{pmatrix}
$$   
  
  
#### Tabla de referencia: Sucesión de Fibonacci
| Pos ($n$) | Val ($F_n$) | | Pos ($n$) | Val ($F_n$) |
| :---: | :---: | - | :---: | :---: |
| **1** | 1 | | **11** | 89 |
| **2** | 1 | | **12** | 144 |
| **3** | 2 | | **13** | 233 |
| **4** | 3 | | **14** | 377 |
| **5** | 5 | | **15** | 610 |
| **6** | 8 | | **16** | 987 |
| **7** | 13 | | **17** | 1597 |
| **8** | 21 | | **18** | 2584 |
| **9** | 34 | | **19** | 4181 |
| **10** | 55 | | **20** | 6765 |

Stakhov llamó 'matrices áureas' (*golden matrices*) a $Q^n$ porque cuando $n \to \infty$, la división de un elemento de $Q^n$ por el siguiente de la misma fila se aproxima al número irracional **Phi** ($\varphi$):

$$
\varphi \approx 1.6180...
$$

Esta proporción áurea se mantiene al multiplicar cualquier matriz por $Q^n$ y permite, en implementaciones avanzadas, corregir errores de transmisión.

**Ejemplo 1:** Con $n = 15$:

$$
Q^{15} = \begin{pmatrix}
   987 & 610 \\
   610 & 377
\end{pmatrix}
$$

Obsérvese la proporción:
$$\frac{987}{610} \approx 1.618... \quad \text{y} \quad \frac{610}{377} \approx 1.618...$$

### Proceso de cifrado

El mensaje se divide en matrices de $2 \times 2$ (llamadas $P$) y se cifra por bloques usando la fórmula:

$$
C = P \times Q^n
$$

*Donde $n$ es la clave privada elegida arbitrariamente.*

Existen dos formas de validar el cifrado:
1.  Comprobando la proporción áurea del mensaje cifrado.
2.  Comprobando la igualdad de determinantes (donde $det(P)$ es el número de control público):
    $$det(C) = (-1)^n \cdot det(P)$$

### Proceso de descifrado

Para recuperar el mensaje original se utiliza la matriz inversa:

$$
P = C \times Q^{-n}
$$

Donde la matriz de descifrado se define como:

$$
Q^{-n} = (-1)^{-n} \begin{pmatrix}
F_{n-1} & -F_{n} \\
-F_{n} & F_{n+1}
\end{pmatrix}
$$

---

**Ejemplo 2:** Cifrar el mensaje $P =$ "HOLA" con $n=15$.

Codificación numérica (alfabeto español):

$$
P = \text{HOLA} \rightarrow \begin{pmatrix} H & O \\
L & A \end{pmatrix} \rightarrow \begin{pmatrix} 7 & 15 \\
11 & 0 \end{pmatrix}
$$

Cálculo de $C = P \times Q^{15}$:

$$
C = \begin{pmatrix} 7 & 15 \\
11 & 0 \end{pmatrix} \times \begin{pmatrix} 987 & 610 \\
610 & 377 \end{pmatrix} = \begin{pmatrix} 16059 & 9925 \\
10857 & 6710 \end{pmatrix}
$$

**Ejemplo 3:** Descifrar el mensaje anterior.

$$
P = \begin{pmatrix} 16059 & 9925 \\
10857 & 6710 \end{pmatrix} \times (-1)^{15} \begin{pmatrix} 377 & -610 \\
-610 & 987 \end{pmatrix} = \begin{pmatrix} 7 & 15 \\
11 & 0 \end{pmatrix}
$$

Al decodificar los números, recuperamos "HOLA".

---

### Vulnerabilidades

El principal problema de la criptografía áurea es que resulta "ingenua" ante un atacante informado. Es un sistema **rígido**, ya que la seguridad depende exclusivamente del exponente $n$.

#### Ataque de Texto Elegido

Si un atacante tiene acceso al sistema de cifrado, puede obtener la clave privada $n$ fácilmente mediante un **Chosen Plaintext Attack**.

El atacante envía una matriz $P$ diseñada específicamente para aislar los componentes de la clave:

$$
P = \begin{pmatrix} 1 & 0 \\
0 & 0 \end{pmatrix}
$$

El sistema cifrará $P$ devolviendo $C$:

$$
C = P \times Q^n = \begin{pmatrix} 1 & 0 \\
0 & 0 \end{pmatrix} \times \begin{pmatrix} F_{n+1} & F_n \\
F_n & F_{n-1} \end{pmatrix} = \begin{pmatrix} F_{n+1} & F_n \\
0 & 0 \end{pmatrix}
$$

Como se observa, el mensaje cifrado expone directamente el valor $F_n$ en la posición (Fila 1, Columna 2). El atacante solo necesita buscar este valor en la sucesión de Fibonacci para encontrar el índice $n$ (la clave privada).


