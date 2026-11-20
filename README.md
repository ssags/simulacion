# simulacion# Refrigeración líquida de una PC: continuidad y Bernoulli

Simulación 3D interactiva, en un solo archivo HTML, de un circuito de refrigeración líquida de una PC. Sirve para ver y entender dos ideas de hidrodinámica de fluidos ideales: la **ecuación de continuidad** y la **ecuación de Bernoulli**, junto con los **manómetros** que miden la diferencia de presión.

El circuito es cerrado y está dentro de un gabinete con placa madre, RAM, placa de video, bomba, placa fría sobre la CPU, radiador con ventiladores y reservorio. Al mover los controles cambian en tiempo real las velocidades, las presiones y las flechas de la escena.

## Cómo usarlo

1. Abrí el archivo HTML en un navegador moderno con WebGL (Chrome, Firefox, Edge o Safari).
2. No hace falta instalar nada ni usar un servidor: se abre con doble clic.
3. Se necesita conexión a internet la primera vez, porque carga dos recursos externos:
   - **Three.js r128** desde `cdnjs.cloudflare.com` (el motor 3D).
   - La tipografía **Instrument Sans** desde Google Fonts. Si no carga, usa la fuente del sistema.
4. La otra alternativa es hacer click en [Ver la simulación](https://ssags.github.io/simulacion-refrigeracion/)

## Qué se ve en la escena

| Elemento | Qué representa |
| --- | --- |
| Tubo (A₁, v₁) | Sección grande y velocidad chica. Flecha corta y gruesa, color turquesa. |
| Placa fría + CPU (A₂, v₂) | 4 microcanales; la suma de sus áreas es menor que A₁, por eso el agua va más rápida. Flecha larga, color naranja. |
| Radiador (A₃ = 3·A₁) | Sección mayor, el agua casi se frena (v₃ = v₁/3). |
| Bomba | Da el caudal Q. Es el único lugar donde se agrega energía al fluido. |
| Reservorio | Almacena agua; no participa del cálculo. |
| Dos manómetros | Columnas de agua graduadas (1 división = 1 cm de agua). Miden P₁ (tubo) y P₂ (placa). La diferencia es Δh. |

El color y el largo de las flechas y de las partículas indican la velocidad: **turquesa = lento, naranja = rápido**. La leyenda «Cómo leerlo» (arriba a la derecha de la escena) se despliega con un clic.

## Controles

### Sliders

| Control | Rango | Valor inicial |
| --- | --- | --- |
| Caudal de la bomba | 0,5 a 3 L/min (paso 0,1) | 2 L/min |
| Diámetro interior del tubo | 6 a 16 mm (paso 1) | 10 mm |
| Sección de la placa fría (% del tubo) | 20 a 100 % (paso 5) | 30 % |

### Botones

- **Fluido ideal / Con pérdidas reales:** cambia entre Bernoulli puro y un modelo con pérdidas.
- **Pausar / Reanudar:** congela el movimiento de partículas, ventiladores y bomba.
- **Resaltar circuito:** vuelve casi transparentes los componentes para ver solo el circuito.
- **Nombres de piezas:** muestra u oculta las etiquetas.
- **Giro automático:** rota la cámara sola; se desactiva al arrastrar o elegir una vista.
- **Vista frontal:** permite leer Δh sin distorsión de perspectiva.
- **Vista superior:** vista desde arriba.

### Cámara

- **Arrastrar:** rotar.
- **Rueda del mouse:** acercar o alejar.
- **Flechas del teclado** (con la escena enfocada): girar.

## Paneles de información

- **Continuidad:** A₁v₁ = A₂v₂ = Q, con las velocidades en el tubo, la placa y el radiador, y el número de Reynolds en el tubo (laminar o turbulento).
- **Bernoulli (tubo → placa, misma altura):** P₁ + ½ρv₁² = P₂ + ½ρv₂², con la caída de presión P₁ − P₂ y el descenso equivalente (P₁ − P₂)/ρg.
- **Qué agrega la realidad:** pérdida de presión total del circuito y potencia hidráulica de la bomba (ΔP·Q). En modo ideal ambas valen 0.
- **Resolución paso a paso (con tus valores):** el desarrollo de la cuenta (datos, ecuación, reemplazo y resultado con unidades) que se actualiza solo con los controles. Sirve para copiarlo en el pizarrón.
- **Hipótesis de Bernoulli:** fluido incompresible, flujo estacionario, sin viscosidad y sin trabajo externo entre los puntos.

Las etiquetas sobre la escena muestran en vivo A₁, v₁, A₂, v₂, A₃, v₃, P₂ = P₁ − ΔP y Δh.

## Modelo físico

Es un **modelo didáctico**, no una simulación de dinámica de fluidos (CFD).

- Agua a 25 °C: ρ = 997 kg/m³, μ = 0,89 mPa·s, g = 9,81 m/s².
- Continuidad: v₁ = Q/A₁, v₂ = v₁/(fracción de sección de la placa), v₃ = v₁/3.
- Reynolds: Re = ρ·v₁·D/μ. Régimen laminar por debajo de 2300 y turbulento por encima.
- **Modo ideal:** P₁ − P₂ = ½ρ(v₂² − v₁²). No hay pérdidas, y la potencia de la bomba se muestra como 0.
- **Modo con pérdidas reales:**
  - Se suma a la caída de presión una pérdida local de entrada a la placa: ½·(½ρv₂²).
  - La pérdida total usa Darcy-Weisbach para 1,2 m de tubo (fricción de Blasius, f = 0,316/Re^0,25, en régimen turbulento; f = 64/Re en laminar) más coeficientes K estimados: **1,5** para la placa y **5** para el radiador.
  - Potencia hidráulica de la bomba = pérdida total × Q.
- El radiador se modela con una sección total 3 veces la del tubo (varios canales en paralelo).
- Los manómetros tienen un tope de escala: si Δh se sale, la etiqueta lo avisa con «(fuera de escala)».


## Personalizar

Los parámetros están al inicio del bloque `<script>` y en las líneas de `update()`:

| Qué cambiar | Dónde |
| --- | --- |
| Densidad, viscosidad, g, largo de tubo | `RHO`, `MU`, `G`, `LT` (primeras líneas del script) |
| Rangos y valores iniciales de los sliders | Atributos `min`, `max`, `step` y `value` de los `<input id="q">`, `<input id="d">` e `<input id="s">` |
| Coeficientes K de pérdidas (1,5 y 5) | Línea `loss=` dentro de `update()` |
| Relación de sección del radiador (3·A₁) | `v3=v1/3` y la etiqueta A₃ en `update()` |
| Colores | Variables CSS en `:root` (`--acc` turquesa, `--hot` naranja) |
| Velocidad de las partículas | Constante `VIS` |

## Estructura del archivo

- `<style>`: tema oscuro y diseño responsive (una columna en pantallas angostas).
- HTML: escena (`#scene`), panel de controles y paneles de resultados.
- `<script>`: construye la escena con Three.js, calcula la física en `update()` y anima en el bucle `frame()`.

## Referencias

- Sears-Zemansky, *Física universitaria* (mecánica de fluidos).
- Çengel y Cimbala, *Mecánica de fluidos*.
- White, *Fluid Mechanics*.