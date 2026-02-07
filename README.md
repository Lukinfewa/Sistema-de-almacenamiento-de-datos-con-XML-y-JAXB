# Sistema de almacenamiento de datos con XML y 


# ÍNDICE

1. Conceptos Teóricos
2. Diseño del Proyecto
3. Estructura de Datos y Jerarquía
4. Funcionalidades del Sistema (Librería)
5. Esquema de Validación (XSD)
6. Documento de Datos (XML)
7. Guía de Defensa y Reflexiones

---

## 1. Conceptos Teóricos

### ¿Qué es el XML?
Es el lenguaje donde guardamos los datos. No tiene funciones, solo etiquetas. Funciona como un archivador estructurado con carpetas y subcarpetas para organizar información de forma legible tanto para humanos como para máquinas.

### ¿Qué es el XSD (XML Schema Definition)?
Es el "contrato" o manual de instrucciones. Define qué etiquetas son obligatorias, su orden y el tipo de dato (número, texto, fecha). Sin XSD, el XML es "anárquico"; con XSD, se convierte en una base de datos estructurada y segura.

### ¿Qué es JAXB (Java Architecture for XML Binding)?
Es el puente de comunicación. Mientras el XML es texto plano, Java trabaja con "Objetos". JAXB lee el XSD y genera automáticamente clases Java, permitiendo usar métodos como `getNombre()` en lugar de procesar texto manualmente.

---

## 2. Diseño del Proyecto

**Temática elegida:** Inventario Técnico de Máquinas de Gimnasio.

**Motivación:** Optimizar la gestión de equipamiento deportivo, permitiendo a los gestores de gimnasios clasificar máquinas por su tamaño físico, dificultad de uso y grupos musculares implicados.

**Identificación de Datos:**
- **Datos de Identidad:** Nombre de la máquina, Marca e ID del fabricante.
- **Ficha Técnica:** Dimensiones totales (Largo, Ancho, Alto) y peso de la estructura.
- **Objetivo Muscular:** Lista dinámica de los músculos ejercitados (permite múltiples valores).

---

## 3. Estructura de Datos y Jerarquía

Para un diseño profesional, se ha organizado la información de forma jerárquica en el sistema:

- **Gimnasio (Raíz):** Contenedor global de la base de datos.
- **Máquina (Entidad Principal):** Contiene metadatos como ID, nombre, marca, modelo y categoría.
- **Dimensiones (Sub-entidad):** Agrupa las medidas físicas (ancho, largo, alto) de forma aislada.
- **Configuración de Uso:** Define el nivel de dificultad (restringido de 1 a 3) y el peso.
- **Lista de Músculos:** Colección de elementos repetibles para los grupos musculares.

---

## 4. Funcionalidades del Sistema (Librería Java)

Se han definido 8 métodos clave para la gestión del inventario:

1. **Persistencia total:** Carga (Unmarshalling) y guardado (Marshalling) automático de datos.
2. **Gestión de Inventario:** Altas de nuevas máquinas y bajas mediante ID único.
3. **Recomendación por Volumen Muscular:** Filtrar máquinas que trabajen más de un número "X" de músculos simultáneamente.
4. **Buscador por Nivel:** Localizar máquinas según su dificultad (1: Bajo, 2: Medio, 3: Alto).
5. **Análisis de Rutina:** Conteo de máquinas disponibles para un músculo específico (ej. "Pierna").
6. **Filtro de Espacio:** Localizar máquinas que no superen ciertas medidas de ancho o largo.
7. **Estadística de Fabricante:** Calcular cuántas máquinas pertenecen a una marca concreta (ej. "Technogym").
8. **Modificación de Dificultad:** Método para actualizar el grado de complejidad de una máquina existente.

---

## 5. Esquema de Validación (XSD)

