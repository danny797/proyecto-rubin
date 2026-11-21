# Observatorio Vera C. Rubin (LSST) DP1 - Caracterización de Estrellas RR Lyrae y Escala de Distancias del Halo

Este repositorio contiene el pipeline astrofísico completo para la detección, clasificación taxonómica, análisis temporal y calibración de candelas estándar en el **Observatorio Vera C. Rubin (Data Preview 1, conjunto `umbramortem/alerts_open_tools_open_sky_2026`)**, enfocado en la calibración cosmológica de la escala de distancias del halo galáctico utilizando estrellas **RR Lyrae** y datos cruzados con **Gaia DR3**.

El proyecto implementa las mejoras metodológicas fundamentadas en la literatura astrofísica reciente (*Marconi et al. 2022*, *Braga et al. 2024*, *Schlafly & Finkbeiner 2011*, *Lindegren et al. 2021*, *Bailer-Jones et al. 2021* y *Ngeow & Bhardwaj 2026*).

---

## 🚀 Arquitectura Modular de Cuadernos (Suite de 3 Notebooks)

Para mantener el código reproducible, pedagógico y desacoplado, el flujo de trabajo está organizado en **tres cuadernos Jupyter complementarios**:

### 1. [`01_pipeline_ingesta_censo_y_crossmatch_gaia.ipynb`](01_pipeline_ingesta_censo_y_crossmatch_gaia.ipynb)
- **Ingesta Estructurada en DuckDB:** Conexión y creación de vistas optimizadas en memoria (`:memory:`) respetando los 3 niveles del pipeline de alertas de Rubin (`v_objects`, `v_detections`, `v_forced`).
- **Censo de Variabilidad:** Criterios estrictos de higiene instrumental (`NOT isdipole`, sin píxeles defectuosos ni saturación), varianza reducida $\chi^2_{\text{red}} \ge 3.0$ y amplitud fraccional $\ge 5\%$. Genera **`rubin_variable_candidates.csv`** (1,920 candidatos).
- **Astrometría y Cruce Posicional:** Resolución ICRS vía API de Fink/LSST, propagación de movimiento propio de Gaia (J2016.0 $\rightarrow$ MJD 61100.0) y consulta ADQL espacial en el archivo TAP de Gaia DR3 con cono de $1.5''$. Genera **`rubin_gaia_crossmatch.csv`**.

### 2. [`02_pipeline_curvas_de_luz_y_modelos_braga.ipynb`](02_pipeline_curvas_de_luz_y_modelos_braga.ipynb)
- **Extracción de Series de Tiempo:** Combinación de detecciones (`diaSource`) y fotometría forzada PSF en DuckDB para muestrear fases de brillo mínimo.
- **Análisis Temporal y Lomb-Scargle:** Malla de 12,000 frecuencias con atenuación de alias diurnos terrestres ($f \pm 1\text{ d}^{-1}$).
- **Plantillas de Fourier (Braga et al. 2024):** Sincronización de fase (*phase-locking*) en banda ancla $r$ ($t_0$) y razones de amplitud fijas $A_b / A_r$ para filtros secundarios ($u, z, y$).
- **Magnitudes Medias Integradas en Intensidad (Pogson):** Derivación de $\langle m \rangle_{\text{int}}$, erradicando el sesgo de la media aritmética por agrupamiento de fase (*phase clumping*).
- **Atlas de Curvas de Luz en PDF:** Generación del archivo multipágina vectorial **`rubin_all_rrlyrae_light_curves.pdf`** (las 40 RR Lyrae graficadas en dos ciclos $\Phi \in [0, 2]$ con observaciones en 6 filtros y curvas de plantilla continuas).
- **Generación de la Tabla Consolidada:** Exporta **`rubin_rrlyrae_period_luminosity_wesenheit_distances.csv`**.

