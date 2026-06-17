# Hangar — Hosting de bots de Discord

Servicio de hosting de bots de Discord para servidores pequeños/medianos,
con tres formas de entrada: subir bot propio, usar una plantilla predefinida,
o pedir uno a medida.

## Estado actual del proyecto

✅ **Hecho:**
- Landing page completa y funcional visualmente (`hangar-landing.html`)
- Diseño: dark mode con identidad propia (no clon del morado de Discord),
  mockup animado de un canal de Discord con mensajes de ejemplo
- Formulario de alta con selector de tipo de bot (subir propio / 3 plantillas / a medida)
- Lógica de pricing definida (ver más abajo)

❌ **Pendiente (todo, en realidad):**
- Las 3 plantillas de bot (moderación, niveles, economía) — **no existen todavía**,
  solo están mencionadas en la landing como propuesta de valor
- Backend que conecte el formulario con pago real + despliegue del bot
- Infraestructura de despliegue (cómo se levanta un contenedor por cliente)
- Conectar el formulario actual (que solo simula el envío) a un backend real

## Modelo de negocio decidido

- **Precio base: 5€/mes** por bot alojado (plantilla o bot propio del cliente)
- **Bots a medida**: 5€/mes de alojamiento + coste único de preparación de
  15-25€ según complejidad (este rango es orientativo, ajustar con los primeros
  clientes reales antes de fijarlo en piedra)
- Razón de separar el precio: las plantillas y el hosting de bot propio tienen
  coste marginal casi nulo para el dueño del servicio (mismo código reutilizado,
  o código que ya trajo el cliente). Lo "a medida" sí consume tiempo real de
  desarrollo/revisión, así que no debe ir al mismo precio plano que el resto.

## Cómo funcionan las "plantillas" (concepto clave)

Una plantilla es un bot ya programado y probado de antemano (una vez, no por
cliente). Cuando un cliente la elige, no se programa nada nuevo: se despliega
una **copia** de ese mismo código ya existente, en un contenedor propio,
configurada con el token de Discord único de ese cliente.

Detalle técnico importante: cada bot de Discord necesita su propio token y
sus propios permisos en el servidor de Discord del cliente. Por eso, aunque
el código sea idéntico para todos los clientes de una misma plantilla, cada
uno necesita su propia instancia/contenedor corriendo con su token específico
— no se puede compartir una sola instancia entre varios clientes.

### Flujo previsto end-to-end (aún sin construir)

1. Cliente rellena el formulario de la landing, elige tipo de bot
2. Si elige plantilla → paga 5€/mes → el backend copia el código maestro de
   esa plantilla a un contenedor nuevo, le mete el token de Discord del
   cliente como variable de entorno, y lo arranca
3. Si elige "subir bot propio" → el cliente manda su código (o un enlace a
   su repo) → se despliega igual que una plantilla pero con su código en vez
   del maestro
4. Si elige "a medida" → flujo manual/asistido por IA: el dueño del servicio
   genera el código con ayuda de un LLM, lo revisa, y lo despliega como si
   fuera una plantilla nueva de un solo uso

Este patrón de "un contenedor por cliente, desplegado automáticamente al
pagar" es el mismo que ya se construyó para el proyecto hermano **Vigía**
(servicio de monitorización de webs) — se puede reutilizar gran parte de esa
infraestructura (Stripe, base de datos de clientes, lógica de despliegue)
adaptándola de "crear un monitor en Kuma" a "lanzar un contenedor de bot".

## Próximos pasos sugeridos (en orden)

1. **Programar la primera plantilla** (recomendado empezar por moderación,
   es la más demandada y la más sencilla de validar). Generar el código base
   con ayuda de IA, probarlo en un servidor de Discord de pruebas propio,
   iterar hasta que funcione bien.
2. Repetir para niveles y economía.
3. Montar la infraestructura de despliegue por contenedor (adaptando el
   patrón de Vigía: Docker + backend que automatiza alta/baja).
4. Conectar el formulario de la landing al backend real (Stripe + lógica de
   despliegue), igual que se hizo con Vigía.
5. Definir el proceso para "a medida": qué información mínima pedir al
   cliente, cómo se genera el código con IA, qué revisión manual mínima se
   hace antes de desplegar (importante: no desplegar código generado por IA
   sin revisión, puede tener bugs o fallos de seguridad).

## Archivos de este proyecto

- `hangar-landing.html` — la landing page completa, lista para desplegar
  como está (visualmente terminada, formulario aún sin conectar a backend)

## Contexto de negocio (por si se retoma la decisión)

Este proyecto se planteó como un servicio de bajo esfuerzo de venta (el
objetivo es "dejarlo publicado y que genere algo, sin venta activa
sostenida"). Se descartó automatizar la promoción en foros/grupos por ser
spam y violar normas de la mayoría de comunidades; el plan de distribución
acordado es: publicación puntual manual en 2-3 grupos/foros relevantes +
SEO pasivo en la landing. Expectativas realistas: pocos clientes al
principio, esto es más un experimento de bajo riesgo que un plan de negocio
con ingresos garantizados.
