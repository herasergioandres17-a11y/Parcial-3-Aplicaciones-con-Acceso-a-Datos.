# Sistema de Inventario de Zapatos — SQLAlchemy ORM

## 1. Información del proyecto

| Información | Descripción |
|---|---|
| Estudiante | [Sergio Andres Salgado Hera] |
| Asignatura | [Desarrollo de Aplicaciones con Acceso a Datos] |
| Docente | [Eduardo Pimienta] |
| Fecha | [17/11/2026] |
| Lenguaje de programación | Python |
| ORM utilizado | SQLAlchemy |
| Base de datos | SQLite |

## 2. Descripción del proyecto

**¿En qué consiste el programa?**

El programa es una aplicacion de consola desarrollada en Python para gestionar el inventario de una pequeña tienda de zapatos. Proposito principal es brindar persistencia mediante el ORM SQLAlchemy y una base de datos relacional SQLite (´zapateria.db´), permitiendo almacenar permanentemente la información de cada referencia de calzado sin perder los registros al cerrar la aplicacion.

**¿Qué funcionalidades tiene?**

Describe brevemente las operaciones que permite realizar el sistema:

- Registrar zapatos: Agrega nuevos modelos de calzado con su talla y precio asignandoles un ID automatico.
- Consultar el inventario:Lista todos los zapatos almacenados actualmente en la base de datos.
- Buscar zapatos por ID: Permite localizar un registro especifico ingresando su clave primaria.
- Actualizar precios: Modifica el valor de venta de un zapato existente en el inventario.
- Eliminar zapatos:Borra permanentemente un registro del catalogo mediante su ID.

## 3. Estructura del proyecto

Presenta la estructura de archivos de tu aplicación.

```text
tienda_zapatos/
├── main.py
├── datos.py
├── requirements.txt
├── README.md
└── evidencias/
```

**Explicación de los archivos:**

| Archivo | ¿Cuál es su responsabilidad? |
|---|---|
| `main.py` | [Contiene la interfaz de usuario por consola, el menu interactivo, la captura de datos mediante teclado y la invocacion de funciones con persistencia.] |
| `datos.py` | [Implementa la capa de acceso a datos con SQLAlchemy: configura la conexion(engine), define el modelo ´Zapato´, crea las tablas y ejecuta las operaciones CRUD.] |
| `requirements.txt` | [Especifica las librerias externas necesarias para ejecutar el proyecto (principalmente SQLAlchemy).] |
| `zapateria.db` | [Archivo SQLite con base de datos generado automaticamente en carpeta raiz al ejecutar ´main.py´ por primera vez mediante la funcion ´inicializar_bd()´ ] |

## 4. Instalación y ejecución

Explica los pasos para ejecutar el proyecto en otro computador.

**Paso 1. Requisitos**

[.Tener instalado Python 3.10 o superior.
.Tener disponible el gestor de paquetes pip.
.Contar con una terminal o consola de comandos.]

**Paso 2. Instalación de dependencias**

```bash
python -m pip install -r requirements.txt
```

**Paso 3. Ejecución**

```bash
python main.py
```

**Paso 4. Resultado esperado**

[Al ejecutar el comando, se crea automaticamente el archivo´zapateria.db´ junto con la tabla ´zapatos´, y se despliega en consola el menu principal con las opciones de: registro, lista, busqueda, actualizacion, eliminacion y salir del sistema.]

---

# 5. Documentación técnica — Preguntas obligatorias

Responde las siguientes preguntas utilizando tu implementación. Cuando sea necesario, incluye fragmentos de tu código para explicar el funcionamiento.

### Pregunta 1. Modelo y tabla

¿Qué clase de Python representa la tabla de la base de datos? Explica cómo SQLAlchemy identifica el nombre de la tabla y sus columnas.

**Respuesta:**

[La tabla de la base de datos está representada por la clase ´Zapato´, la cual hereda de ´Base´. SQLAlchemy identifica el nombre de la tabla física mediante el atributo de clase especial ´__tablename__ = "zapatos"´.Por su parte, las columnas y sus tipos de datos se definen declarando atributos anotados con ´Mapped[tipo]´ y configurados mediante ´mapped_column()´, indicando restricciones como la clave primaria (primary_key=True).  ]

