# Crear instancias en OpenStack

- Ubicación
  1. Compute
  2. Instancias
  3. Lanzar instancias

## Configuración

- Details
  - Instance name \[Nombre de la instancia\]
  - Zona de Disponibilidad nova
  - Count 1
- Source
  - Seleccionar Origen de arranque
  - Imagen
  - Volumen
  - Crear un nuevo volumen
  - Volumen Size (GB)
  - Delete Volumen on Instance Delete Borrar volumen junto con la instancia
  - Asignar CentOS-7-x86\_64-GenericCloud-1511
- Sabor
  - Asignar m1.middle
- Redes
  - Asignar Red propia o public si no has configurado una
- Grupos de Seguridad
  - Asignar default más la creada
- Par de claves
  - Asignados
  - Crear par de claves
  - Importar Par de claves
  - Seleccionar tú clave
- El resto los dejamos sin tocar
