# Simulador de Lazo Cerrado Discreto para Streaming Adaptativo (ABR)

Este repositorio contiene el entorno interactivo desarrollado para la validación empírica y defensa del Trabajo Final de la asignatura **Automatización y Control Discreto** de la **Universidad Tecnológica Nacional - Facultad Regional Buenos Aires (UTN-FRBA)**.

El sistema modela en tiempo real la dinámica de un algoritmo de tasa de bits adaptable (*Adaptive Bitrate Streaming*), aplicando los conceptos de la teoría de control clásica digital (Tiempo Discreto).

---

## 🛠️ Fundamentación de Control y Especificaciones

El software implementa un lazo cerrado cerrado con las siguientes características técnicas extraídas del informe del TP:
1. **Planta (Proceso):** Modelada como un integrador puro con cuantización temporal. Actualiza el buffer en cada ciclo considerando el tamaño del fragmento ($T = 4\text{ s}$), el *Bitrate* solicitado y la capacidad real de la red (*Throughput*).
2. **Señal de Error:** $e(k) = 10\text{ s} - \text{Buffer Health}(k)$. El *Set Point* de referencia es de 10 segundos.
3. **Controlador PD Digital (Sin componente Integral):** 
   * Si $|e(k)| > \text{Zona Muerta}$, ejecuta:  
     $$u(k) = K_p \cdot e(k) + \frac{K_d \cdot (e(k) - e(k-1))}{T}$$
   * Valores por defecto sintonizados: $K_p = 0.60$ y $K_d = 0.30$.
4. **Banda de Aceptación (Zona Muerta):** Configurada por defecto en $\pm2\text{ s}$ (Rango: 8 a 12 segundos). Dentro de este umbral, el error se procesa como $0$ para mitigar las oscilaciones continuas de calidad (*efecto zapateo*).
5. **Actuador Cuantizado:** Mapea la señal continua ideal al perfil comercial comercial disponible inmediatamente inferior: `[0.4 Mbps (240p), 1.0 Mbps (480p), 2.5 Mbps (720p), 8.0 Mbps (1080p)]`.

---

## 📊 Guía de Lectura de los Gráficos

Los 4 gráficos se actualizan segundo a segundo y están perfectamente alineados en el eje horizontal del tiempo para facilitar el análisis correlativo:

1. **Gráfico 1 · Buffer Health [s]:** Muestra la evolución del nivel del buffer (línea azul) respecto al *Set Point* de 10s (línea roja punteada). La franja sombreada amarilla representa la **Zona Muerta**. *Nota: Su característica forma de diente de sierra se debe a la inyección instantánea de +4 segundos de video al finalizar cada descarga frente al consumo continuo de 1 s/s del reproductor.*
2. **Gráfico 2 · Velocidades [Mbps]:** Contrapone el *Throughput* real del canal de red (línea verde punteada) frente al *Bitrate* discreto seleccionado por el actuador (línea naranja escalonada).
3. **Gráfico 3 · Señal de Control $u(k)$ [Mbps]:** Visualiza la conversión matemática del lazo. La línea violeta rígida representa la acción pura del controlador $u(k)$ calculada cada 4 segundos. La línea fina punteada gris representa el **Bitrate Ideal continuo** ($Throughput - u(k)$) antes de ser recortado por los escalones fijos del actuador.
4. **Gráfico 4 · Señal de Error $e(k)$ [s]:** Muestra el desvío instantáneo respecto al objetivo. Es el reflejo inverso y lineal del gráfico de Buffer.

---

## 📂 Estructura del Archivo
La simulación está completamente integrada en el archivo **`index.html`** (o `simulador_abr.html`). Al ser un único archivo autónomo que contiene la estructura HTML5, los estilos visuales CSS y la lógica en JavaScript, **no requiere instalar dependencias, servidores locales ni compiladores**. 

*Para ejecutarlo, simplemente realice un doble clic sobre el archivo desde cualquier navegador convencional o acceda a la versión web desplegada en la barra superior de este repositorio.*
