# Práctica Evaluable — Unidad 1
## Fundamentos de IA Generativa y Large Language Models · Análisis Comparativo de Técnicas Generativas

**Nicolás Martín Castaño** · 12 de marzo de 2026

---

## Parte 1 · Selección de Técnicas Generativas

### Ejercicio 1.1 — Casos de uso

| Caso de uso | Técnica | Justificación |
|---|---|---|
| App móvil filtros artísticos en tiempo real (<100ms) | **GAN** | Una sola pasada forward en inferencia. Variantes como CycleGAN están especializadas en transferencia de estilo imagen-a-imagen. |
| Arte digital de alta calidad con control por texto | **Difusión** | Estado del arte en calidad con control semántico preciso mediante prompts. La latencia no es crítica aquí. |
| Detección de anomalías médicas con espacio latente interpretable | **VAE** | Espacio latente continuo y probabilístico. Las anomalías se detectan como puntos de baja probabilidad. |
| Datos sintéticos para reconocimiento facial preservando privacidad | **GAN** | StyleGAN genera rostros de alta calidad. Al ser sintéticos, no corresponden a personas reales. |
| Asistente virtual sobre documentación técnica | **LLM** | Caso de uso canónico. Combinado con RAG puede consultar documentación específica con precisión. |
| Interpolación entre estilos artísticos para animación | **VAE** | El espacio latente continuo permite transiciones suaves entre estilos, crítico para animación. |

### Ejercicio 1.2 — Trade-offs

| Criterio | GANs | VAEs | Difusión | LLMs |
|---|---|---|---|---|
| Velocidad de generación | Alta | Alta | Baja | Media |
| Calidad de salida | Alta | Media | Alta | Alta |
| Estabilidad de entrenamiento | Baja | Alta | Alta | Alta |
| Control sobre la salida | Baja | Media | Alta | Alta |
| Facilidad de uso | Baja | Media | Alta | Alta |

---

## Parte 2 · Ciclo de Vida de LLMs

### Ejercicio 2.1 — Ordenar el pipeline

| Etapa | Orden |
|---|---|
| Recopilación de datos de entrenamiento (Common Crawl, libros, código) | 1 |
| Pre-entrenamiento con objetivo de predicción del siguiente token | 2 |
| Fine-tuning con datos específicos del dominio | 3 |
| RLHF con feedback de evaluadores humanos | 4 |
| Evaluación y red-teaming de seguridad | 5 |
| Despliegue como API o producto | 6 |

### Ejercicio 2.2 — Análisis de alineamiento

**a) ¿Por qué el modelo base responde de manera literal?**

El modelo base fue entrenado únicamente para predecir el siguiente token, sin ningún criterio ético. No distingue entre solicitudes maliciosas o legítimas — simplemente genera la continuación más probable según los patrones aprendidos de sus datos de entrenamiento.

**b) ¿Qué aprendió el modelo durante el RLHF?**

Durante el RLHF, el modelo aprendió qué respuestas son consideradas aceptables mediante un sistema de recompensas y penalizaciones. Aprendió a identificar solicitudes potencialmente dañinas y a rechazarlas, priorizando la seguridad sobre la simple predicción de texto.

**c) ¿Puede el alineamiento ser excesivo? Ejemplo de over-refusal.**

Sí. Por ejemplo, un estudiante de ciberseguridad que pregunta cómo funcionan los virus informáticos con fines académicos podría recibir una negativa injustificada por asociación superficial con contenido dañino.

---

## Parte 3 · Tokenización y Parámetros

### Ejercicio 3.1 — Análisis de tokenización

| Texto | Tokens (aprox.) | Observación |
|---|---|---|
| "Hello, world!" | 4 | Tokenización eficiente en inglés |
| "Hola, mundo!" | 4–5 | Similar por alfabeto compartido |
| "Funcionamiento de transformers" | 5–6 | "Funcionamiento" se divide en subwords |
| `def calculate_sum(a, b): return a + b` | ~13 | Cada símbolo sintáctico cuenta como token |
| "日本語のテキスト" | ~10 | Kanji/hiragana/katakana se fragmentan agresivamente |

El tokenizador está optimizado para inglés. El español tiene un sobrecoste moderado al compartir el alfabeto latino. El japonés, al usar tres sistemas de escritura distintos, puede multiplicar por 2–3 el número de tokens. El código es costoso porque cada símbolo sintáctico (`:`, `+`, `()`) cuenta como token independiente.

### Ejercicio 3.2 — Experimentación con parámetros

| Configuración | Resultado esperado/obtenido |
|---|---|
| Temperature = 0.2 | "El bosque estaba envuelto en una densa niebla. Los árboles centenarios proyectaban sombras alargadas sobre el suelo húmedo." |
| Temperature = 0.8 | "Entre los árboles retorcidos, el silencio tenía una textura casi palpable. La luz filtrada creaba patrones inquietantes en el musgo." |
| Temperature = 1.5 | "El bosque respiraba secretos olvidados, sus raíces tejiendo historias imposibles bajo la luna que nunca llegaba a tocar el suelo." |

Temperature baja para tareas que requieren precisión (código, respuestas técnicas). Temperature alta para tareas creativas donde la originalidad prima sobre la exactitud (escritura creativa, brainstorming).

---

## Parte 4 · Reflexión Crítica

### Ejercicio 4.1 — Limitaciones

| Limitación | Impacto en producción |
|---|---|
| Alucinaciones | Genera información falsa con aparente seguridad, requiriendo verificación adicional en aplicaciones críticas. |
| Conocimiento desactualizado | Desconoce eventos posteriores al entrenamiento, poco fiable en dominios que evolucionan rápido. |
| Sesgos heredados | Reproduce discriminaciones de sus datos, generando respuestas injustas hacia ciertos grupos. |
| Ventana de contexto limitada | No procesa documentos extensos de una vez, perdiendo coherencia global en tareas complejas. |

### Ejercicio 4.2 — Caso ético

**a) Riesgos principales**

1. Diagnósticos incorrectos: el 5% de error puede derivar en tratamientos inadecuados con consecuencias fatales.
2. Alucinaciones médicas: puede recomendar tratamientos inexistentes o incorrectos con total confianza aparente.
3. Falta de contexto clínico: sin historial médico ni exploración física, el diagnóstico es inevitablemente incompleto.

**b) Medidas de mitigación**

1. Supervisión médica obligatoria: el LLM como apoyo, nunca como decisor final.
2. Disclaimers y derivación: advertir de sus limitaciones e indicar cuándo se requiere atención urgente.
3. RAG con fuentes verificadas: conectar el modelo a bases de datos médicas oficiales para reducir alucinaciones.

**c) ¿Debería desplegarse?**

No como sistema autónomo — un 5% de error en medicina puede ser fatal. Sí como herramienta de apoyo bajo supervisión médica, con disclaimers claros y fuentes verificadas.
