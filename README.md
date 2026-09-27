# Proyecto ConnectaTel
Perfil estadístico y segmentación de clientes

## Objetivo

Como analista de datos, evaluar el **comportamiento de los clientes** de ConnectaTel, una empresa de telecomunicaciones en Latinoamérica, con información registrada hasta el año 2024. El propósito es construir un **perfil estadístico** de los clientes, detectar **comportamientos atípicos (outliers)** y crear **segmentos de clientes** que permitan identificar patrones de consumo, diseñar estrategias de retención y sugerir mejoras en los planes ofrecidos.

**Preguntas que responde el dashboard:**
- ¿Qué problemas de calidad tenían originalmente los datos y qué proporción de registros afectaban?
- ¿Qué segmentos de clientes existen según edad y nivel de uso (llamadas/mensajes)?
- ¿Qué segmentos parecen más valiosos para el negocio (por tipo de plan)?
- ¿Qué patrones de uso extremo (outliers) se detectaron y qué implican?
- ¿Qué recomendaciones se pueden dar para mejorar o crear nuevos planes?

## Datos

- **Periodo:** Registros hasta el cierre de 2024 (usuarios registrados desde 2022; uso de servicios reportado solo para 2024)
- **Tamaño:**
  - `plans.csv` — 2 filas × 8 columnas (catálogo de planes Básico y Premium)
  - `users.csv` (`users_latam.csv`) — 4,000 filas × 8 columnas
  - `usage.csv` — 40,000 filas × 6 columnas (llamadas y mensajes)
- **Variables principales:**
  - **plans:** `plan_name`, `messages_included`, `gb_per_month`, `minutes_included`, `usd_monthly_pay`, `usd_per_gb`, `usd_per_message`, `usd_per_minute`
  - **users:** `user_id`, `first_name`, `last_name`, `age`, `city`, `reg_date`, `plan`, `churn_date`
  - **usage:** `id`, `user_id`, `type` (call/text), `date`, `duration`, `length`
  - **Variables derivadas:** `cant_mensajes`, `cant_llamadas`, `cant_minutos_llamada`, `grupo_uso` (Bajo/Medio/Alto), `grupo_edad` (Joven/Adulto/Adulto Mayor)

## Herramientas

- Python (pandas, seaborn, matplotlib)
- Técnicas: detección de sentinels, análisis de valores faltantes (MAR), método IQR para outliers, segmentación por reglas lógicas
- Jupyter Notebook

## Principales conclusiones

1. **Problemas de calidad detectados y corregidos:**
   - `age` contenía el valor centinela **-999** (dato inválido), reemplazado por la mediana.
   - `city` tenía el centinela **"?"**, reemplazado por nulos (`pd.NA`); representa ~12% de los registros (469 de 4,000).
   - `churn_date` tiene un 88% de valores nulos (3,534 de 4,000), por lo que se descarta para análisis directo.
   - En `usage`, los nulos de `duration` y `length` **no son aleatorios (MAR)**: dependen del tipo de registro (`call` vs `text`), por lo que se dejaron como nulos de forma justificada.
   - Se detectaron fechas imposibles: años **2026** en `reg_date`, marcados como nulos por estar fuera del rango esperado (hasta 2024).

2. **Segmentación por edad:** predominan los clientes **"Adulto"** (30–59 años); hay muy pocos clientes **"Joven"** (<30 años), lo que sugiere una base de usuarios envejecida.

3. **Segmentación por nivel de uso:** la mayoría de los clientes se ubica en **"Uso medio"** (llamadas y mensajes < 10); el segmento de **"Alto uso"** es el más pequeño. No hay muchos usuarios que exploten realmente el servicio.

4. **Outliers relevantes:** `cant_minutos_llamada` presenta numerosos valores atípicos por encima del límite superior (~62 minutos), indicando usuarios con consumo de llamadas muy por encima del comportamiento típico. `cant_mensajes` y `cant_llamadas` presentan pocos outliers. Se decidió **conservar los outliers** ya que el negocio necesita identificarlos, no eliminarlos.

5. **Valor comercial de los segmentos:** los clientes con plan **Premium** representan mayor ingreso por usuario; los usuarios con consumo de llamadas muy elevado son un segmento de riesgo/oportunidad para cobros por excedente o nuevos planes.

## Aprendizajes

- Practicar la detección y tratamiento de **valores centinela** (-999, "?") distintos de nulos verdaderos, y decidir entre imputar, eliminar o dejar como nulos según el porcentaje de faltantes.
- Aplicar el concepto de **MAR (Missing At Random)** para justificar por qué ciertos nulos no deben imputarse, verificando su dependencia de otra columna categórica (`type`).
- Reforzar el uso del **método IQR** para detectar outliers y la importancia de decidir con criterio de negocio si deben conservarse o tratarse (no siempre se eliminan).
- Practicar la creación de **segmentos de clientes** mediante reglas lógicas simples (edad, nivel de uso) como primer paso antes de técnicas más avanzadas (ej. clustering).
- Aprender a comunicar hallazgos técnicos en un **insight ejecutivo** orientado a decisiones de negocio (planes, retención, pricing).

## Contacto

- LinkedIn: (https://www.linkedin.com/in/emma-solorzano-hernandez-jauregui-200301345/)
- Perfil de Tableau Public: https://public.tableau.com/app/profile/emma.solorzano7415/vizzes