Este archivo define las reglas de integridad de nuestro sistema.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema">

    <!-- Elemento Raíz -->
    <xs:element name="gimnasio">
        <xs:complexType>
            <xs:sequence>
                <xs:element name="maquina" type="tipoMaquina" maxOccurs="unbounded"/>
            </xs:sequence>
        </xs:complexType>
    </xs:element>

    <!-- Entidad Principal -->
    <xs:complexType name="tipoMaquina">
        <xs:sequence>
            <xs:element name="id" type="xs:int"/>
            <xs:element name="nombre" type="xs:string"/>
            <xs:element name="marca" type="xs:string"/>
            <xs:element name="modelo" type="xs:string"/>
            <xs:element name="categoria" type="xs:string"/>
            <xs:element name="dimensiones" type="tipoDimensiones"/>
            <xs:element name="dificultad" type="tipoDificultad"/>
            <xs:element name="listaMusculos" type="tipoMusculos"/>
        </xs:sequence>
    </xs:complexType>

    <!-- Tipos Complejos -->
    <xs:complexType name="tipoDimensiones">
        <xs:sequence>
            <xs:element name="ancho" type="xs:decimal"/>
            <xs:element name="largo" type="xs:decimal"/>
            <xs:element name="alto" type="xs:decimal"/>
        </xs:sequence>
    </xs:complexType>

    <xs:complexType name="tipoMusculos">
        <xs:sequence>
            <xs:element name="musculo" type="xs:string" maxOccurs="unbounded"/>
        </xs:sequence>
    </xs:complexType>

    <!-- Tipo Simple con Restricciones -->
    <xs:simpleType name="tipoDificultad">
        <xs:restriction base="xs:int">
            <xs:minInclusive value="1"/>
            <xs:maxInclusive value="3"/>
        </xs:restriction>
    </xs:simpleType>

</xs:schema>
```

---

## 6. Documento de Datos (XML)

Ejemplo de base de datos poblada con tres perfiles de máquinas (Fuerza, Cardio y Alta Complejidad).

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<gimnasio>
    <maquina>
        <id>1</id>
        <nombre>Prensa de Piernas Pro</nombre>
        <marca>Matrix</marca>
        <modelo>G7-S70</modelo>
        <categoria>Fuerza</categoria>
        <dimensiones>
            <ancho>1.20</ancho>
            <largo>2.10</largo>
            <alto>1.50</alto>
        </dimensiones>
        <dificultad>2</dificultad>
        <listaMusculos>
            <musculo>Cuádriceps</musculo>
            <musculo>Glúteo</musculo>
        </listaMusculos>
    </maquina>
    <maquina>
        <id>2</id>
        <nombre>Cinta de Correr Skillrun</nombre>
        <marca>Technogym</marca>
        <modelo>SK-2024</modelo>
        <categoria>Cardio</categoria>
        <dimensiones>
            <ancho>0.90</ancho>
            <largo>2.00</largo>
            <alto>1.70</alto>
        </dimensiones>
        <dificultad>1</dificultad>
        <listaMusculos>
            <musculo>Corazón</musculo>
            <musculo>Gemelos</musculo>
        </listaMusculos>
    </maquina>
</gimnasio>
```


## 7. Guía de Defensa y Reflexiones

### ¿Por qué `<dificultad>` es simpleType y `<dimensiones>` es complexType?
La dificultad es un único valor numérico al que solo aplicamos reglas de rango. Las dimensiones, en cambio, actúan como un contenedor que agrupa tres valores distintos (ancho, largo y alto), por lo que requiere una estructura compleja.

### ¿Para qué sirve el `maxOccurs="unbounded"`?
Permite que una máquina trabaje un número ilimitado de músculos. Técnicamente, esto indica a JAXB que debe generar una `List<String>` en Java en lugar de una variable simple, facilitando el conteo y filtrado.

### ¿Qué ocurriría si el XML indica una dificultad de nivel 5?
El sistema fallaría durante la validación del XSD (antes de procesar los datos en Java), ya que hemos blindado el campo mediante `minInclusive` y `maxInclusive` para aceptar únicamente valores entre 1 y 3.

### Importancia de la Declaración XML
La línea `<?xml version="1.0" ...?>` debe ser estrictamente la primera del documento. Cualquier espacio o línea en blanco previa invalidará el archivo, impidiendo su lectura por parte de las librerías de Java.
```
