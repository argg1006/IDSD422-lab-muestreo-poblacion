# Informe: Clasificación de variables y población vs. muestra

**Para:** Gerencia de RetailAndina S.A. · **De:** Equipo de Business Intelligence
**Fecha:** 23/10/2026 · **Asunto:** Base de fidelización: tipos de variables y estimación por muestreo

---

## Resumen ejecutivo

Se clasificaron las 8 variables de la base de fidelización según su naturaleza (cuantitativa o cualitativa) y se comprobó con código. Luego se estimó el ingreso medio mensual de la población (400 clientes, parámetro = 1330.72 USD) con tres muestras de 20 clientes: conveniencia, aleatoria simple y estratificada. Ningún diseño reproduce el parámetro exactamente. La muestra por conveniencia solo incluyó clientes de Quito, así que su cercanía fue casualidad. Antes de decidir con una muestra, la gerencia debe exigir que el diseño sea aleatorio o estratificado y que se reconozca su variabilidad.

## Parte A: Clasificación de las variables

| Variable | Tipo | Justificación (criterio) | Verificación en código |
|---|---|---|---|
| `cliente_id` | Cualitativa nominal | Es un código identificador; no tiene sentido promediarlo ni ordenarlo. | object, 12 únicos (uno por cliente) |
| `edad` | Cuantitativa discreta | Se mide en años enteros; la media tiene sentido. | int64, 12 únicos |
| `numero_hijos` | Cuantitativa discreta | Es un conteo (0 a 4); no existen valores intermedios. | int64, 5 únicos |
| `genero` | Cualitativa nominal | 3 categorías sin orden; no es binaria porque incluye «Otro». | object, 3 únicos |
| `nivel_educacion` | Cualitativa ordinal | Hay orden: Primaria < Secundaria < Universidad < Posgrado. | object, 4 únicos; el orden se declaró como categoría ordenada |
| `ciudad` | Cualitativa nominal | Tres ciudades sin orden entre ellas. | object, 3 únicos |
| `ingresos_mensuales` | Cuantitativa continua | Es una medida en USD; la mitad de un valor tiene sentido. | int64, 12 únicos |
| `satisfaccion` | Cualitativa ordinal | Escala de 2 a 5 con orden, pero la distancia entre niveles no es medible. | int64, 4 únicos (2, 3, 4, 5) |

**P1.** El dtype de `satisfaccion` fue int64, lo que sugiere una variable numérica. Pero el dtype solo dice cómo se guarda el dato, no qué significa: son 4 códigos de una escala de opinión, con orden pero sin distancias iguales, por lo que es ordinal. 

## Parte B: Población vs. muestra

### La población y su parámetro

| Concepto | Valor |
|---|---|
| Población (N) | 400 clientes (Quito 200, Guayaquil 120, Cuenca 80) |
| Variable de interés | Ingresos mensuales (USD) |
| Parámetro poblacional (media) | 1330.72 USD |

Aquí el parámetro se conoce porque es un ejercicio y permite medir qué tan bien estima cada diseño. En la práctica la población es demasiado grande o costosa de medir completa, por eso se muestrea y el parámetro queda desconocido.

### Los tres diseños

| Diseño | n | Media estimada | Composición por ciudad | Diferencia vs. parámetro |
|---|---|---|---|---|
| Conveniencia (`head`) | 20 | 1348.50 | Quito 20 | +17.78 |
| Aleatorio simple (`sample`) | 20 | 1336.00 | Cuenca 8, Quito 8, Guayaquil 4 | +5.28 |
| Estratificado (`groupby` + 5 %) | 20 | 1287.50 | Quito 10, Guayaquil 6, Cuenca 4 | -43.22 |

Medias de 5 sorteos del aleatorio simple:

| Semilla | 1 | 2 | 3 | 4 | 5 |
|---|---|---|---|---|---|
| Media | 1336.00 | 1405.00 | 1235.00 | 1491.50 | 1288.00 |

## Conclusiones

1. El tipo de variable se decide por lo que significa, no por cómo se guarda: `satisfaccion` es int64 pero ordinal, y `genero` tiene 3 categorías, así que no es binaria.
2. Una muestra pequeña puede acertar por casualidad (conveniencia) o fallar aun con buen diseño (estratificado en R); lo que importa es cómo se selecciona, no un resultado puntual.
3. Recomendación a la gerencia: no aceptar análisis basados en muestras por conveniencia para decisiones generales; pedir muestreo aleatorio o estratificado, indicar el tamaño de la muestra y presentar la estimación con su margen de error.