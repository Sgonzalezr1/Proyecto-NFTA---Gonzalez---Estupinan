***Codigo implementado ***

**Algoritmo Greedy con busqueda local**

```
import numpy as np
import random

# Parámetros y constantes
m = 18         # Número de intervalos
F = 2          # Número de sentidos (direcciones)
n_f = [3, 3]   # Flota por sentido
s_f = [2, 2]   # Servicios por sentido
alpha = 10     # Duración de cada intervalo
t_ruta = 60    # Tiempo de ruta
t_min = 5      # Tiempo de maniobra
h_min = 10     # Intervalo mínimo entre servicios
h_max = 30     # Intervalo máximo entre servicios
C = 50         # Capacidad del bus
lambda_f = [[1, 2],[3,4] ,[5,6],[7,8],[9,10],[11,12],[13,14],[15,16],[17,18],[19,20],[21,22],[23,24],[25,26],[27,28],[29,30],[31,32],[33,34],[35,36]] # Tasa de llegada de pasajeros por intervalo, para cada sentido
# Inicializar matrices de decisión
x = np.zeros((max(n_f), max(s_f), F, m), dtype=int) # Matriz de despacho
b = np.zeros((m, F), dtype=int)                     # Pasajeros que abordan
w = np.zeros((m, F), dtype=int)                     # Pasajeros que esperan 
llegadas_pasajeros = np.zeros((m, F))               #Pasajeros que llegan 

#LLenar la matriz de pasajeros que llegan teniendo en cuenta la tasas de llegada y el tamaño del intervalo 
for t in range(m):
    for f in range(F):
        llegadas_pasajeros[t, f] = lambda_f[t][f] * alpha
#LLenar la matriz w teniendo en cuenta que ningun servicio ha sido asignado                    
for t in range(m):
    for f in range(F):
        if t == 0:
            w[t,f]=llegadas_pasajeros[t, f]  # No hay pasajeros esperando en el intervalo inicial
        else:
            # Aplicar la restricción (10): w(t,f) = w(t-1,f) + λ(t-1,f) - b(t,f)
            w[t,f]=w[t - 1, f] + llegadas_pasajeros[t - 1, f] - b[t, f]
```

