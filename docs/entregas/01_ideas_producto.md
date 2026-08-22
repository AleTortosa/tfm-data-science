# Entrega 1 — Propuesta inicial de ideas de producto

## Idea 1 — ParkingPredict

### Problema que resuelve

En Valencia, especialmente en las zonas más densas del centro urbano, encontrar aparcamiento es un problema cotidiano: los conductores dan vueltas durante minutos (a veces bastantes) buscando plaza libre, lo que genera pérdida de tiempo, más congestión de tráfico y mayores emisiones. Actualmente el ayuntamiento ofrece cierta información en tiempo real (a través de la AppValència, el geoportal y València al minut) sobre algunas plazas sensorizadas, pero esa información es puramente descriptiva del estado actual: no anticipa dónde va a haber sitio libre dentro de un rato.

Esto no es solo un ejercicio técnico: se trata de una decisión que muchas personas toman varias veces al día (a qué zona dirigirse para aparcar) y que hoy se resuelve de forma ineficiente, por prueba y error. Un modelo que prediga la disponibilidad de aparcamiento en distintas zonas y franjas horarias podría convertir esa decisión en algo informado.

### Motivación para realizar el proyecto

- Relevancia urbana y medioambiental: menos tiempo buscando aparcamiento significa menos tráfico innecesario y menos emisiones en el centro de la ciudad.
- Interés técnico: permite trabajar series temporales, datos geoespaciales y, si se dispone de suficiente histórico, modelos de predicción a corto plazo.
- Existencia de datos: el Ayuntamiento de Valencia dispone de un portal de datos abiertos (opendata.vlci.valencia.es) con API pública (CKAN) que incluye datasets de ubicación y plazas de aparcamientos ORA, aparcamiento no regulado (cordón y batería) y parkings públicos, además de un proyecto piloto de sensores en plazas reservadas (movilidad reducida, carga y descarga, taxis) con disponibilidad histórica y en tiempo real.

### A quién impacta

- Conductores particulares que se desplazan al centro de Valencia y pierden tiempo buscando aparcamiento.
- Profesionales del transporte y reparto que dependen de plazas de carga y descarga y se ven afectados por su ocupación irregular.
- Personas con movilidad reducida, que a menudo encuentran sus plazas reservadas ocupadas indebidamente.
- El propio Ayuntamiento, que podría usar predicciones para mejorar la gestión y señalización de zonas de aparcamiento.

### Por qué tiene valor

- Reducción del tiempo perdido buscando aparcamiento y, con ello, de la congestión y las emisiones asociadas.
- Mejor planificación de desplazamientos en coche hacia el centro urbano.
- Posible apoyo a la gestión municipal de zonas de aparcamiento regulado.
- Mayor transparencia y mejor experiencia para el ciudadano, al pasar de información puramente descriptiva a información predictiva.

---

## Idea 2 — SupermarketDemand

### Problema que resuelve

Los supermercados de barrio, especialmente en la sección de productos frescos (fruta, verdura, panadería, lácteos de corta caducidad), se enfrentan constantemente a una disyuntiva: pedir de más y generar desperdicio alimentario, o pedir de menos y quedarse sin stock, perdiendo ventas y clientes. Al tratarse de productos perecederos, los errores de previsión tienen un coste directo e inmediato, y muchas tiendas pequeñas todavía deciden el reaprovisionamiento a partir de la intuición del encargado más que de un análisis sistemático de patrones de demanda.

No es un ejercicio técnico aislado: afecta directamente a una decisión operativa diaria (cuánto pedir de cada producto fresco) que tiene impacto económico y medioambiental real.

### Motivación para realizar el proyecto