**Fragmento de código relacionado:**

[class Zapato(Base):
    __tablename__ = "zapatos"

    id: Mapped[int] = mapped_column(primary_key=True, autoincrement=True)
    modelo: Mapped[str] = mapped_column(String(100), nullable=False)
    talla: Mapped[int] = mapped_column(nullable=False)
    precio: Mapped[float] = mapped_column(nullable=False)
    En este bloque, ´__tablename__´ vincula la clase con la tabla ´zapatos´, mientras que cada ´Mapped´ traduce los tipos nativos de Python (int, str, float) a columnas SQL (INTEGER, VARCHAR, FLOAT).
    ]

### Pregunta 2. Creación de la base de datos

¿Qué ocurre cuando ejecutas el programa por primera vez? Explica cómo se crea la base de datos `zapateria.db` y la tabla `zapatos`.

**Respuesta:**

[Al ejecutar el programa por primera vez, SQLAlchemy evalúa la cadena de conexión definida en ´create_engine("sqlite:///zapateria.db")´y crea físicamente el archivo ´zapateria.db´en el directorio del proyecto si aún no existe. Acto seguido, al llamarse la función ´inicializar_bd()´, el método´Base.metadata.create_all(engine)´nspecciona todos los modelos registrados que heredan de ´Base´(en este caso Zapato) y ejecuta internamente la sentencia DDL (CREATE TABLE IF NOT EXISTS zapatos ...) para estructurar la tabla.]

**Fragmento de código relacionado:**

[engine = create_engine("sqlite:///zapateria.db", echo=False)

def inicializar_bd():
    Base.metadata.create_all(engine).]
engine gestiona el enlace con el archivo SQLite y Base.metadata.create_all(engine) materializa el esquema de la tabla en dicha base de datos.
### Pregunta 3. Registro de zapatos

Cuando seleccionas la opción de registrar un zapato, ¿qué recorrido siguen los datos desde el menú hasta quedar almacenados en SQLite? Identifica las funciones utilizadas.

**Respuesta:**

[1.En main.py, el usuario selecciona la opción de registrar e ingresa por teclado el modelo, la talla y el precio.   
2.main.py invoca la función crear_zapato(modelo, talla, precio) definida en datos.py
3.Dentro de crear_zapato(), se instancia un objeto de la clase Zapato con los valores recibidos.
4.   Se abre una sesión de trabajo (with Session(engine) as session:), se añade el objeto al área de preparación mediante session.add(nuevo_zapato) y finalmente se ejecuta session.commit(), momento en el cual SQLAlchemy traduce el objeto a una sentencia INSERT y lo guarda físicamente en zapateria.db.]

**Fragmento de código relacionado:**

[def crear_zapato(modelo, talla, precio):
    with Session(engine) as session:
        nuevo_zapato = Zapato(modelo=modelo, talla=talla, precio=precio)
        session.add(nuevo_zapato)
        session.commit()
        return True
        La función encapsula la creación de la instancia Python, su inserción en la sesión y la confirmación definitiva en la base de datos.]

### Pregunta 4. Sesiones y transacciones

¿Para qué se utiliza una sesión de SQLAlchemy? ¿Qué ocurriría si agregas o modificas un objeto, pero no confirmas la transacción con `commit()`?

**Respuesta:**

[Una sesión (Session) actúa como un intermediario o zona de trabajo temporal entre los objetos en memoria de Python y la base de datos relacional. Gestiona el ciclo de vida de los objetos cargados o creados y agrupa las operaciones dentro de una transacción. Si se agrega (session.add()) o modifica un objeto pero no se ejecuta session.commit(), los cambios permanecen únicamente en la memoria temporal de la sesión y nunca se escriben en el archivo zapateria.db; al cerrarse el bloque with, la transacción se revierte (rollback) automáticamente y los datos se pierden..]

