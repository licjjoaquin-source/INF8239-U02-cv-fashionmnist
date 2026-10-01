# Model Card - Fashion-MNIST CNN

## Modelo y versión

Comparación de dos modelos entrenados sobre Fashion-MNIST: una red densa (baseline) y 
una red convolucional (CNN), versión 1.0, entrenados el 28/09/2026. Modelo CNN guardado 
en models/best_cnn.keras (mejor checkpoint según ModelCheckpoint con save_best_only=True).

## Uso previsto

Demostración académica de clasificación de imágenes de prendas de vestir en 10 categorías, 
con fines educativos para comparar arquitecturas de red neuronal (densa vs convolucional) 
en términos de desempeñó y costo computacional (enfoque Green AI).

## Usos fuera de alcance

Este modelo NO debe usarse para: clasificación de imágenes médicas, reconocimiento facial, 
vigilancia, control de calidad industrial real, ni ningún dominio distinto a imágenes de 
prendas en escala de grises de baja resolución (28x28 px) similares a Fashion-MNIST. Un 
buen resultado en este benchmark didáctico no demuestra preparación para otros dominios 
de visión por computadora.

## Dataset y particiones

- Fashion-MNIST (Zalando Research), 70,000 imágenes en escala de grises, 28x28 px, 10 clases
- Partición: 54,000 entrenamiento, 6,000 validación (últimas 6000 del set de train 
  original), 10,000 prueba (partición oficial del dataset, sin modificar)
- Clases: 0=T-shirt/top, 1=Trouser, 2=Pullover, 3=Dress, 4=Coat, 5=Sandal, 6=Shirt, 
  7=Sneaker, 8=Bag, 9=Ankle boot
- Semilla fija (42) para reproducibilidad; TF_DETERMINISTIC_OPS=1

## Preprocesamiento

Normalización de valores de pixel (función normalize_images en 
src/inf8239_u02_cv/data.py). Validación de forma y rango de imágenes antes de entrenar 
(validate_images).

## Métricas globales y por clase

| Modelo | F1-macro | Accuracy |
|--------|----------|----------|
| dense (baseline) | 0.864 | (ver reports/cv_metrics.json) |
| cnn | 0.789 | 0.793 |

Reporte de clasificación de la CNN (conjunto de prueba, n=10,000):

| Clase | Precisión | Recall | F1-score |
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

Clase más difícil: Shirt (clase 6), con el F1-score más bajo (0.428). La matriz de 
confusión muestra que solo 37.4% de las camisas reales se clasificaron correctamente; 
el resto se confundió principalmente con T-shirt/top (27.4%), Coat (13.0%) y Pullover 
(12.4%), prendas que comparten silueta similar en imágenes de baja resolución.

## Comparación de costo
- Hardware: Dell Inspiron 14 7420 2-in-1, Intel Core i7-1255U (12th Gen), 16GB RAM, 
  GPU integrada Intel Iris Xe (sin GPU dedicada NVIDIA); TensorFlow 2.21.0 ejecutado 
  exclusivamente en CPU (TensorFlow >=2.11 no soporta GPU nativa en Windows sin WSL2)
- Parámetros: dense = 50,890 | cnn = 19,466
- Tiempo de entrenamiento (8 épocas): dense = 6.38 s | cnn = 61.93 s (9.7x más lento)
- Tiempo de inferencia: dense = 0.031 ms/imagen | cnn = 0.086 ms/imagen (2.8x más lento)

**Green AI - la mejora no justifica el costo en esta corrida:** en este experimento 
específico (8 épocas), el modelo más costoso (CNN) obtuvo un F1-macro MENOR (0.789) 
que el baseline denso más simple y barato (0.864), a pesar de tardar casi 10 veces más 
en entrenar y ser 2.8 veces más lento en inferencia. Sin embargo, la curva de perdida de 
validación de la CNN seguía disminuyendo consistentemente en la época 8 (val_loss: 0.5867, 
sin señales de estancamiento), lo que sugiere que el modelo aún no había convergido y 
probablemente mejoraría con más épocas de entrenamiento. Este resultado no debe 
generalizarse como "las CNN son peores que las redes densas"; es una limitación de esta 
corrida específica con presupuesto de entrenamiento limitado (8 épocas).

## Limitaciones yriesgos

- Resultado obtenido con solo 8 épocas; la CNN muestra evidencia de no haber convergido, 
  por lo que la comparación de F1-macro entre modelos no es definitiva en este experimento.
- La clase Shirt tiene un desempeñó notablemente bajo (F1 0.428) en ambos modelos, un 
  riesgo si el sistema se usara para catalogación automática de inventario real.
- Fashion-MNIST es un benchmark didáctico de baja resolución; no representa la 
  complejidad de fotografías reales de productos (fondos variados, iluminación, ángulos, 
  oclusión parcial).
- Las mediciones de tiempo son específicas de este hardware (CPU, sin GPU) y no deben 
  interpretarse como una medida universal de eficiencia energética.

## Supervisión y monitoreo

Antes de cualquier uso más allá de la demostración académica, se recomienda: (1) repetir 
el entrenamiento de la CNN con más épocas (ej. 20-30) para confirmar si supera al baseline 
denso una vez que converge completamente, (2) revisar manualmente las predicciones de la 
clase Shirt dado su bajo recall, y (3) validar el modelo con imágenes reales del dominio 
de destino antes de cualquier despliegue, dado que el desempeñó en Fashion-MNIST no 
garantiza generalización a fotografías de prendas en condiciones reales.
