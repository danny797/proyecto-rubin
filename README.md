# Observatorio Vera C. Rubin (LSST) DP1 - Caracterización de Estrellas RR Lyrae y Escala de Distancias de Halo

Este repositorio contiene el análisis fotométrico, astrométrico y cosmológico completo de candidatos a estrellas variables del **Observatorio Vera C. Rubin (Data Preview 1, conjunto `umbramortem`)**, enfocado en la calibración de la escala de distancias del halo galáctico utilizando estrellas **RR Lyrae** y datos cruzados con **Gaia DR3**.

El proyecto implementa las 6 mejoras metodológicas fundamentadas en la literatura astrofísica reciente (*Marconi et al. 2022*, *Braga et al. 2024*, *Schlafly & Finkbeiner 2011*, *Lindegren et al. 2021*, *Bailer-Jones et al. 2021* y *Ngeow & Bhardwaj 2026*).

---

## 🚀 Estructura del Repositorio

```text
├── Proyecto_Vera-Rubin.ipynb                               # Notebook principal ejecutable (con soporte de caché ultrarrápido)
├── rubin_rrlyrae_period_luminosity_wesenheit_distances.csv # Catálogo consolidado de las 40 RR Lyrae con distancias Wesenheit
├── rubin_variable_candidates.csv                           # Censo global de 1,920 estrellas variables detectadas en Rubin DP1
├── rubin_gaia_crossmatch.csv                               # Tabla maestra de crossmatch de alta precisión con Gaia DR3
├── tabla_coefs_COLS.csv                                    # Coeficientes teóricos de calibración PWZ (Marconi et al. 2022)
├── cache_sfd_dust_extinction.csv                           # Caché de extinción de polvo 2D individual (SFD98)
├── resumen_datos_variables_rubin.txt                       # Resumen ejecutivo consolidado en texto plano
├── rubin_all_rrlyrae_light_curves.pdf                      # Curvas de luz ajustadas con plantillas empíricas (Braga et al. 2024)
├── reporte_ngeow2026_rrlyrae_dp1.pdf                       # Reporte científico formal en formato artículo
├── reporte_ngeow2026_rrlyrae_dp1.tex                       # Código fuente en LaTeX del reporte
├── Ngeow_2026_AJ_172_3.pdf                                 # Paper de referencia principal (Ngeow & Bhardwaj 2026)
├── requirements.txt                                        # Dependencias de Python necesarias
├── .gitignore                                              # Archivos excluidos del control de versiones
└── README.md                                               # Esta documentación
```

---

## 🔭 Metodología y Mejoras Astrofísicas Implementadas

1. **Índice de Wesenheit $W_{gr}$ y Relación $PWZ$ de Marconi et al. (2022):**
   - $W_{gr} = r - 2.796(g - r)$
   - $W_{gr,0} = -0.486 - 2.540 \log_{10}(P) + 0.053 [\text{Fe/H}]$
   - Inmunidad total a la ley de extinción local y al enrojecimiento interestelar.

2. **Mapas de Extinción 2D Individuales (SFD98 / Schlafly & Finkbeiner 2011):**
   - Coeficientes específicos por banda Rubin: $R_u=4.239, R_g=3.303, R_r=2.285, R_i=1.698, R_z=1.263, R_y=1.066$.
   - Rango real de extinción en el campo: $A_r \in [0.018, 0.115]\text{ mag}$ (mediana $0.038\text{ mag}$), eliminando sesgos sistemáticos de hasta 10% causados por asumir valores planos.

3. **Ajuste de Plantillas de Braga et al. (2024) y Phase-Locking en Banda $r$:**
   - Determinación robusta de épocas de máximo $t_0$ en banda ancla $r$.
   - Integración de flujo físico para magnitudes medias $\langle m \rangle_{\text{int}}$.

4. **Metalicidad Fotométrica $[\text{Fe/H}]_{gri}$:**
   - $[\text{Fe/H}]_{gri} = 21.4(g - r)_0 - 37.0(r - i)_0 - 4.36$
   - Filtro de estabilidad de Marconi et al. (2022) / Ngeow & Bhardwaj (2026) con prior del halo galáctico ($\sim -1.80\text{ dex}$).

5. **Validación Astrométrica en el Espacio de Paralajes con Gaia DR3:**
   - Comparación libre del sesgo de Lutz-Kelker: $\varpi_{\text{fot}} = 1/d_W$ frente a $\varpi_{\text{Gaia,corr}} = \varpi - (-0.025\text{ mas})$ (Lindegren et al. 2021).
   - Validación cruzada con distancias geométricas bayesianas ($r_{\text{med,geo}}$) de Bailer-Jones et al. (2021).

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
  - **Consistencia ZAHB:** 25/25 RRab (100.0%) caen dentro de la envolvente canónica de ZAHB; 0/25 en modelos evolucionados.
- **Escala de Distancias del Halo:**
  - Mediana: **$18.17\text{ kpc}$** (Media: $18.43\text{ kpc}$, rango de $7.41\text{ a }52.00\text{ kpc}$).
- **Metalicidad Media:** $\langle [\text{Fe/H}]_{gri} \rangle = -1.84\text{ dex}$ (mediana: $-1.88\text{ dex}$), en concordancia con el halo exterior.
- **Validación con Gaia DR3:** Residuo mediano de paralaje $\Delta\varpi = -0.0011\text{ mas}$ (prácticamente nulo).

---

## 💻 Instalación y Uso

1. Clonar el repositorio:
```bash
git clone https://github.com/<tu-usuario>/proyecto-rubin.git
cd proyecto-rubin
```

2. Instalar las dependencias:
```bash
pip install -r requirements.txt
```

3. Abrir y ejecutar el notebook:
```bash
jupyter notebook Proyecto_Vera-Rubin.ipynb
```
> **Nota de Rendimiento:** El notebook tiene activado por defecto el modo caché (`USE_LOCAL_CACHE = True` en la celda 1), por lo que ejecuta todos los cálculos y genera las figuras en menos de **2 segundos** utilizando las tablas de datos precalculadas incluidas en este repositorio.