### Pregunta 5. Búsqueda por ID

¿Cómo funciona la búsqueda de zapatos por ID? Explica qué devuelve tu función cuando encuentra el registro y qué devuelve cuando no existe.

**Respuesta:**

[La búsqueda por ID utiliza el método session.get(Zapato, id_zapato) de SQLAlchemy, el cual consulta directamente por la clave primaria (id) de la entidad Zapato. Si existe una fila con ese identificador en la tabla zapatos, SQLAlchemy reconstruye y devuelve la instancia del objeto Zapato con todos sus atributos cargados; si no encuentra ninguna coincidencia, devuelve None.]

**Fragmento de código relacionado:**

[def buscar_zapato(id_zapato):
    with Session(engine) as session:
        return session.get(Zapato, id_zapato)]
 session.get() optimiza la consulta por clave primaria y retorna el objeto mapeado o None en caso de ausencia.
### Pregunta 6. Actualización de precios

¿Cómo se actualiza el precio de un zapato mediante el ORM? Explica cómo se obtiene el objeto, cómo se modifica su atributo y cómo se guarda el cambio.

**Respuesta:**

[Para actualizar el precio, primero se recupera el registro mediante session.get(Zapato, id_zapato). Si el objeto existe (es distinto de None), se reasigna directamente su propiedad en Python (zapato.precio = nuevo_precio). SQLAlchemy detecta automáticamente que el atributo del objeto rastreado ha cambiado ("dirty state") y, al llamar a session.commit(), genera y ejecuta la sentencia UPDATE correspondiente en SQLite, devolviendo True (o False si el ID no existía).]

**Fragmento de código relacionado:**

[def actualizar_precio(id_zapato, nuevo_precio):
    with Session(engine) as session:
        zapato = session.get(Zapato, id_zapato)
        if zapato is not None:
            zapato.precio = nuevo_precio
            session.commit()
            return True
        return False
        Aquí se evidencia el paradigma ORM: modificar un registro en la base de datos equivale a modificar el atributo de un objeto en memoria y confirmar la sesión.]

### Pregunta 7. Eliminación de registros

¿Qué ocurre cuando eliminas un zapato? Explica cómo compruebas que el registro ya no existe en la base de datos.

**Respuesta:**

[Al llamar a eliminar_zapato(id_zapato), primero se localiza el objeto con session.get(Zapato, id_zapato). Si existe, se marca para borrado mediante session.delete(zapato) y se confirma con session.commit(), lo que ejecuta la sentencia DELETE en SQLite. Para comprobar que ya no existe, se puede volver a ejecutar la opción de listar inventario o buscar nuevamente por ese mismo ID, verificando que el sistema informa que el registro no fue encontrado (None)]

**Fragmento de código relacionado:**

[def eliminar_zapato(id_zapato):
    with Session(engine) as session:
        zapato = session.get(Zapato, id_zapato)
        if zapato is not None:
            session.delete(zapato)
            session.commit()
            return True
        return False
        El método session.delete() instruye al ORM para remover la fila vinculada al objeto en cuanto se confirme la transacción.]

### Pregunta 8. Persistencia de datos

¿Qué sucede con los zapatos registrados cuando cierras y vuelves a ejecutar el programa? Explica por qué permanecen almacenados.

**Respuesta:**

[Al cerrar el programa y volver a ejecutarlo, todos los zapatos previamente registrados (junto con sus actualizaciones o eliminaciones) permanecen intactos. Esto ocurre porque los datos no se almacenan en variables volátiles en la memoria RAM del programa, sino que fueron confirmados mediante commit() dentro del archivo binario zapateria.db en el disco duro, el cual es leído nuevamente por SQLite cada vez que se inicia la aplicación.]

---

## 6. Pruebas de funcionamiento y evidencias

Debes ejecutar el programa y demostrar el funcionamiento de cada una de las operaciones.

### Prueba 1. Registrar zapatos

