# Proyecto-IA
# Bogotá Visible 2023–2024  
**Estadística para la dignidad: que ningún barrio quede fuera del mapa.**

> Este repositorio es un acto de transparencia: aquí están las tripas del proyecto, con el que buscamos **medir con rigor para reparar con justicia**. Donde la muestra calla, dejamos hablar al territorio; donde el modelo duda, habla la **incertidumbre**. Este README es el documento completo del proyecto (versión viva).

---

## Tabla de contenido
1. [Manifiesto](#manifiesto)
2. [Problema y justificación](#problema-y-justificación)
3. [Delimitación (tiempo y espacio)](#delimitación-tiempo-y-espacio)
4. [Preguntas de investigación](#preguntas-de-investigación)
5. [Objetivos](#objetivos)
6. [Variables del IPM (15 oficiales)](#variables-del-ipm-15-oficiales)
7. [Datos y contrato](#datos-y-contrato)
8. [Metodología](#metodología)
9. [Plan de validación y auditoría](#plan-de-validación-y-auditoría)
10. [Cronograma sugerido (10 semanas)](#cronograma-sugerido-10-semanas)
11. [Entregables](#entregables)
12. [Ética y salvaguardas](#ética-y-salvaguardas)
13. [Estructura del repositorio](#estructura-del-repositorio)
14. [Quickstart (Colab)](#quickstart-colab)
15. [Guía de reproducción local](#guía-de-reproducción-local)
16. [Licencia, cita y contacto](#licencia-cita-y-contacto)
17. [Bibliografía mínima](#bibliografía-mínima)

---

## Manifiesto
Medir es decidir quién existe en el presupuesto y quién no. La estadística no es ornamento: es **poder público** hecho número. Este proyecto nace para sacar de la sombra a las **localidades y UPZ** de Bogotá que no caben en el promedio.  
Nuestra línea: **ciencia con responsabilidad**. Nada de cifras espectaculares sin intervalos. Nada de mapas que estigmaticen. Nada de cajas negras sin bitácora. Aquí todo deja rastro: **datos, código y decisiones**.

---

## Problema y justificación
- La **Encuesta Multipropósito** (EM) no siempre permite granularidad sublocal (UPZ) con precisión aceptable.  
- Políticas que se diseñan con promedios invisibilizan diferencias de **periferia, acceso y ruralidad urbana**.  
- Bogotá cuenta con cartografía pública y geodatos que permiten **enriquecer** la información de encuesta y **estabilizar** estimaciones mediante modelos para **pequeñas áreas** (SAE) y **ML**.  
**Para qué sirve**: priorizar inversión social y seguimiento territorial de metas, **con incertidumbre reportada** y auditoría de sesgos.

---

## Delimitación (tiempo y espacio)
- **Temporal:** 2023–2024.  
- **Espacial:** Bogotá D.C.; **20 localidades** y, cuando la calidad/anonimización lo permita, **UPZ**.

---

## Preguntas de investigación
1) ¿En cuánto mejoran los errores/varianza de la medición sublocal de pobreza frente a la **estimación directa** al incorporar **covariables geoespaciales**?  
2) ¿Qué combinación (Fay–Herriot, ELL, ML + calibración) logra mejor balance **sesgo–varianza** bajo **validación espacial** (*leave-one-locality-out*)?  
3) ¿Cómo se comporta la **cobertura** de intervalos en contextos de **ruralidad/periferia** y baja accesibilidad?

---

## Objetivos
**General.** Estimar y mapear pobreza (IPM y monetaria) por **localidad** y **UPZ** en Bogotá 2023–2024 con SAE/ML, reportando **incertidumbre** y **auditorías de sesgo**.

**Específicos.**
- Integrar EM + cartografía (IDECA) + geodatos en un **dataset reproducible**.  
- Desarrollar **directo**, **Fay–Herriot (FH)** y **Elbers–Lanjouw–Lanjouw (ELL)** + **ML** supervisado con calibración probabilística.  
- Implementar **validación espacial** (LOLO) + métricas de cobertura.  
- Entregar **mapas**, **tablas** e **insumos de decisión** por localidad/UPZ.

---

## Variables del IPM (15 oficiales)
**Regla general:** 0 = no privación, 1 = privación. Umbral pobreza IPM: `ipm_score ≥ 0.333`.

| Indicador | `variable_code` | Regla de privación (hogar) | Peso |
|---|---|---|---:|
| Analfabetismo | `analfabetismo` | ≥1 persona 15+ no lee/escribe | 0.10 |
| Bajo logro educativo | `bajo_logro_educativo` | Promedio años aprobados (15+) < 9 | 0.10 |
| Barreras a servicios para cuidado de la primera infancia | `cuidado_primera_infancia` | Niño 0–5 sin salud/nutrición/cuidado integrales | 0.05 |
| Barreras de acceso a servicios de salud | `barreras_acceso_salud` | Necesitó en 30d y **no acudió** | 0.10 |
| Desempleo de larga duración | `desempleo_larga_duracion` | ≥1 PEA desempleado >12 meses | 0.10 |
| Hacinamiento crítico | `hacinamiento_critico` | >3 personas por cuarto habitable | 0.04 |
| Inadecuada eliminación de excretas | `excretas_inadecuadas` | Sin alcantarillado/solución adecuada | 0.04 |
| Inasistencia escolar | `inasistencia_escolar` | Niño 6–16 no asiste a institución | 0.05 |
| Material inadecuado de paredes exteriores | `paredes_inadecuadas` | Madera burda/zinc/tela/cartón/desechos/sin paredes | 0.04 |
| Material inadecuado de pisos | `pisos_inadecuados` | Piso de tierra o equivalente | 0.04 |
| Rezago escolar | `rezago_escolar` | 7–17 por debajo de norma edad–grado | 0.05 |
| Sin acceso a fuente de agua mejorada | `sin_agua_mejorada` | Sin acueducto / fuente no mejorada | 0.04 |
| Sin aseguramiento en salud | `sin_aseguramiento_salud` | ≥1 persona >5 sin aseguramiento | 0.10 |
| Trabajo infantil | `trabajo_infantil` | 12–17 trabajando | 0.05 |
| Trabajo informal | `trabajo_informal` | Ningún ocupado formal (p. ej., pensión) | 0.10 |

---

## Datos y contrato
**Entradas mínimas (`data/raw/`):**
1. **Series oficiales 2018–2024**: `anex-PMultidimensional-Departamental-2024.xlsx`  
   → el parser genera `data/processed/privaciones_departamentos_tidy.csv` y `bogota_departamento_tidy.csv`.
2. **Hogares Bogotá 2023–2024**: `hogares_em_2023_2024.(xlsx/csv)`  
   - `household_id` (único), `localidad_code` ("01"–"20"), `upz_code` (opcional), `peso_muestral` (>0), `anio`.  
   - 15 privaciones IPM **0/1** (o variables fuente para derivarlas).
3. **Covariables por localidad**: `cov_localidad_2023_2024.(xlsx/csv)`  
   - `localidad_code, anio, viirs_mean, densidad, acces_min, elev_m, ruralidad_local (0/1)`  
**Opcionales:** `localidades.geojson` (mapas), `pseudo_censo_upz.csv` (ELL).

**Salidas (`data/processed/`):**  
`hogares_em_2023_2024.csv`, `cov_localidad_2023_2024.csv`, `privaciones_departamentos_tidy.csv`, `bogota_departamento_tidy.csv`, `pseudo_censo_upz.csv` (si aplica).

---

## Metodología
1) **ETL + features**  
   - Limpieza EM y construcción de **privaciones IPM** a nivel hogar.  
   - Integración de **geodatos** (VIIRS, densidad, accesibilidad, relieve) por localidad/UPZ.  
2) **Estimación base**  
   - **Directo** por localidad: tasa + varianza.  
3) **SAE (área y unit-level)**  
   - **Fay–Herriot (FH)**: EBLUP/REML (o Bayesiano), con covariables territoriales.  
   - **ELL**: modelo a hogar + pseudo-censo UPZ → simulación y agregación con **intervalos**.  
4) **ML supervisado**  
   - Logit/Elastic-net, Random Forest, LightGBM; **calibración** (isotónica) y **ensamble**.  
5) **Validación y reporte**  
   - **LOLO** (leave-one-locality-out) y/o bloques geográficos.  
   - Métricas: AUC, Brier, MAE/RMSE, **cobertura 95%**; diagnóstico de **sesgo sistemático**.

---

## Plan de validación y auditoría
- **CV espacial (LOLO)** para evitar “fugas de barrio”.  
- **Cobertura**: % de estimaciones cuyo **IC 95%** contiene el valor “verdad proxy” (o directo).  
- **Auditoría** por **ruralidad/periferia/accesibilidad**: comparar error y cobertura por estratos territoriales.  
- **Estabilidad**: sensibilidad a covariables (ablation) y a hiperparámetros.

---

## Cronograma sugerido (10 semanas)
**1–2** Gobernanza de datos y diccionario • **3–4** ETL + geodatos • **5–6** Directo + FH + ML • **7–8** ELL + ensamble • **9** Auditoría + mapas • **10** Informe/README final.

---

## Entregables
- **Tablas**: `results/estimaciones_localidad.csv` (con IC).  
- **Mapas**: `results/mapa_localidades_*.png` (con bandas/sombreado de incertidumbre).  
- **Fichas por localidad**: `results/fichas_localidad/`.  
- **Documentación**: este README + `docs/` con resumen ejecutivo.

---

## Ética y salvaguardas
- **Complementariedad**: insumos para decisión territorial; **no sustituyen** cifras oficiales.  
- **Transparencia**: publicar parámetros, semillas y errores.  
- **Privacidad**: agregación mínima; en UPZ, aplicar umbrales de anonimización.  
- **Cuidado narrativo**: mapas con contexto e **intervalos** para evitar lecturas estigmatizantes.

---

## Estructura del repositorio
