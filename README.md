# Gestor de Documentos con ChromaDB

## Descripción General
Una implementación robusta en Python para gestionar operaciones de documentos con ChromaDB, que incluye manejo completo de errores, registro de actividades, operaciones en lote y validación de metadatos. Este sistema ofrece una solución completa para almacenar, recuperar y consultar datos de documentos basados en vectores.

## Características
- 🔒 Manejo robusto de errores y validación
- 🖍️ Sistema completo de registro (logs)
- 🚀 Soporte para operaciones en lote
- ✅ Validación de metadatos
- 🔄 Actualizaciones parciales de documentos
- 🔍 Capacidades avanzadas de consulta
- 💾 Almacenamiento persistente
- 📊 Tipado estático y sugerencias de tipo

## Instalación

### Requisitos previos
- Python 3.7+
- Gestor de paquetes `pip`

### Paquetes necesarios
```bash
pip install chromadb logging typing
```

## Uso Básico

### Inicializar el Gestor
```python
from chromadb_manager import ChromaDBManager

# Crear una instancia del gestor
db_manager = ChromaDBManager(path="./data", collection_name="Documents")
```

### Crear Documentos

#### Documento Único
```python
try:
    db_manager.create_document(
        doc_id="doc1",
        text="Texto de ejemplo del documento",
        metadata={"categoria": "ejemplo"}
    )
except ValueError as e:
    print(f"Error de validación: {e}")
except Exception as e:
    print(f"Error al crear el documento: {e}")
```

#### Creación en Lote
```python
documents = [
    {
        "doc_id": "doc2",
        "text": "Segundo documento",
        "metadata": {"categoria": "lote"}
    },
    {
        "doc_id": "doc3",
        "text": "Tercer documento",
        "metadata": {"categoria": "lote"}
    }
]

try:
    db_manager.create_documents_bulk(documents)
except Exception as e:
    print(f"Error en la creación en lote: {e}")
```

### Leer Documentos
```python
try:
    document = db_manager.read_document("doc1")
    print(f"Texto: {document['document']}")
    print(f"Metadatos: {document['metadata']}")
except ValueError as e:
    print(f"Documento no encontrado: {e}")
```

### Actualizar Documentos
```python
# Actualización Completa
try:
    db_manager.update_document(
        doc_id="doc1",
        new_text="Texto actualizado",
        new_metadata={"categoria": "actualizado"}
    )
except ValueError as e:
    print(f"Error de actualización: {e}")

# Actualización Parcial (solo metadatos)
try:
    db_manager.update_document(
        doc_id="doc1",
        new_metadata={"categoria": "modificado"}
    )
except ValueError as e:
    print(f"Error en la actualización parcial: {e}")
```

### Eliminar Documentos
```python
try:
    db_manager.delete_document("doc1")
except ValueError as e:
    print(f"Error al eliminar: {e}")
```

### Consultar Documentos

#### Consulta de Documentos Similares
```python
try:
    similar_docs = db_manager.query_similar(
        text="Texto de consulta de ejemplo",
        top_k=3,
        filters={"categoria": "ejemplo"}
    )
    for doc in similar_docs:
        print(f"Documento similar: {doc['text']}")
        print(f"Metadatos: {doc['metadata']}")
except Exception as e:
    print(f"Error en la consulta: {e}")
```

#### Consulta por Filtros
```python
try:
    filtered_docs = db_manager.query_by_filter(
        filter_conditions={"categoria": "ejemplo"}
    )
    for doc in filtered_docs:
        print(f"ID: {doc['id']}")
        print(f"Texto: {doc['text']}")
        print(f"Metadatos: {doc['metadata']}")
except Exception as e:
    print(f"Error en la consulta por filtros: {e}")
```

## Características Avanzadas

### Registro (Logging)
El sistema incluye registro completo de todas las operaciones:
```python
# Los logs se crean automáticamente con marcas de tiempo
# Ejemplo de entrada en el log:
# 2024-01-14 10:30:45 - ChromaDBManager - INFO - Documento creado exitosamente: doc1
```

### Validación de Metadatos
El sistema valida la estructura de los metadatos antes de las operaciones:
```python
# Los campos requeridos se pueden configurar en el método _validate_metadata
required_fields = {'categoria'}  # Modificar según sea necesario
```

### Manejo de Errores
Todas las operaciones incluyen manejo completo de errores:
- `ValueError` para problemas de validación y existencia de documentos
- `RuntimeError` para problemas de inicialización
- Captura de excepciones generales para errores inesperados

## Mejores Prácticas

1. **Manejo de Errores**
   - Siempre encapsula las operaciones en bloques `try-except`
   - Maneja las excepciones específicas por separado
   - Registra los errores adecuadamente

2. **Gestión de Metadatos**
   - Mantén una estructura consistente para los metadatos
   - Define claramente los campos requeridos
   - Valida los metadatos antes de realizar las operaciones

3. **Optimización de Consultas**
   - Utiliza valores apropiados para `top_k`
   - Incluye filtros relevantes
   - Maneja correctamente conjuntos de resultados grandes

4. **Gestión de Recursos**
   - Cierra las conexiones al finalizar
   - Monitorea el uso del disco con el almacenamiento persistente
   - Divide las operaciones en lote en partes pequeñas si son muy grandes

## Problemas Comunes y Soluciones

### Problema: Documento No Encontrado
```python
try:
    doc = db_manager.read_document("id_inexistente")
except ValueError as e:
    print("Documento no encontrado, créalo primero")
```

### Problema: Validación de Metadatos
```python
try:
    db_manager.create_document("doc1", "texto", {})  # Faltan campos requeridos
except ValueError as e:
    print("Estructura de metadatos inválida")
```

### Problema: Documentos Duplicados
```python
try:
    db_manager.create_document("id_existente", "texto", {"categoria": "prueba"})
except ValueError as e:
    print("El ID del documento ya existe")
```

## Limitaciones

1. **Actualizaciones**
   - Requieren reescritura completa del documento
   - No hay soporte para actualizaciones atómicas
   - No incluye soporte para transacciones

2. **Consultas**
   - Solo coincidencia exacta para filtros de metadatos
   - Soporte limitado para consultas complejas
   - No incluye capacidades de búsqueda de texto completo

3. **Escalabilidad**
   - Limitado por la memoria disponible
   - No incluye particionamiento automático
   - Operaciones limitadas a un solo nodo

## Contribuciones

¡Las contribuciones son bienvenidas! Sigue estos pasos:
1. Haz un fork del repositorio
2. Crea una rama para tu funcionalidad
3. Realiza tus cambios y haz commit
4. Haz push a la rama
5. Crea un Pull Request
