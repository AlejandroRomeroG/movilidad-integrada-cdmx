<div align="center">
  <h1>Red de Movilidad Integrada de la CDMX</h1>
  <p><strong>Una visualización interactiva que conecta el mapa esquemático con la geografía real de la red.</strong></p>
  <p>
    <a href="https://github.com/AlejandroRomeroG/movilidad-integrada-cdmx/actions/workflows/pages/pages-build-deployment"><img alt="GitHub Pages" src="https://github.com/AlejandroRomeroG/movilidad-integrada-cdmx/actions/workflows/pages/pages-build-deployment/badge.svg"></a>
    <a href="https://alejandroromerog.github.io/movilidad-integrada-cdmx/"><img alt="Abrir mapa" src="https://img.shields.io/badge/mapa-abrir-00A87A?style=flat-square"></a>
    <img alt="23 líneas" src="https://img.shields.io/badge/l%C3%ADneas-23-356FD1?style=flat-square">
    <img alt="4 sistemas" src="https://img.shields.io/badge/sistemas-4-C72C8E?style=flat-square">
  </p>
  <p>
    <a href="#cobertura">Cobertura</a> ·
    <a href="#fuentes-y-metodología">Fuentes y metodología</a> ·
    <a href="#inicio-rápido">Inicio rápido</a> ·
    <a href="#arquitectura">Arquitectura</a>
  </p>
</div>

El proyecto representa las líneas del Metro, Metrobús, Cablebús y Tren Suburbano en un mismo espacio visual. En lugar de presentar dos mapas separados, interpola continuamente cada estación y cada trazo entre el esquema de Movilidad Integrada y su posición geográfica. Así permite observar qué conserva el diseño diagramático, qué simplifica y cómo se relaciona la red con el territorio metropolitano.

