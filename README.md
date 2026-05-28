# Proyecto final E2D2 - Transformer encoder-decoder de difusion discreta

## Integrantes

- Nicolas Vasquez Renjifo - nicolas.vasquez@uao.edu.co
- Jorge Luis Fong Gutierrez - jorge.fong@uao.edu.co
- Jhonatan David Rengifo Bermeo - jhonatan.rengifo@uao.edu.co
- Mateo Gonzalez Ruiz - mateo.gonzalez@uao.edu.co

## Descripcion

Este proyecto implementa y analiza inferencia con E2D2, una arquitectura Transformer encoder-decoder basada en modelos de difusion discreta, aplicada a traduccion automatica aleman-ingles sobre WMT14 de-en.

El proyecto no entrena desde cero. Usa pesos preentrenados publicados en Hugging Face y se enfoca en comprender la arquitectura, ejecutar inferencia, evaluar configuraciones de generacion y comparar contra un baseline encoder-decoder autoregresivo.

## Articulo base

- Paper: Encoder-Decoder Diffusion Language Models for Efficient Training and Inference
- arXiv: https://arxiv.org/abs/2510.22852
- Codigo oficial: https://github.com/kuleshov-group/e2d2
- Modelo principal: https://huggingface.co/kuleshov-group/e2d2-wmt
- Coleccion de modelos: https://huggingface.co/collections/kuleshov-group/e2d2

## Archivos principales

- `Notebook/PF-TRANSFORMERS_NVR - JLFG - JDRB - MGR.ipynb`: notebook final documentado y ejecutado en Colab.
- `Informe/Informe_E2D2_IEEE.tex`: informe completo en formato IEEE.
- `Informe/Informe_E2D2_IEEE.bib`: bibliografia BibTeX del informe.
- `Informe/Informe_PF_Transformers.pdf`: PDF compilado del informe.
- `Guia_Sustentacion_E2D2.md`: guia de preparacion para la sustentacion, con guion, conceptos, metricas, resultados y posibles preguntas.
- `Outputs/e2d2_wmt_ablation_summary.csv`: resumen de configuraciones E2D2.
- `Outputs/e2d2_wmt_ablation_outputs.csv`: salidas E2D2 por ejemplo.
- `Outputs/translation_model_comparison_summary.csv`: comparacion inicial E2D2 vs OPUS-MT.
- `Outputs/opus_wmt_outputs.csv`: salidas del baseline OPUS-MT.
- `Outputs/qualitative_error_analysis.csv`: analisis cualitativo por ejemplo.
- `Outputs/extended_150_summary.csv`: resumen de la evaluacion extendida de 150 ejemplos.
- `Outputs/extended_150_outputs.csv`: salidas de la evaluacion extendida de 150 ejemplos.
- `Insumos/2510.22852v1.pdf`: paper base.

## Como ejecutar el notebook

1. Abrir `E2D2_WMT_Colab_Project_Documentado.ipynb` en Google Colab.
2. Activar GPU: `Runtime > Change runtime type > GPU`.
3. Ejecutar todas las celdas en orden.
4. Si Colab pide reiniciar despues de instalar dependencias, reiniciar y continuar desde las celdas de imports/carga del modelo.
5. Al finalizar, descargar los CSV generados.

## Demo interactiva

El notebook incluye la seccion `6.1. Traducción interactiva en vivo`. Permite:

- editar una variable `live_german_text` y traducir inmediatamente;
- usar un widget con caja de texto y boton `Traducir`;
- comparar contra OPUS-MT si la seccion del baseline ya fue ejecutada.

Para una defensa o demostracion, basta ejecutar hasta la carga del modelo E2D2 y luego usar la celda interactiva. Si se quiere comparar contra OPUS-MT, ejecutar primero la seccion del baseline.

## Requisitos de ejecucion

- Google Colab con GPU. La corrida registrada uso Tesla T4.
- Internet activo para descargar modelos y dataset.
- Dependencias instaladas desde el notebook:
  - `transformers==4.52.4`
  - `accelerate==1.11.0`
  - `datasets==4.2.0`
  - `evaluate`
  - `sacrebleu`
  - `sentencepiece==0.2.1`
  - `einops==0.8.1`
  - `sacremoses`

## Reproducibilidad

El notebook final fija las revisiones de Hugging Face:

- E2D2-WMT: `7c65309acc0d6d89049c9aca4ea02f9d10d1da5a`
- OPUS-MT de-en: `1a922f3b32a8e809e17a47d4b32142d8105924e5`

## Resultados principales extendidos

Sobre 150 ejemplos de WMT14 de-en:

| Modelo/configuracion | BLEU | chrF | Tiempo promedio | Tokens/s | Repeticion bigrama |
|---|---:|---:|---:|---:|---:|
| E2D2 b4/s4 cache | 16.53 | 49.66 | 0.706 s | 50.32 | 0.077 |
| OPUS-MT autoregresivo | 25.02 | 54.09 | 0.189 s | 144.68 | 0.005 |

La configuracion oficial de E2D2 (`block_size=4`, `num_steps=4`, cache activado) fue la mas equilibrada dentro de E2D2. OPUS-MT supero a E2D2 en la evaluacion extendida, lo cual se discute criticamente en el informe.

## Ablation study

La ablacion se conserva sobre 30 ejemplos para estudiar sensibilidad de parametros:

| Configuracion E2D2 | BLEU | chrF | Tiempo promedio | Tokens/s | Repeticion bigrama |
|---|---:|---:|---:|---:|---:|
| b2/s2 cache | 0.00 | 0.31 | 0.140 s | 15.75 | 0.033 |
| b4/s4 cache | 17.85 | 50.00 | 1.066 s | 37.24 | 0.092 |
| b8/s8 cache | 10.13 | 45.65 | 0.950 s | 71.03 | 0.240 |

## Evaluacion extendida recomendada

El notebook documentado incluye una seccion adicional para correr 150 ejemplos con:

- E2D2 oficial: `block_size=4`, `num_steps=4`, `use_cache=True`.
- OPUS-MT autoregresivo.

Esta seccion guarda progreso cada 25 ejemplos y produce:

- `extended_150_outputs_partial.csv`
- `extended_150_outputs.csv`
- `extended_150_summary.csv`

Si se ejecuta esta seccion, se recomienda usar esos resultados como evaluacion principal y conservar la evaluacion de 30 ejemplos como ablation study/control experimental.

## Limitacion conocida

La configuracion E2D2 con `use_cache=False` fallo en todos los ejemplos con error de dimensiones:

`RuntimeError: The size of tensor a (4) must match the size of tensor b (0) at non-singleton dimension 2`

Se reporta como limitacion tecnica de esta ruta de inferencia/checkpoint, no como resultado competitivo.
