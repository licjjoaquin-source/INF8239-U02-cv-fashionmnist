\# U02.LAB07 · Vision computacional: CNN reproducible con Fashion-MNIST



\## Cierre interpretativo



\*\*Resultado principal:\*\* Se compararon dos arquitecturas de red neuronal (una red densa 

baseline y una CNN) sobre Fashion-MNIST, entrenadas con la misma particion (54,000 train, 

6,000 validacion, 10,000 prueba) durante 8 epocas con EarlyStopping.



\*\*Evidencia predictiva:\*\* El baseline denso obtuvo un F1-macro de 0.864, superando a la 

CNN (F1-macro 0.789, accuracy 0.793). Sin embargo, la curva de perdida de validacion de 

la CNN seguia disminuyendo consistentemente en la ultima epoca (val\_loss: 0.5867, sin 

estancamiento), lo que sugiere que el modelo convolucional aun no habia convergido y 

probablemente mejoraria con un mayor numero de epocas.



\*\*Clase mas dificil:\*\* La clase 6 (Shirt) obtuvo el F1-score mas bajo de la CNN (0.428), 

con solo 37.4% de recall. La matriz de confusion mostro que la mayoria de sus errores se 

concentraron en otras prendas superiores de silueta similar: T-shirt/top (27.4%), Coat 

(13.0%) y Pullover (12.4%). El analisis visual de 16 errores confirmo que la mayoria 

corresponden a confusiones razonables entre prendas visualmente parecidas en imagenes de 

baja resolucion (28x28 px, escala de grises), aunque algunos casos (ej. Ankle boot 

clasificado como Sandal) resultaron menos explicables.



\*\*Costo comparado:\*\* La CNN tiene menos parametros que la red densa (19,466 vs 50,890), 

pero tardo casi 10 veces mas en entrenar (61.93 s vs 6.38 s) y es 2.8 veces mas lenta en 

inferencia (0.086 ms/imagen vs 0.031 ms/imagen), todo medido en CPU (Intel Core i7-1255U, 

sin GPU dedicada).



\*\*¿La mejora justifica el costo?:\*\* No, en esta corrida especifica. El modelo mas costoso 

(CNN) obtuvo un desempeno inferior al modelo mas barato (denso), lo cual es un resultado 

util para el analisis de Green AI: no se debe asumir que una arquitectura mas compleja 

o costosa produce automaticamente mejores resultados; en este caso, el presupuesto de 

entrenamiento (8 epocas) fue insuficiente para que la CNN alcanzara su potencial, mientras 

que el modelo mas simple ya habia convergido satisfactoriamente en ese mismo presupuesto.



\*\*Limitacion del benchmark:\*\* Fashion-MNIST es un dataset didactico de baja resolucion, 

con imagenes centradas, sin fondo, sin variacion de iluminacion ni oclusion. Un buen 

resultado aqui no demuestra que el modelo (ni la arquitectura CNN en general) este 

preparado para clasificar fotografias reales de productos, imagenes medicas, o cualquier 

dominio con mayor complejidad visual.



\*\*Decision antes de usar otro dominio:\*\* Antes de aplicar cualquiera de estos modelos a 

un dominio distinto, se recomendaria: (1) repetir el entrenamiento de la CNN con mas 

epocas para confirmar si supera al baseline una vez que converge completamente, (2) 

validar con imagenes reales del nuevo dominio antes de cualquier despliegue, y (3) revisar 

especificamente el desempeno en clases visualmente ambiguas similares a "Shirt", ya que 

este tipo de confusion entre categorias de silueta parecida probablemente se repita en 

otros dominios de clasificacion de imagenes.