- **Acción realizada:** registrar dos zapatos.
- **Datos utilizados:** [Zapato1:Modelo: Nike Air Force 1, Talla:42 y Precio:450000
Zapato2: Modelo:Adidas Ultraboost, Talla:40, Precio: 520000].
- **Resultado obtenido:** [El sistema confirmo el registro exitoso de ambos zapatos asiganandoles automaticamente los IDs 1 y 2].

**Evidencia:**

![Registro de zapatos](evidencias/registro.png)

### Prueba 2. Listar inventario

- **Acción realizada:** consultar todos los zapatos.
- **Resultado obtenido:** [El programa imprime los articulos anteriormente registrados].

**Evidencia:**

![Listado de zapatos](evidencias/listado.png)

### Prueba 3. Buscar por ID

- **ID utilizado:** [Indicar ID].
- **Resultado obtenido:** [El programa imprime el articulo segun el ID que se le asigno y fue digitado en la Terminal].

**Evidencia:**

![Búsqueda de zapatos](evidencias/busqueda.png)

### Prueba 4. Actualizar precio

- **Zapato seleccionado:** [Adidas Ultraboost].
- **Precio anterior:** [520000].
- **Precio nuevo:** [40000].
- **Resultado obtenido:** [El precio del articulo fue cambiado en la base de datos].

**Evidencia:**

![Actualización de precio](evidencias/actualizacion.png)

### Prueba 5. Eliminar zapato

- **ID eliminado:** [1].
- **Resultado obtenido:** [El articulo fue eliminado de la base de datos].

**Evidencia:**

![Eliminación de zapato](evidencias/eliminacion.png)

### Prueba 6. Comprobar persistencia

- **Acción realizada:** cerrar y ejecutar nuevamente el programa.
- **Resultado obtenido:** [Los articulos ingresados en la primera ejecucion, se mantuvieron enlistados con su mismo ID y precio ].

**Evidencia:**

![Persistencia de datos](evidencias/persistencia.png)

### Pruebas con registros inexistentes

Ejecuta las operaciones de buscar, actualizar y eliminar utilizando un ID que no exista.

| Operación | ¿Qué sucedió? |
|---|---|
| Buscar ID inexistente | [El Articulo no fue encontrado] |
| Actualizar ID inexistente | [El Articulo no fue encontrado] |
| Eliminar ID inexistente | [El Articulo no fue encontrado] |

## 7. Conclusiones

Responde brevemente:

1. ¿Qué aprendiste al implementar las operaciones CRUD utilizando SQLAlchemy?
Aprendí a estructurar la capa de persistencia utilizando un ORM, comprendiendo cómo un objeto de Python se mapea y se sincroniza automáticamente con una tabla relacional en SQLite mediante sesiones y transacciones
2. ¿Qué diferencia identificaste entre trabajar con objetos Python y escribir sentencias SQL directamente?
Con los objetos Python trabajas con la lógica orientada a objetos de forma intuitiva, mientras que el SQL tradicional requiere redactar cadenas de texto con instrucciones estructuradas , lo que hace al ORM mucho más limpio y fácil de mantener.
3. ¿Cuál fue la principal dificultad durante el desarrollo y cómo la solucionaste?
La principal dificultad estuvo en ajustar la definición de las columnas con la nueva sintaxis declarativa de SQLAlchemy 2.0 y asegurar que el tipado con Mapped reconociera correctamente los tipos de datos nativos; se solucionó especificando explícitamente las clases de SQL (Integer, String, Float) en los mapeos

## Indicaciones de documentación

- Responde las ocho preguntas con tus propias palabras.
- Relaciona tus explicaciones con el código que implementaste.
- Incluye fragmentos de código únicamente cuando ayuden a explicar tu respuesta.
- Todas las evidencias deben corresponder a ejecuciones reales del programa.
- Guarda las capturas dentro de la carpeta `evidencias/` utilizando los nombres indicados o actualiza las rutas del README.
- Verifica que las imágenes se visualicen correctamente en GitHub.
- Elimina los textos entre corchetes y reemplázalos con tus respuestas antes de entregar.
- La documentación debe permitir que otra persona comprenda cómo instalar, ejecutar y utilizar tu aplicación.