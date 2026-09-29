# Model Card - Fashion-MNIST CNN

## Modelo y version

Comparacion de dos modelos entrenados sobre Fashion-MNIST: una red densa (baseline) y 
una red convolucional (CNN), version 1.0, entrenados el 28/09/2026. Modelo CNN guardado 
en models/best_cnn.keras (mejor checkpoint segun ModelCheckpoint con save_best_only=True).

## Uso previsto

Demostracion academica de clasificacion de imagenes de prendas de vestir en 10 categorias, 
con fines educativos para comparar arquitecturas de red neuronal (densa vs convolucional) 
en terminos de desempeno y costo computacional (enfoque Green AI).

## Usos fuera de alcance

Este modelo NO debe usarse para: clasificacion de imagenes medicas, reconocimiento facial, 
vigilancia, control de calidad industrial real, ni ningun dominio distinto a imagenes de 
prendas en escala de grises de baja resolucion (28x28 px) similares a Fashion-MNIST. Un 
buen resultado en este benchmark didactico no demuestra preparacion para otros dominios 
de vision por computadora.

## Dataset y particiones

- Fashion-MNIST (Zalando Research), 70,000 imagenes en escala de grises, 28x28 px, 10 clases
- Particion: 54,000 entrenamiento, 6,000 validacion (ultimas 6000 del set de train 
  original), 10,000 prueba (particion oficial del dataset, sin modificar)
- Clases: 0=T-shirt/top, 1=Trouser, 2=Pullover, 3=Dress, 4=Coat, 5=Sandal, 6=Shirt, 
  7=Sneaker, 8=Bag, 9=Ankle boot
- Semilla fija (42) para reproducibilidad; TF_DETERMINISTIC_OPS=1

## Preprocesamiento

Normalizacion de valores de pixel (funcion normalize_images en 
src/inf8239_u02_cv/data.py). Validacion de forma y rango de imagenes antes de entrenar 
(validate_images).

## Metricas globales y por clase

| Modelo | F1-macro | Accuracy |
|--------|----------|----------|
| dense (baseline) | 0.864 | (ver reports/cv_metrics.json) |
| cnn | 0.789 | 0.793 |

Reporte de clasificacion de la CNN (conjunto de prueba, n=10,000):

| Clase | Precision | Recall | F1-score |
|-------|-----------|--------|----------|
| 0 T-shirt/top | 0.645 | 0.807 | 0.717 |
| 1 Trouser | 0.988 | 0.932 | 0.959 |
| 2 Pullover | 0.705 | 0.642 | 0.672 |
| 3 Dress | 0.753 | 0.843 | 0.796 |
| 4 Coat | 0.661 | 0.663 | 0.662 |
| 5 Sandal | 0.950 | 0.885 | 0.916 |
| 6 Shirt | 0.500 | 0.374 | 0.428 |
| 7 Sneaker | 0.855 | 0.952 | 0.901 |
| 8 Bag | 0.917 | 0.937 | 0.927 |
| 9 Ankle boot | 0.935 | 0.896 | 0.915 |

Clase mas dificil: Shirt (clase 6), con el F1-score mas bajo (0.428). La matriz de 
confusion muestra que solo 37.4% de las camisas reales se clasificaron correctamente; 
el resto se confundio principalmente con T-shirt/top (27.4%), Coat (13.0%) y Pullover 
(12.4%), prendas que comparten silueta similar en imagenes de baja resolucion.

## Comparacion de costo
- Hardware: Dell Inspiron 14 7420 2-in-1, Intel Core i7-1255U (12th Gen), 16GB RAM, 
  GPU integrada Intel Iris Xe (sin GPU dedicada NVIDIA); TensorFlow 2.21.0 ejecutado 
  exclusivamente en CPU (TensorFlow >=2.11 no soporta GPU nativa en Windows sin WSL2)
- Parametros: dense = 50,890 | cnn = 19,466
- Tiempo de entrenamiento (8 epocas): dense = 6.38 s | cnn = 61.93 s (9.7x mas lento)
- Tiempo de inferencia: dense = 0.031 ms/imagen | cnn = 0.086 ms/imagen (2.8x mas lento)

**Green AI - la mejora no justifica el costo en esta corrida:** en este experimento 
especifico (8 epocas), el modelo mas costoso (CNN) obtuvo un F1-macro MENOR (0.789) 
que el baseline denso mas simple y barato (0.864), a pesar de tardar casi 10 veces mas 
en entrenar y ser 2.8 veces mas lento en inferencia. Sin embargo, la curva de perdida de 
validacion de la CNN seguia disminuyendo consistentemente en la epoca 8 (val_loss: 0.5867, 
sin senales de estancamiento), lo que sugiere que el modelo aun no habia convergido y 
probablemente mejoraria con mas epocas de entrenamiento. Este resultado no debe 
generalizarse como "las CNN son peores que las redes densas"; es una limitacion de esta 
corrida especifica con presupuesto de entrenamiento limitado (8 epocas).

## Limitaciones yriesgos

- Resultado obtenido con solo 8 epocas; la CNN muestra evidencia de no haber convergido, 
  por lo que la comparacion de F1-macro entre modelos no es definitiva en este experimento.
- La clase Shirt tiene un desempeno notablemente bajo (F1 0.428) en ambos modelos, un 
  riesgo si el sistema se usara para catalogacion automatica de inventario real.
- Fashion-MNIST es un benchmark didactico de baja resolucion; no representa la 
  complejidad de fotografias reales de productos (fondos variados, iluminacion, angulos, 
  oclusion parcial).
- Las mediciones de tiempo son especificas de este hardware (CPU, sin GPU) y no deben 
  interpretarse como una medida universal de eficiencia energetica.

## Supervision y monitoreo

Antes de cualquier uso mas alla de la demostracion academica, se recomienda: (1) repetir 
el entrenamiento de la CNN con mas epocas (ej. 20-30) para confirmar si supera al baseline 
denso una vez que converge completamente, (2) revisar manualmente las predicciones de la 
clase Shirt dado su bajo recall, y (3) validar el modelo con imagenes reales del dominio 
de destino antes de cualquier despliegue, dado que el desempeno en Fashion-MNIST no 
garantiza generalizacion a fotografias de prendas en condiciones reales.
