\# U02.LAB07 · Visión computacional: CNN reproducible con Fashion-MNIST



\## Cierre interpretativo



\*\*Resultado principal:\*\* Se compararon dos arquitecturas de red neuronal (una red densa 

baseline y una CNN) sobre Fashion-MNIST, entrenadas con la misma partición (54,000 train, 

6,000 validación, 10,000 prueba) durante 8 épocas con EarlyStopping.



\*\*Evidencia predictiva:\*\* El baseline denso obtuvo un F1-macro de 0.864, superando a la 

CNN (F1-macro 0.789, accuracy 0.793). Sin embargo, la curva de perdida de validación de 

la CNN seguía disminuyendo consistentemente en la última época (val\_loss: 0.5867, sin 

estancamiento), lo que sugiere que el modelo convolucional aún no había convergido y 

probablemente mejoraría con un mayor número de épocas.



\*\*Clase más difícil:\*\* La clase 6 (Shirt) obtuvo el F1-score más bajo de la CNN (0.428), 

con solo 37.4% de recall. La matriz de confusión mostró que la mayoría de sus errores sé 

concentraron en otras prendas superiores de silueta similar: T-shirt/top (27.4%), Coat 

(13.0%) y Pullover (12.4%). El análisis visual de 16 errores confirmo que la mayoría 

corresponden a confusiones razonables entre prendas visualmente parecidas en imágenes de 

baja resolución (28x28 px, escala de grises), aunque algunos casos (ej. Ankle boot 

clasificado como Sandal) resultaron menos explicables.



\*\*Costo comparado:\*\* La CNN tiene menos parámetros que la red densa (19,466 vs 50,890), 

pero tardo casi 10 veces más en entrenar (61.93 s vs 6.38 s) y es 2.8 veces más lenta en 

inferencia (0.086 ms/imagen vs 0.031 ms/imagen), todo medido en CPU (Intel Core i7-1255U, 

sin GPU dedicada).



\*\*¿La mejora justifica el costo?:\*\* No, en esta corrida específica. El modelo más costoso 

(CNN) obtuvo un desempeñó inferior al modelo más barato (denso), lo cual es un resultado 

Útil para el análisis de Green AI: no se debe asumir que una arquitectura más compleja 

o costosa produce automáticamente mejores resultados; en este caso, el presupuesto de 

entrenamiento (8 épocas) fue insuficiente para que la CNN alcanzara su potencial, mientras 

que el modelo más simple ya había convergido satisfactoriamente en ese mismo presupuesto.



\*\*Limitación del benchmark:\*\* Fashion-MNIST es un dataset didáctico de baja resolución, 

con imágenes centradas, sin fondo, sin variación de iluminación ni oclusión. Un buen 

resultado aquí no demuestra que el modelo (ni la arquitectura CNN en general) este 

preparado para clasificar fotografías reales de productos, imágenes médicas, o cualquier 

dominio con mayor complejidad visual.



\*\*Decisión antes de usar otro dominio:\*\* Antes de aplicar cualquiera de estos modelos a 

un dominio distinto, se recomendaría: (1) repetir el entrenamiento de la CNN con más 

Épocas para confirmar si supera al baseline una vez que converge completamente, (2) 

validar con imágenes reales del nuevo dominio antes de cualquier despliegue, y (3) revisar 

Específicamente el desempeñó en clases visualmente ambiguas similares a "Shirt", ya que 

este tipo de confusión entre categorías de silueta parecida probablemente se repita en 

otros dominios de clasificación de imágenes.