### 3. [`Proyecto_Vera-Rubin.ipynb`](Proyecto_Vera-Rubin.ipynb) (Cuaderno Científico Maestro)
- **Ejecución y Diagnóstico Integrado:** Cuaderno maestro, pedagógico y 100% ejecutable que procesa la muestra purificada de 40 estrellas RR Lyrae.
- **Galería de Curvas de Luz:** Despliega directamente las observaciones y modelos en fase de la muestra representativa de Rubin DP1 y provee el visor interactivo `mostrar_atlas_curvas_luz(pagina)`.
- **Diagrama de Bailey y Poblaciones de Oosterhoff:** Diagnóstico en el plano $\log_{10} P$ vs $A_r$, verificación ZAHB (Rama Horizontal de Edad Cero de Marconi et al. 2022) y trazado del ensamblaje galáctico (OoI vs OoII).
- **Extinción 2D SFD98 y Metalicidad Fotométrica:** Corrección punto a punto con coeficientes específicos de Rubin ($R_\lambda$) y calibración de $[\text{Fe/H}]_{gri}$.
- **Escala de Distancias con Wesenheit ($W_{gr}$):** Inmunidad analítica al enrojecimiento y derivación de distancias cosmológicas al halo ($7.41\text{ a }52.00\text{ kpc}$).
- **Validación Astrométrica en Gaia DR3:** Comparación en el espacio de paralajes $\varpi_{\text{fot}} = 1/d_W$ frente a $\varpi_{\text{Gaia,corr}}$ (Lindegren et al. 2021), erradicando el sesgo no lineal de Lutz-Kelker.

---

## 📁 Estructura del Repositorio

```text
├── 01_pipeline_ingesta_censo_y_crossmatch_gaia.ipynb       # Pipeline 01: Ingesta DuckDB, censo y crossmatch Gaia TAP
├── 02_pipeline_curvas_de_luz_y_modelos_braga.ipynb         # Pipeline 02: Series temporales, plantillas Braga y atlas PDF
├── Proyecto_Vera-Rubin.ipynb                               # Cuaderno científico principal con visualización de curvas de luz
├── rubin_rrlyrae_period_luminosity_wesenheit_distances.csv # Catálogo consolidado de 40 RR Lyrae con distancias Wesenheit
├── rubin_variable_candidates.csv                           # Censo global de 1,920 estrellas variables detectadas en Rubin DP1
├── rubin_gaia_crossmatch.csv                               # Tabla maestra de crossmatch astrométrico con Gaia DR3
├── tabla_coefs_COLS.csv                                    # Coeficientes teóricos de calibración PWZ (Marconi et al. 2022)
├── cache_sfd_dust_extinction.csv                           # Caché de extinción de polvo 2D individual (SFD98)
├── resumen_datos_variables_rubin.txt                       # Resumen ejecutivo consolidado en texto plano
├── rubin_all_rrlyrae_light_curves.pdf                      # Atlas completo vectorial de curvas de luz (40 estrellas, 7 págs)
├── reporte_ngeow2026_rrlyrae_dp1.pdf                       # Reporte científico formal en formato artículo
├── reporte_ngeow2026_rrlyrae_dp1.tex                       # Código fuente en LaTeX del reporte
├── Ngeow_2026_AJ_172_3.pdf                                 # Paper de referencia principal (Ngeow & Bhardwaj 2026)
├── requirements.txt                                        # Dependencias de Python necesarias
├── .gitignore                                              # Archivos excluidos del control de versiones
└── README.md                                               # Esta documentación
```

---

## 🔭 Metodología y Mejoras Astrofísicas Implementadas

1. **Magnitud de Wesenheit $W_{gr}$ y Relación $PWZ$ de Marconi et al. (2022):**
   $$W_{gr} = r - 2.796\,(g - r)$$
   $$W_{gr,0} = -0.486 - 2.540\,\log_{10}(P_{\text{fund}}) + 0.053\,[\text{Fe/H}]$$
   Anulación analítica del vector de enrojecimiento interestelar, logrando una candela estándar inmune a la extinción del polvo.

2. **Mapas de Extinción 2D Individuales (SFD98 / Schlafly & Finkbeiner 2011):**
   - Coeficientes específicos por banda Rubin: $R_u=4.239, R_g=3.303, R_r=2.285, R_i=1.698, R_z=1.263, R_y=1.066$.
   - Rango real de extinción en el campo: $A_r \in [0.018, 0.115]\text{ mag}$ (mediana $0.038\text{ mag}$), eliminando sesgos sistemáticos de hasta 10% causados por asumir valores planos.

3. **Ajuste de Plantillas de Braga et al. (2024) y Phase-Locking en Banda $r$:**
   - Determinación unívoca del origen de fase $t_0$ en la banda ancla $r$.
   - Integración de flujo físico en escala Pogson para magnitudes medias $\langle m \rangle_{\text{int}}$.

