---
name: clima
description: Obtiene información del clima actual y pronóstico para Rosario, Santa Fe, Argentina. Úsala cuando el usuario pregunte por el clima, temperatura, pronóstico o condiciones meteorológicas.
---

# Skill: Clima en Rosario

Consulta siempre el clima de **Rosario, Santa Fe, Argentina**. No preguntes por la ubicación ni permitas cambiarla.

## 1. Obtener datos del clima

Usa WebFetch con esta URL fija:

```
https://wttr.in/Rosario,Santa+Fe,Argentina?format=j1&lang=es
```

## 2. Presentar la información

Del JSON obtenido, extrae y muestra:

**Condiciones actuales** (`current_condition[0]`):
- Temperatura actual: `temp_C` °C (y `FeelsLikeC` sensación térmica)
- Descripción: `lang_es[0].value`
- Humedad: `humidity` %
- Velocidad del viento: `windspeedKmph` km/h, dirección: `winddir16Point`
- Visibilidad: `visibility` km
- Presión: `pressure` hPa
- Índice UV: `uvIndex`

**Pronóstico de los próximos 3 días** (`weather[]`):
- Fecha: `date`
- Temperatura máx/mín: `maxtempC` / `mintempC` °C
- Descripción general: `hourly[4].lang_es[0].value` (mediodía)
- Probabilidad de lluvia: `hourly[4].chanceofrain` %

## 3. Formato de respuesta

```
## Clima en Rosario, Santa Fe — {fecha}

**Condición actual:** {descripción}
**Temperatura:** {temp}°C (sensación térmica: {feelsLike}°C)
**Humedad:** {humedad}% | **Viento:** {viento} km/h {dirección}
**Visibilidad:** {vis} km | **Presión:** {presión} hPa | **UV:** {uv}

---
### Pronóstico
| Día | Máx | Mín | Condición | Lluvia |
|-----|-----|-----|-----------|--------|
| ... | ... | ... | ...       | ...    |
```

## Manejo de errores

Si wttr.in no responde, informa al usuario e indícale que intente en unos minutos.
