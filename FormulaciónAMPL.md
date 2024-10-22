El modelo de AMPL esta por refinar dado que se presentan despachos aleatorio, no secuenciales.
**Archivo de datos de prueba .dat**
```
set F := 1 2;         # Sentidos 1 y 2
set T := 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18;       # Intervalos de tiempo de 1 a 18
set B := 1 2 3 4 5 6 7 8 9 10;       # Total de 10 buses

set B_f[1] := 1 2 3 4 5;  # Buses del 1 al 5 para el sentido 1
set B_f[2] := 6 7 8 9 10; # Buses del 6 al 10 para el sentido 2

set S := 1 2 3 4 5;        # Servicios (5 servicios por bus)
set S_f[1] := 1 2 3 4 5;   # Servicios 1, 2, 3, 4, 5 para el sentido 1
set S_f[2] := 1 2 3 4 5;   # Servicios 1, 2, 3, 4, 5 para el sentido 2

# Parámetros
param P := 180;               # Periodo de análisis en minutos
param alpha := 5;            # Intervalos donde se carga el sistema
param n_f := 5 5;            # Tamaño de la flota (5 buses por sentido)
param s_f := 5 5;            # Número de servicios por sentido
param h_min := 0;            # Intervalo mínimo de despacho (5 minutos)
param h_max := 20;           # Intervalo máximo de despacho (15 minutos)
param C := 45;               # Capacidad de buses (45 pasajeros)
param m := 18;               # Número de intervalos en el periodo de análisis
param t_min := 2;            # Tiempo de maniobra (2 minutos)
param t_rut := 39;           # Tiempo de ruta (39 minutos)

# Tasas
param lambda :=
[1, 1] 5,   [1, 2] 8,    
[2, 1] 6,   [2, 2] 10,   
[3, 1] 7,   [3, 2] 12,   
[4, 1] 6,   [4, 2] 11,   
[5, 1] 5,   [5, 2] 5,
[6, 1] 2,   [6, 2] 8,   
[7, 1] 5,   [7, 2] 7,   
[8, 1] 6,   [8, 2] 6,   
[9, 1] 9,   [9, 2] 6,   
[10, 1] 12,  [10, 2] 7,  
[11, 1] 4,  [11, 2] 4,  
[12, 1] 5,  [12, 2] 6,  
[13, 1] 6,  [13, 2] 8,  
[14, 1] 8,  [14, 2] 5,  
[15, 1] 7,  [15, 2] 6,  
[16, 1] 5,  [16, 2] 7,  
[17, 1] 9,   [17, 2] 3,  
[18, 1] 10,  [18, 2] 7;
```
**Archivo del modelo  .mod**
```
#Definición de sets
set F;              # Conjunto de sentidos (1, 2)
set T;              # Conjunto de intervalos de tiempo
set B;              # Conjunto total de buses
set B_f{F} within B; # Conjunto de buses por sentido
set S;              # Conjunto de servicios
set S_f {f in F} within S;    # Conjunto de servicios por cada sentido

# Parámetros
param P;                     # Periodo de análisis expresado en minutos
param alpha;                 # Intervalos de análisis donde se carga el sistema
param n_f{F};                # Tamaño de la flota para el sentido f
param s_f{F};                # Número de servicios para el sentido f
param h_min;                 # Intervalo mínimo (5 minutos)
param h_max;                 # Intervalo máximo (15 minutos)
param C;                     # Capacidad de buses (45 pasajeros)
param m;                     # Número de intervalos en el periodo de análisis
param lambda{T, F};          # Tasa de llegada de pasajeros en intervalo t, sentido f
param t_min;                 # Tiempo de maniobra al final de la ruta
param t_rut;                 # Tiempo de maniobra al final de la ruta

var x{B, S, F, T} binary; # Despacho del bus i en el servicio j en la dirección f, tiempo t
var b{T, F} >= 0, integer; # Pasajeros que suben al inicio del intervalo [t,t+1], en la dirección f
var w{T, F} >= 0, integer; # Pasajeros que esperan en el inicio del intervalo [t,t+1] en la dirección f
var t_disp{B, S, F} >= 0; #Variable que almacena correctamente los tiempso de despacho.

#Restricción * que asocia la variable t_Disp al tiempo de despacho de x
subject to Tiempo_despacho {i in B, j in S, f in F}:
    t_disp[i, j, f] = sum {t in T} t * x[i, j, f, t];

#Restricción 1 - Intervalo minimo y maximo entre despachos
subject to intervalo_entre_despachos {f in F, j in S, i in 1..card(B_f[f])-1}:
   h_min <= t_disp[i+1, j, f]*alpha - t_disp[i, j, f]*alpha <= h_max;

#Restricción * - Despacha buses secuencialmente
subject to despacho_secuencial {f in F, j in S_f[f], i in 1..card(B_f[f])-1}:
    t_disp[i+1, j, f] >= t_disp[i, j, f];
    
#Restricción 2 - Intervalo entre ultimo bus de ciclo o servicio y siguiente
subject to interval_last_to_first {f in F, j in 1..card(S_f[f])-1}:
    h_min <= t_disp[1, j+1, f] - t_disp[card(B_f[f]), j, f] <= h_max;

#Restricción 3 - Maximo de un despacho por servicio en el mismo bus t sentido
subject to max_despacho_por_Servicio {f in F, i in B_f[f], j in S_f[f]}:
    sum {t in T} x[i, j, f, t] <= 1;
#Restricción 4 - Maximo de un despacho por intervalo
subject to max_bus_por_intervalo {f in F, t in T}:
    sum {i in B_f[f], j in S_f[f]} x[i, j, f, t] <= 1;
#Restricción 5 - Calculo de pasajeros en espera   
subject to waiting_passengers {f in F, t in 2..card(T)}:
    w[t, f] = w[t-1, f] + lambda[t-1, f] * alpha - b[t, f];
#Restricción 6 - Capacidad del bus    
subject to passenger_capacity {f in F, t in T}:
    b[t, f] <= sum {i in B_f[f], j in S_f[f]} x[i, j, f, t] * C;

minimize total_waiting_passengers:
    sum {t in T, f in F} w[t,f];
```