4. **Metalicidad Fotométrica $[\text{Fe/H}]_{gri}$:**
   $$[\text{Fe/H}]_{gri} = 21.4\,(g - r)_0 - 37.0\,(r - i)_0 - 4.36 \quad (\sigma = 0.31\text{ dex})$$
   Filtro de estabilidad de Marconi et al. (2022) / Ngeow & Bhardwaj (2026) con prior del halo galáctico ($\sim -1.80\text{ dex}$).

5. **Validación Astrométrica en el Espacio de Paralajes con Gaia DR3:**
   - Comparación directa libre del sesgo de Lutz-Kelker: $\varpi_{\text{fot}} = 1/d_W$ frente a $\varpi_{\text{Gaia,corr}} = \varpi - (-0.025\text{ mas})$ (Lindegren et al. 2021).
   - Residuo astrométrico mediano nulo ($\Delta\varpi = -0.0011\text{ mas}$).

6. **Diagrama de Bailey y Validación ZAHB:**
   - Diagnóstico en el plano $\log_{10} P$ vs $A_r$.
   - Separación de subpoblaciones Oosterhoff I y II.
   - Verificación teórica de que el 100% de la muestra corresponde a estrellas de la Rama Horizontal de Edad Cero (ZAHB) no evolucionadas.

---

## 📊 Síntesis de Resultados Principales

- **Censo de Variabilidad (Rubin DP1):** 1,920 candidatos procesados (1,760 variables galácticas, 78 binarias eclipsantes, 41 RR Lyrae, 21 rotacionales, 11 eruptivas/cataclísmicas, 9 $\delta$ Scuti / SX Phe).
- **Muestra de Calibración RR Lyrae:** 40 estrellas.
  - **RRab (Modo Fundamental):** 25 estrellas (62.5%).
    - *Oosterhoff I (OoI):* 12 estrellas (48.0%), $\langle P_{ab} \rangle = 0.572\text{ d}$ (trazador del colapso in-situ / disco grueso).
    - *Oosterhoff II (OoII):* 13 estrellas (52.0%), $\langle P_{ab} \rangle = 0.645\text{ d}$ (trazador de acreción de galaxias enanas satélites).
  - **RRc (Primer Sobretono):** 15 estrellas (37.5%), $\langle P_c \rangle = 0.309\text{ d}$.
  - **Consistencia ZAHB:** 25/25 RRab (100.0%) concuerdan con la envolvente canónica de ZAHB; 0/25 en modelos evolucionados.
- **Escala de Distancias del Halo:**
  - Mediana: **$18.17\text{ kpc}$** (Media: $18.43\text{ kpc}$, abarcando hasta **$52.00\text{ kpc}$**).
- **Metalicidad Media:** $\langle [\text{Fe/H}]_{gri} \rangle = -1.84\text{ dex}$ (mediana: $-1.88\text{ dex}$), concordante con el halo exterior.
- **Validación con Gaia DR3:** Residuo mediano de paralaje $\Delta\varpi = -0.0011\text{ mas}$.

---

## 💻 Instalación y Ejecución

1. Clonar el repositorio:
```bash
git clone https://github.com/danny797/proyecto-rubin.git
cd proyecto-rubin
```

2. Instalar las dependencias:
```bash
pip install -r requirements.txt
```

3. Abrir y ejecutar los cuadernos en Jupyter:
```bash
# Para el análisis astrofísico principal y visualización:
jupyter notebook Proyecto_Vera-Rubin.ipynb

# Para re-ejecutar el pipeline de extracción y Gaia crossmatch:
jupyter notebook 01_pipeline_ingesta_censo_y_crossmatch_gaia.ipynb

# Para re-ejecutar el pipeline de plantillas Braga y atlas de curvas de luz:
jupyter notebook 02_pipeline_curvas_de_luz_y_modelos_braga.ipynb
```
> **Modo Rápido / Fast-Path:** Todos los cuadernos tienen activada por defecto la bandera `USE_LOCAL_CACHE = True`, por lo que ejecutan todos los cálculos, tablas y figuras en menos de **2 segundos** utilizando las tablas consolidadas del repositorio. Para re-ejecutar las consultas masivas sobre DuckDB o Gaia TAP, basta con cambiar la bandera a `USE_LOCAL_CACHE = False`.
