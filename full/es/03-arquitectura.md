# Arquitectura Hexagonal: Principios Clave

La arquitectura hexagonal —también conocida como Ports & Adapters— propone colocar el dominio en el centro de la aplicación, rodeado por "puertos" (interfaces) y "adaptadores" (implementaciones técnicas).

## 🧱 Capas

- **Dominio**: lógica de negocio pura
- **Aplicación**: casos de uso y orquestación
- **Adaptadores**: entrada/salida (web, DB, mensajería)

## 💡 Beneficios

- Bajo acoplamiento
- Alta testabilidad
- Flexibilidad para cambiar tecnologías

## 🖼 Diagrama general

```plaintext
[ Web Adapter ]    [ DB Adapter ]  
       |                  |  
       |          [ Application Layer ]  
       +--------> [ Domain Layer ] <--------+
