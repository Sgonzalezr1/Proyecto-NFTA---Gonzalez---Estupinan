El modelo de AMPL debe ser refinado
**Archivo de datos de prueba .dat**
```
param m := 96;
param n := 7;

param alpha := 10;
param h_min := 5;
param h_max := 30;
param t_min := 0;            
param t_rut := 40;    
param C := 45;

param lambda :=
[1, 1] 19,   [1, 2] 19,
[2, 1] 21,   [2, 2] 21,
[3, 1] 22,   [3, 2] 22,
[4, 1] 23,   [4, 2] 23,
[5, 1] 25,   [5, 2] 25,
[6, 1] 26,   [6, 2] 26,
[7, 1] 27,   [7, 2] 27,
[8, 1] 28,   [8, 2] 28,
[9, 1] 29,   [9, 2] 29,
[10, 1] 31,  [10, 2] 31,
[11, 1] 31,  [11, 2] 31,
[12, 1] 32,  [12, 2] 32,
[13, 1] 33,  [13, 2] 33,
[14, 1] 34,  [14, 2] 34,
[15, 1] 34,  [15, 2] 34,
[16, 1] 34,  [16, 2] 34,
[17, 1] 35,  [17, 2] 35,
[18, 1] 35,  [18, 2] 35,
[19, 1] 35,  [19, 2] 35,
[20, 1] 35,  [20, 2] 35,
[21, 1] 35,  [21, 2] 35,
[22, 1] 35,  [22, 2] 35,
[23, 1] 35,  [23, 2] 35,
[24, 1] 35,  [24, 2] 35,
[25, 1] 35,  [25, 2] 35,
[26, 1] 34,  [26, 2] 34,
[27, 1] 34,  [27, 2] 34,
[28, 1] 34,  [28, 2] 34,
[29, 1] 34,  [29, 2] 34,
[30, 1] 34,  [30, 2] 34,
[31, 1] 33,  [31, 2] 33,
[32, 1] 33,  [32, 2] 33,
[33, 1] 33,  [33, 2] 33,
[34, 1] 33,  [34, 2] 33,
[35, 1] 33,  [35, 2] 33,
[36, 1] 33,  [36, 2] 33,
[37, 1] 33,  [37, 2] 33,
[38, 1] 33,  [38, 2] 33,
[39, 1] 33,  [39, 2] 33,
[40, 1] 33,  [40, 2] 33,
[41, 1] 33,  [41, 2] 33,
[42, 1] 33,  [42, 2] 33,
[43, 1] 33,  [43, 2] 33,
[44, 1] 33,  [44, 2] 33,
[45, 1] 33,  [45, 2] 33,
[46, 1] 33,  [46, 2] 33,
[47, 1] 33,  [47, 2] 33,
[48, 1] 34,  [48, 2] 34,
[49, 1] 34,  [49, 2] 34,
[50, 1] 34,  [50, 2] 34,
[51, 1] 34,  [51, 2] 34,
[52, 1] 34,  [52, 2] 34,
[53, 1] 34,  [53, 2] 34,
[54, 1] 34,  [54, 2] 34,
[55, 1] 34,  [55, 2] 34,
[56, 1] 35,  [56, 2] 35,
[57, 1] 35,  [57, 2] 35,
[58, 1] 35,  [58, 2] 35,
[59, 1] 35,  [59, 2] 35,
[60, 1] 36,  [60, 2] 36,
[61, 1] 36,  [61, 2] 36,
[62, 1] 36,  [62, 2] 36,
[63, 1] 36,  [63, 2] 36,
[64, 1] 36,  [64, 2] 36,
[65, 1] 37,  [65, 2] 37,
[66, 1] 37,  [66, 2] 37,
[67, 1] 37,  [67, 2] 37,
[68, 1] 37,  [68, 2] 37,
[69, 1] 37,  [69, 2] 37,
[70, 1] 37,  [70, 2] 37,
[71, 1] 37,  [71, 2] 37,
[72, 1] 37,  [72, 2] 37,
[73, 1] 37,  [73, 2] 37,
[74, 1] 37,  [74, 2] 37,
[75, 1] 36,  [75, 2] 36,
[76, 1] 36,  [76, 2] 36,
[77, 1] 35,  [77, 2] 35,
[78, 1] 35,  [78, 2] 35,
[79, 1] 34,  [79, 2] 34,
[80, 1] 33,  [80, 2] 33,
[81, 1] 32,  [81, 2] 32,
[82, 1] 31,  [82, 2] 31,
[83, 1] 30,  [83, 2] 30,
[84, 1] 29,  [84, 2] 29,
[85, 1] 28,  [85, 2] 28,
[86, 1] 27,  [86, 2] 27,
[87, 1] 26,  [87, 2] 26,
[88, 1] 24,  [88, 2] 24,
[89, 1] 23,  [89, 2] 23,
[90, 1] 22,  [90, 2] 22,
[91, 1] 21,  [91, 2] 21,
[92, 1] 20,  [92, 2] 20,
[93, 1] 19,  [93, 2] 19,
[94, 1] 19,  [94, 2] 19,
[95, 1] 18,  [95, 2] 18,
[96, 1] 17,  [96, 2] 17
;
```
**Archivo del modelo  .mod**
```
param m;					  # Numero de intervalos de tiempo
param n;					  # Numero de buses

set T := {1..m};              # Conjunto de intervalos de tiempo
set B := {1..n};              # Conjunto total de buses
set F := {1..2};              # Conjunto de sentidos


param alpha;				  #Duración de intervalos
param h_min;				  #Tiempo minimo de intervalo
param h_max;				  #Tiempo maximo de intervalo
param lambda{t in T, f in F}; #Tasa de llegada de pasajeros (Cada diez minutos)
param C;					  # Capacidad del bus 
param t_min;            	  #tiempo de maniobra de buses
param t_rut;                  #tiempo de ruta del bus
param r := t_rut/alpha;       # Número de intervalos necesarios para completar el recorrido en un sentido


var x{i in B, f in F, t in T} binary; # Despacho del bus i en el servicio j en la dirección f, tiempo t
var b{t in T, f in F} >= 0, integer;     # Pasajeros que suben al inicio del intervalo [t,t+1], en la dirección f
var w{t in T, f in F} >= 0, integer;      # Pasajeros que esperan en el inicio del intervalo [t,t+1] en la dirección f
var tdex{i in B, f in F} >= 0;          #Variable que almacena tiempo de despacho.


#Restricción - variable que ata tdex al tiempo de despacho
subject to tiempo_despacho{i in B, f in F}:
	tdex[i,f] = sum { t in T} t * x[i,f,t];

#Restricción - Que define que establece que el despacho del primer bus debe ser realizado en el primer intervalo.
subject to despacho_1_bus :
	x[1,1,1] = 1;
	
#Restricción -  Establece que se haga maximo un despacho por intervalo	
subject to 1_bus_por_intervalo {t in 1..card(T)}:
    sum {i in B, f in F} x[i, f, t] <= 1;

#Restricción - Establece que el redespacho del mismo bus sea realizado despues de cumplir el tiempo de ruta (t+4)
subject to intervalo_minimo_redespacho {i in B, f in F, t in T: t + 4 <= m}:
   x[i, f, t] + sum {t_prime in t+1 .. min(t + 4, m)} x[i, f, t_prime] <= 1;

# Restricción - Establece que despues de servir un sentido el siguiente despacho se haga en el siguiente sentido
subject to alternanciaSentido {i in B, t in T: t + r <= m}:
    x[i, 1, t] + sum {tp in t + r .. m} x[i, 1, tp] <= 1
    and
    x[i, 2, t] + sum {tp in t + r .. m} x[i, 2, tp] <= 1;

#Restricción - Establece como es el calculo de los pasajeros en espera, /10 dado que la tasa de demanda es por cada 10 minutos
subject to Pax_espera {t in 2..card(T), f in F}:
    w[t,f] = w[t-1,f] + floor(lambda[t-1,f]/10) * alpha - b[t,f];

#Restricción - Establece que por cada intervalo los pasajeros abordando no puede ser la capacidad del bus
subject to cap_bus {t in T, f in F}:
    b[t,f] <= sum {i in B} x[i, f, t] * C;    

#Restricción - Establece que el tiempo de despacho del bus i+1 debe ser mayor que el del bus i
subject to despacho_secuencial {i in 1..card(B)-1,f in F, t in T}:
    tdex[i+1,f] >= tdex[i,f];   

#Restricción - Establece el limite maximo para que los buses consecutivos sean despachados 
subject to intervalo_maximo {i in 1..card(B)-1 ,f in F}:
  tdex[i+1, f]*alpha - tdex[i, f]*alpha <= h_max;

#Condición - Corriendo el modelo, observamos que gurobi establece algunas variables con decimales, por lo que esta restricción ayuda a que sean binarias
var x_rounded{i in B, f in F, t in T} binary;
subject to round_x {i in B, f in F, t in T}:
    x_rounded[i, f, t] = if x[i, f, t] >= 0.5 then 1 else 0;

#Función objetivo - Minimización de pasajeros en espera en el tiempo t, en el sentido f
minimize total_waiting_passengers:
    sum {t in T, f in F} w[t,f];
```
