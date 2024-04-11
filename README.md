# Asistente de Calibración y Certificados - PDF Procces

## Asistente de Calibración

### 1. Búsqueda por Modelo de Equipo

- **Ingreso del Modelo**: Los usuarios inician la interacción ingresando una parte del modelo del equipo en un campo de texto. Esta acción activa una búsqueda en una base de datos de certificados para encontrar coincidencias.

- **Selección de Certificado**: Los certificados que coinciden con el modelo ingresado se presentan al usuario. El usuario selecciona el certificado que desea consultar de la lista proporcionada.

- **Selección de Detalles de Calibración**:
  - Con el certificado seleccionado, se muestran las opciones para definir el grupo de medición objetivo, el valor nominal, y la unidad de medida.
  - El usuario elige el grupo de medición objetivo (por ejemplo, 'weight_-_linearity_down'), selecciona el valor nominal específico dentro de ese grupo, y la unidad de medida correspondiente.

- **Cálculo de Incertidumbre**: La aplicación calcula la incertidumbre de medición basándose en la selección del usuario, utilizando los datos del certificado seleccionado. Se toman en cuenta la incertidumbre de medición especificada y el CMC (Capacidad de Medición y Calibración), realizando cálculos internos para determinar la incertidumbre total.

![CalculoIncertidumbre](./formula.png)


- **Resultados**: Se presentan al usuario los detalles seleccionados y el resultado del cálculo de incertidumbre total.

### 2. Ingreso Manual del Número de Certificado

- **Ingreso del Número de Certificado**: En esta modalidad, el usuario ingresa directamente el número de certificado en vez de buscar por modelo.

- **Selección de Detalles de Calibración**: Similar al proceso después de seleccionar un certificado en la búsqueda por modelo, el usuario elige el grupo objetivo de medición, el valor nominal específico, y la unidad de medida.

- **Cálculo de Incertidumbre**: Utilizando los datos ingresados manualmente, el sistema calcula la incertidumbre total de la misma manera que en el proceso de búsqueda por modelo, utilizando la incertidumbre de medición proporcionada y el CMC para los cálculos.

- **Resultados**: Se muestran al usuario los detalles de su selección junto con el resultado del cálculo de incertidumbre total.

### Notas Importantes sobre la Lógica de Cálculo de Incertidumbre

- Para ambos métodos, el cálculo de la incertidumbre se basa en los mismos principios y utiliza la misma función o método. La diferencia principal radica en cómo el usuario accede a la etapa de selección de detalles específicos para el cálculo (búsqueda por modelo vs. entrada manual del número de certificado).
- La selección del grupo objetivo, valor nominal, y unidad de medida son pasos cruciales que determinan los parámetros específicos para el cálculo de la incertidumbre.
- Los datos de certificado incluyen la incertidumbre de medición y el CMC, elementos fundamentales para calcular la incertidumbre total.
- El cálculo considera el valor nominal en gramos (convertido si es necesario), el CMC fijo, el CMC proporcional, y la incertidumbre de medición especificada, combinándolos para producir la incertidumbre total expresada en gramos, miligramos y microgramos.

## Procesamiento de Certificados en PDF

### Inicio de la Aplicación en Streamlit:

- La aplicación Streamlit se inicia con un título descriptivo y un cargador de archivos que acepta archivos PDF.

### Carga y Procesamiento de PDF:

- El usuario carga un archivo PDF.
- El archivo PDF se guarda temporalmente para su procesamiento.
- Se extrae el número de certificado del PDF usando `PyMuPDF` (fitz) y expresiones regulares, identificando un patrón específico dentro de la primera página del documento.

### Extracción de Tablas del PDF:

- El PDF se procesa página por página.
  - Para la primera página, se extraen tablas utilizando coordenadas específicas que capturan áreas precisas de interés.
  - Para las páginas subsiguientes, se ajustan las áreas de extracción y se definen columnas específicas si es necesario, para adaptarse a posibles variaciones en la estructura o formato de las tablas.
- Se determina si algunas columnas dentro de las tablas extraídas deben eliminarse basándose en criterios específicos, como la presencia de ciertos caracteres o la falta de datos significativos.

### Generación de Archivo XLSX:

- Los datos extraídos se organizan y se guardan en un archivo XLSX en memoria, utilizando `openpyxl`.
- Se ofrece al usuario la opción de descargar este archivo XLSX.

### Conversión de XLSX a JSON:

- El archivo XLSX en memoria se procesa para convertir los datos de las tablas a formato JSON.
  - Se procesa específicamente la primera hoja para extraer datos generales del certificado.
  - Las hojas subsiguientes se procesan para extraer datos de mediciones y resultados, omitiendo líneas o palabras clave específicas que no son relevantes para la estructuración de datos.
- Los datos convertidos a JSON se ofrecen al usuario para su descarga.

### Actualización de Archivo JSON Acumulativo:

- Se actualiza un archivo JSON acumulativo (`certificate_data.json`) con los nuevos datos del certificado.
- Este archivo mantiene una colección de todos los certificados procesados, utilizando el número de certificado como clave para cada entrada.
- Si el archivo no existe, se crea; si ya existe, se lee y se actualiza con los nuevos datos sin sobrescribir los datos existentes.

### Manejo de Errores y Limpieza:

- Se manejan posibles errores durante el proceso, como problemas al eliminar el archivo PDF temporal.
- Se asegura la limpieza de recursos temporales y la correcta finalización del proceso.

### Consideraciones Específicas

#### Extracción y Procesamiento de Tablas:

- Se utilizan técnicas específicas para ajustar la extracción de tablas a los diferentes formatos encontrados en los PDFs, incluyendo la definición de áreas de extracción y la eliminación de columnas basadas en contenido específico.

#### Ajustes en la Extracción de Datos para JSON:

- El proceso de conversión de datos a JSON se ajusta para manejar variaciones en los datos de las tablas, como omitir ciertas líneas o ajustar el manejo de celdas combinadas.