```        
# Función para construir la RCL (Restricted Candidate List)
def generar_rcl(i, f, j):
    rcl = []
    for t in range(m):
        # Restricción: no se puede asignar un servicio j sin haber asignado el servicio anterior j-1
        if j > 0 and np.sum(x[i, j-1, f, :]) == 0:
            continue
        
        
        #Asignar temporalmente el servicio a un intervalo t
        x[i, j, f, t] = 1
        # El intervalo del primer servicio debe respetar los tiempos minimos y maximos de despacho  
        if j == 0 and i==0:  # Para el primer servicio
            if not (h_min <= alpha * t <= h_max):
                x[i, j, f, t] = 0
                continue
        
        # Un servicio j de un bus i no puede comenzar sin que el servicio j-1 de ese mismo bus haya completado el tiempo de la ruta y el tiempo de maniobra 
        if j > 0:  # Si no es el primer servicio
            tiempo_despacho_j_menos_1 = np.sum([alpha * (t_prev-1) * x[i, j-1, f, t_prev] for t_prev in range(m)])
            tiempo_despacho_j = np.sum([alpha * (t_prev-1) * x[i, j, f, t_prev] for t_prev in range(m)])
            if (tiempo_despacho_j - tiempo_despacho_j_menos_1) < (t_ruta + t_min):
                x[i, j, f, t] = 0
                continue  # No se puede despachar si no se cumple la restricción
        
        # Restricción de diferencia mínima y máxima entre servicios consecutivos 
        if i > 0:
            tiempo_despacho_i = np.sum([alpha * (t_prev-1) * x[i, j, f, t_prev] for t_prev in range(m)])
            tiempo_despacho_i_menos_1 = np.sum([alpha * (t_prev-1) * x[i-1, j, f, t_prev] for t_prev in range(m)])
            if (tiempo_despacho_i - tiempo_despacho_i_menos_1) > h_max or (tiempo_despacho_i - tiempo_despacho_i_menos_1) < h_min:
                x[i, j, f, t] = 0
                continue  # No se puede despachar si no se cumple la restricción 
        # Restriccion de diferencia minima y maxima entre el inicio del servicio j en el primer bus y el inicio del servicio j-1 en el ultimo bus 
        if np.sum(x[:,j-1,f,:])==n_f[f] and j > 0:
            tiempo_despacho_j_plus_1_0 = np.sum([alpha * (t_prev-1) * x[0, j, f, t_prev] for t_prev in range(m)])
            tiempo_despacho_j_n_f = np.sum([alpha * (t_prev-1) * x[n_f[f]-1, j-1, f, t_prev] for t_prev in range(m)])
            if (tiempo_despacho_j_plus_1_0 - tiempo_despacho_j_n_f) > h_max or (tiempo_despacho_j_plus_1_0 - tiempo_despacho_j_n_f) < h_min:
                x[i, j, f, t] = 0
                continue
        
        rcl.append(t)
        x[i,j,f,:]=0
    return rcl

# Función que calcula los pasajeros abordando
def calcular_abordaje(f, t):
   
    suma_despachos = np.sum(x[:, :, f, t])  # Encontrar algun servicio de algun bus en el sentido f que este asignado al intervalo t 
    return min(suma_despachos * C, w[t,f])  # Minimo entre la capacidad y la cantidad de personas esperando 


# Función para calcular pasajeros en espera
def calcular_pasajeros_esperando(t, f):
    if t == 0:
        return llegadas_pasajeros[t, f]  # No hay pasajeros esperando en el intervalo inicial
    else:
        # La cantidad de pasajeros esperando en el intervalo t es la cantidad de pasajeros esperando en el intervalo anterior + la cantidad de personas que llegaron en el intervalo anterior - la cantidad de personas que anbordaron en el intervalo t  
        c=w[t - 1, f] + llegadas_pasajeros[t - 1, f] - b[t, f]
        return c
# Algoritmo GRASP
def grasp(max_iter=50):
    mejor_solucion_global = None
    mejor_objetivo_global = float('inf')
    for iteracion in range(max_iter):
        lista_indices=[]
        # Fase de construcción
        for f in range(F):
            for j in range(s_f[f]):
                for i in range(n_f[f]):
                    rcl = generar_rcl(i, f, j)
                    if len(rcl) == 0:
                        continue
                    # Escoger un intervalo t aleatorio 
                    t=random.choice(rcl)
                    #Asignar el servicio al intervalo t
                    x[i, j, f, t] = 1
                    # Calcular la cantidad de pasajeros abordando en el intervalo t
                    b[t, f] = calcular_abordaje(f, t)
                    # Dada la asignacion calcular la cantidad de pasajeros esperando en todo el horizonte de planeacion 
                    for t_ in range(m):
                        w[t_,f]=calcular_pasajeros_esperando(t_, f)
                    mejor_objetivo = np.sum(w)
                    mejor_solucion_t = -1 # Minimizar pasajeros esperando
                    # Interar en toda la lista de posibles intervalos 
                    for t_alternativo in rcl:
                        b[t,f]=0
                        # Asignar temporalmente el servicio al intervalo t_alternativo
                        b[t_alternativo,f]=calcular_abordaje(f, t_alternativo)
                        # Dada la asignacion temporal calcular la cantidad de pasajeros esperando en todo el horizonte de planeacion 
                        for t1 in range(m):
                            w[t1,f]=calcular_pasajeros_esperando(t1, f)
                        # Calcular la cantidad total de pasajeros esperando 
                        nuevo_objetivo = np.sum(w)
                        # Compararacion entre alternativas que minimizen la cantidad total de pasajeros esperando 
                        if nuevo_objetivo < mejor_objetivo:
                            mejor_objetivo = nuevo_objetivo
                            # Guardar el mejor t encontrado
                            mejor_solucion_t = t_alternativo
                            #Revertir el cambio 
                            b[t_alternativo,f]=0
                            for t1 in range(m):
                                w[t1,f]=calcular_pasajeros_esperando(t1, f)
                        else:
                            # Revertir el cambio
                            x[i, j, f, t_alternativo] = 0
                            b[t_alternativo,f]=calcular_abordaje(f,t_alternativo)
                            for t1 in range(m):
                                w[t1,f]=calcular_pasajeros_esperando(t1, f)
                    # En el caso de que se haya encontrado una mejor solucion que la solucion dada por el algoritmo de construccion asignar el servicio al mejor intervalo encontrado 
                    if mejor_solucion_t >-1:
                        x[i,j,f,t]=0
                        b[t,f]=0
                        x[i,j,f,mejor_solucion_t]=1
                        b[mejor_solucion_t,f]=calcular_abordaje(f,mejor_solucion_t)
                        for t1 in range(m):
                            w[t1,f]=calcular_pasajeros_esperando(t1, f)
                        lista_indices.append((i,j,f,mejor_solucion_t))
                    # En el caso de que no se haya encontrado un mejor intervalo quedarse con la solucion inicial  
                    else:
                        x[i,j,f,t]=1
                        b[t,f]=calcular_abordaje(f,t)
                        for t1 in range(m):
                            w[t1,f]=calcular_pasajeros_esperando(t1, f)
                        lista_indices.append((i,j,f,t))
        #Comparar las soluciones encontradas en cada iteracion del algortimo GRASP y quedarse con aquella que minimize la cantidad total de pasajeros esperando 
        if np.sum(w)<mejor_objetivo_global:
            mejor_objetivo_global=np.sum(w)
            mejor_solucion_global=lista_indices
                    
        return mejor_objetivo_global,mejor_solucion_global
# Ejecutar el algoritmo GRASP
grasp(max_iter=50)

# Mostrar resultados
print("Asignaciones de servicios:")
for i in range(max(n_f)):
    for j in range(max(s_f)):
        for f in range(F):
            for t in range(m):
                if x[i, j, f, t] == 1:
                    print(f"Bus {i}, Servicio {j}, Sentido {f}, Intervalo {t}")

print("Pasajeros abordando por intervalo y sentido:")
print(b)
print("Pasajeros esperando por intervalo y sentido:")
print(w)
```