**Sitio público:** [alejandroromerog.github.io/movilidad-integrada-cdmx](https://alejandroromerog.github.io/movilidad-integrada-cdmx/)

<a href="https://alejandroromerog.github.io/movilidad-integrada-cdmx/">
  <img src=".github/assets/mapa-esquematico.png" alt="Vista del mapa esquemático de la Movilidad Integrada de la CDMX" width="100%">
</a>

## Cobertura

| Sistema | Líneas | Cobertura representada |
| --- | ---: | --- |
| **Metro** | 12 | Líneas 1–9, 12, A y B |
| **Metrobús** | 7 | Líneas 1–7 |
| **Cablebús** | 3 | Líneas 1–3 |
| **Tren Suburbano** | 1 | Buenavista–Cuautitlán y ramal al AIFA |
| **Total** | **23** | **4 sistemas** |

## Controles

| Acción | Escritorio | Dispositivo táctil |
| --- | --- | --- |
| Desplazar el mapa | Arrastrar | Arrastrar con un dedo |
| Acercar o alejar | Rueda, doble clic o botones `−` / `+` | Pellizcar o usar los botones `−` / `+` |
| Restablecer la vista | Botón `↻` | Botón `↻` |
| Consultar una estación | Pasar el puntero o hacer clic sobre el nodo | Tocar el nodo |
| Recorrer nodos coincidentes | Repetir el clic en el mismo punto | Repetir el toque en el mismo punto |
| Cambiar de representación | Mover el control **Esquema–Geografía** | Mover el control **Esquema–Geografía** |

## Fuentes y metodología

| Componente | Fuente o tratamiento |
| --- | --- |
| **Geometría esquemática** | [Conversión SVG usada como referencia](referencias/mapa-MI_STC-2025.svg), derivada del [Mapa de Movilidad Integrada STC 2025](https://www.semovi.cdmx.gob.mx/storage/app/media/MI%20MAPA/Mapa%20MI_STC%202025.pdf) publicado por la Secretaría de Movilidad de la Ciudad de México. |
| **Rutas y estaciones** | [GTFS estático de la Ciudad de México](https://datos.cdmx.gob.mx/dataset/gtfs), con corte al 24 de febrero de 2026. |
| **Ramal al AIFA** | Información pública de conectividad del [Tren Felipe Ángeles](https://www.aifa.aero/conectividad/tren). |
| **Límites administrativos** | [Marco Geoestadístico](https://www.inegi.org.mx/temas/mg/) del INEGI para el contexto territorial. |

### Referencia del esquema

El archivo [`referencias/mapa-MI_STC-2025.svg`](referencias/mapa-MI_STC-2025.svg) conserva la versión vectorial empleada para estudiar el mapa oficial y hacer trazable el origen del diseño. No se utilizó únicamente como referencia visual: su geometría sirvió como guía para construir las posiciones del modo **Esquema**.

El proceso siguió cuatro pasos:

1. Se identificaron los trazos por sistema, línea, color y anchura para separar sus ejes vectoriales.
2. Se normalizaron las transformaciones del SVG en un sistema de coordenadas común, conservando quiebres, direcciones, terminales y la relación espacial entre líneas.
3. Se asociaron las estaciones con la guía de su línea y se mantuvo su orden topológico desde la terminal inicial hasta la final.
4. Se reparametrizó cada recorrido por longitud acumulada para distribuir sus estaciones de forma equidistante, fijar las terminales en los extremos y sincronizar los nodos de correspondencia.

Por ello, el esquema actual está inspirado directamente en el layout publicado por el gobierno de la CDMX, pero es una reconstrucción adaptada a la interpolación: regulariza distancias y reacomoda distintivos para conservar legibilidad durante la transición hacia la geografía.

Cada estación conserva dos coordenadas: una extraída o reconstruida a partir del esquema oficial y otra derivada de su ubicación geográfica. El deslizador interpola ambas posiciones y actualiza las rutas en SVG.

Los datos, geometrías e iconos necesarios para la experiencia pública están incorporados en el documento HTML. El navegador no necesita solicitar una API durante la interacción.

## Inicio rápido

No hay dependencias de ejecución ni proceso de compilación. Basta con servir los archivos estáticos desde un servidor local:

```bash
git clone https://github.com/AlejandroRomeroG/movilidad-integrada-cdmx.git
cd movilidad-integrada-cdmx
python3 -m http.server 8000
```

Después abre [http://localhost:8000](http://localhost:8000) en el navegador.

## Arquitectura

| Ruta | Responsabilidad |
| --- | --- |
| `index.html` | Aplicación autocontenida: estructura, estilos, lógica, datos, geometrías e iconos. |
| `.github/assets/mapa-esquematico.png` | Vista previa utilizada por este README. |
| `referencias/mapa-MI_STC-2025.svg` | Conversión vectorial del mapa oficial utilizada para reconstruir la geometría esquemática. |
| `.nojekyll` | Indica a GitHub Pages que publique los archivos sin procesamiento de Jekyll. |
| `README.md` | Documentación, fuentes y guía de uso local. |

La aplicación usa HTML, CSS y JavaScript nativos. El mapa se renderiza con SVG y las interacciones unifican mouse, lápiz y tacto mediante Pointer Events. No emplea frameworks ni dependencias de ejecución.

## Privacidad y accesibilidad

- La aplicación no incorpora formularios, analítica, publicidad ni rastreadores propios.
- La única preferencia persistente es el tema visual, guardado localmente en el navegador.
- Los controles incluyen nombres accesibles y estados anunciables para tecnologías de asistencia.
- Las animaciones respetan `prefers-reduced-motion`.
- El diseño considera áreas seguras, orientación vertical y horizontal y distintos tamaños de pantalla.

## Alcance y límites

- La cercanía o longitud de un tramo en el esquema no representa distancia ni tiempo de viaje.
- La vista geográfica sirve para comparar la red con el territorio; no es un planificador de rutas.
- El proyecto no está afiliado con el Gobierno de la Ciudad de México, SEMOVI, STC Metro, Metrobús, Cablebús, Ferrocarriles Suburbanos o AIFA.

## Autor

Creado y mantenido por **Alejandro Romero González**.

- Sitio personal: [alejandroromerog.github.io](https://alejandroromerog.github.io/)
- GitHub: [@AlejandroRomeroG](https://github.com/AlejandroRomeroG)