- Relevancia económica y ambiental: el desperdicio de alimentos frescos tiene un coste económico directo para el comercio y un impacto ambiental evitable.
- Interés profesional: la previsión de demanda es una de las aplicaciones más extendidas de Data Science en retail, con alta transferibilidad a un entorno laboral real.
- Utilidad para un colectivo concreto: pequeños y medianos supermercados de barrio, que no suelen tener herramientas de previsión avanzada como las grandes cadenas.
- Existencia de datos: hay datasets públicos específicamente pensados para este problema, como FreshRetailNet-50K (series temporales horarias de venta de productos perecederos en cientos de tiendas, con eventos de rotura de stock anotados), el dataset de la competición Corporación Favorita Grocery (ventas de miles de artículos por tienda y fecha, con foco explícito en reducir desperdicio) y el dataset de Genpact Food Demand Forecasting (demanda semanal de comida perecedera por centro).

### A quién impacta

- Encargados y propietarios de supermercados de barrio, que gestionan el pedido de productos frescos con recursos limitados.
- Proveedores de productos perecederos, cuya planificación de producción depende de la demanda real de sus clientes.
- Consumidores del barrio, que se benefician de mayor disponibilidad de producto fresco y, potencialmente, de mejores precios al reducirse las pérdidas por desperdicio.

### Por qué tiene valor

- Reducción del desperdicio alimentario y de su coste económico y ambiental asociado.
- Mejora en la disponibilidad de producto fresco para el cliente, evitando roturas de stock.
- Mejor toma de decisiones en el pedido diario o semanal, menos dependiente de la intuición.
- Posible generación de alertas o recomendaciones automáticas de reaprovisionamiento.

---

## Idea 3 — GamePredict

### Problema que resuelve

Los estudios de videojuegos independientes (indies) lanzan sus títulos en plataformas como Steam sin una forma fiable de anticipar cómo va a recibirlos el mercado. A diferencia de los juegos AAA, que cuentan con presupuestos de marketing y una base de jugadores prácticamente garantizada, los indies dependen en gran medida de decisiones como el género, el precio, la fecha de lanzamiento o las primeras reseñas para determinar su éxito, y muchas veces esas decisiones se toman sin ningún tipo de análisis de datos previo.

El problema no es predecir el éxito de cualquier juego (los AAA tienden a tener éxito por su propia naturaleza), sino ayudar a un estudio pequeño a tomar mejores decisiones de lanzamiento cuando cada elección tiene un impacto proporcionalmente mucho mayor en su viabilidad.

### Motivación para realizar el proyecto

- Relevancia empresarial: para un estudio indie, acertar con el posicionamiento del lanzamiento puede ser la diferencia entre poder seguir desarrollando o cerrar.
- Interés profesional y personal: la industria del videojuego combina datos de comportamiento de usuario, texto (reseñas) y variables de producto, lo que permite trabajar distintas técnicas de Data Science.
- Utilidad para un colectivo concreto: estudios y desarrolladores indie, que no cuentan con departamentos de análisis de datos como los grandes publishers.
- Existencia de datos: hay varios datasets públicos de Steam obtenidos vía API oficial o SteamSpy, con miles de juegos y variables como género, precio, fecha de lanzamiento, reseñas y estimación de propietarios (owners). Existen incluso proyectos previos similares que usan estos datos para predecir métricas de éxito en juegos indie, lo que confirma la viabilidad del enfoque.

### A quién impacta

- Estudios y desarrolladores indie que están decidiendo cómo y cuándo lanzar su juego.
- Publishers pequeños que evalúan qué títulos indie apoyar.
- Indirectamente, jugadores, que se beneficiarían de un ecosistema indie más sostenible.

### Por qué tiene valor

- Apoyo a la toma de decisiones de lanzamiento (precio, fecha, género, etiquetas) con base en datos reales del mercado.
- Reducción del riesgo económico para estudios con recursos limitados.
- Identificación de patrones de éxito específicos del segmento indie, distintos a los del mercado AAA.
- Generación de conocimiento útil sobre qué factores influyen realmente en el rendimiento de un juego indie en Steam.

**Nota sobre los datos:** ninguno de los datasets de Steam disponibles incluye cifras de ventas o ingresos reales exactos, ya que Steam no las publica. El "éxito" tendrá que definirse a partir de proxies como número de reseñas, ratio de reseñas positivas o estimación de propietarios (owners), lo cual se explicitará en la definición del problema en fases posteriores.
