# 🎮 GameTracker - Clase Full Stack
## URLs Dinámicas, Métodos HTTP y Presentación del Reto Final

**Duración:** 3 horas  
**Objetivo:** Dominar APIs RESTful y presentar el reto GameTracker

---

## 📅 Cronograma de la Clase

| Tiempo | Actividad |
|--------|-----------|
| 0:00 - 0:15 | 🎯 Bienvenida y Repaso Interactivo |
| 0:15 - 0:45 | 🔗 Parámetros de URL + Dinámica |
| 0:45 - 1:15 | 🔍 Query Parameters + Ejercicio Grupal |
| 1:15 - 1:30 | ☕ **DESCANSO** |
| 1:30 - 2:00 | 📝 Método POST + Competencia |
| 2:00 - 2:30 | ✏️ Método PUT + Práctica en Parejas |
| 2:30 - 3:00 | 🗑️ Método DELETE + Presentación Reto GameTracker |

---

## 🎯 Parte 1: Bienvenida y Repaso Interactivo (15 min)

### 🎮 Dinámica: "El Desarrollador Veloz"

**Objetivo:** Repasar conceptos mientras nos divertimos

**Instrucciones:**
- Dividir en equipos de 3-4 personas
- Cada equipo escoge un nombre de videojuego famoso
- Preguntas rápidas sobre Express (30 segundos por respuesta)
- El equipo ganador elige el primer videojuego del ejemplo

### Preguntas de Repaso:
1. ¿Qué es Express y para qué sirve?
2. ¿Cómo se crea un servidor básico?
3. ¿Qué hace `app.use(express.json())`?
4. ¿En qué puerto corremos normalmente el servidor?

> ⚡ **Verificación:** Asegúrate de que todos tengan el proyecto de la clase anterior funcionando antes de continuar.

---

## 🔗 Parte 2: Parámetros de URL (30 min)

### Concepto Básico con Analogía de Videojuegos

**Piénsalo como un inventario de juegos:**
- URL normal: `/juegos` → "Muéstrame todos los juegos"
- URL con parámetro: `/juego/1` → "Muéstrame el juego número 1"

### Código de Práctica

```javascript
// servidor.js
const express = require('express');
const app = express();

app.use(express.json());

// Array de videojuegos (será nuestro "inventario")
let juegos = [
    { id: 1, titulo: "The Legend of Zelda", genero: "Aventura", plataforma: "Nintendo Switch" },
    { id: 2, titulo: "God of War", genero: "Acción", plataforma: "PlayStation" },
    { id: 3, titulo: "Halo", genero: "FPS", plataforma: "Xbox" }
];

// Obtener juego por ID
app.get('/juego/:id', (req, res) => {
    const id = parseInt(req.params.id);
    const juego = juegos.find(j => j.id === id);
    
    if (!juego) {
        return res.status(404).json({ error: "Juego no encontrado en el inventario" });
    }
    
    res.json(juego);
});

app.listen(3000, () => {
    console.log('GameTracker API en http://localhost:3000');
});
```

### 🧪 Pruebas en el navegador:
- `http://localhost:3000/juego/1`
- `http://localhost:3000/juego/2`
- `http://localhost:3000/juego/999`

### 🏆 Dinámica: "Encuentra tu Juego Favorito"

**Actividad (10 min):**
- Cada estudiante agrega su videojuego favorito al array
- Prueban obtener diferentes juegos por ID
- El que encuentre el error 404 más creativo gana un punto

---

## 🔍 Parte 3: Query Parameters (30 min)

### Concepto con Analogía de Filtros de Steam

**Como filtrar juegos en una tienda:**
- `/juegos?genero=RPG` → "Solo juegos de RPG"
- `/juegos?limite=5` → "Solo los primeros 5 juegos"
- `/juegos?plataforma=PC&limite=3` → "3 juegos de PC"

### Código de Práctica

```javascript
// Agregar al servidor.js

// Obtener todos los juegos (con filtros opcionales)
app.get('/juegos', (req, res) => {
    const limite = parseInt(req.query.limite);
    const genero = req.query.genero;
    const plataforma = req.query.plataforma;
    const buscar = req.query.buscar;
    
    let resultado = juegos;
    
    // Filtrar por género
    if (genero) {
        resultado = resultado.filter(j => 
            j.genero.toLowerCase().includes(genero.toLowerCase())
        );
    }
    
    // Filtrar por plataforma
    if (plataforma) {
        resultado = resultado.filter(j => 
            j.plataforma.toLowerCase().includes(plataforma.toLowerCase())
        );
    }
    
    // Búsqueda por título
    if (buscar) {
        resultado = resultado.filter(j => 
            j.titulo.toLowerCase().includes(buscar.toLowerCase())
        );
    }
    
    // Limitar resultados
    if (limite) {
        resultado = resultado.slice(0, limite);
    }
    
    res.json({
        total: resultado.length,
        juegos: resultado
    });
});
```

### 🧪 Pruebas de filtros:
- `http://localhost:3000/juegos`
- `http://localhost:3000/juegos?limite=2`
- `http://localhost:3000/juegos?genero=accion`
- `http://localhost:3000/juegos?buscar=zelda`
- `http://localhost:3000/juegos?plataforma=xbox&limite=1`

### 💪 Ejercicio Grupal: "Construye tu Filtro Perfecto"

**En equipos (15 min):**
1. Cada equipo agrega 5 juegos más al array
2. Crean 3 URLs diferentes con combinaciones de filtros
3. Presentan sus filtros más creativos al grupo
4. Votan por el filtro más útil

---

## ☕ DESCANSO - 15 MINUTOS
*¡Perfecto momento para hablar de sus videojuegos favoritos!*

---

## 📝 Parte 4: Método POST - Crear Juegos (30 min)

### Concepto: Agregar Juegos a la Biblioteca

**POST = Agregar un nuevo juego a tu colección**

### Código de Práctica

```javascript
// Agregar al servidor.js

let proximoId = 4;

// Crear nuevo juego
app.post('/juegos', (req, res) => {
    const { titulo, genero, plataforma } = req.body;
    
    // Validaciones
    if (!titulo) {
        return res.status(400).json({ error: "El título es obligatorio" });
    }
    if (!genero) {
        return res.status(400).json({ error: "El género es obligatorio" });
    }
    if (!plataforma) {
        return res.status(400).json({ error: "La plataforma es obligatoria" });
    }
    
    const nuevoJuego = {
        id: proximoId++,
        titulo: titulo,
        genero: genero,
        plataforma: plataforma
    };
    
    juegos.push(nuevoJuego);
    
    res.status(201).json({
        mensaje: "¡Juego agregado a tu biblioteca!",
        juego: nuevoJuego
    });
});
```

### 🧪 Probar en RapidAPI Client o Postman:

**Method:** POST  
**URL:** `http://localhost:3000/juegos`  
**Headers:** `Content-Type: application/json`  
**Body:**
```json
{
    "titulo": "Cyberpunk 2077",
    "genero": "RPG",
    "plataforma": "PC"
}
```

### 🏁 Competencia: "Quién Agrega Más Juegos"

**Reglas (15 min):**
- Cada estudiante tiene 5 minutos para agregar juegos
- Gana puntos por: creatividad del título, variedad de géneros
- Pierde puntos por: datos duplicados, errores de validación
- Al final vemos quién construyó la mejor biblioteca

---

## ✏️ Parte 5: Método PUT - Actualizar Juegos (30 min)

### Concepto: Editar Información de Juegos

**PUT = Actualizar completamente un juego existente**

### Código de Práctica

```javascript
// Agregar al servidor.js

// Actualizar juego completo
app.put('/juego/:id', (req, res) => {
    const id = parseInt(req.params.id);
    const { titulo, genero, plataforma } = req.body;
    
    // Validaciones
    if (!titulo || !genero || !plataforma) {
        return res.status(400).json({ 
            error: "Se requieren todos los campos: titulo, genero, plataforma" 
        });
    }
    
    const juego = juegos.find(j => j.id === id);
    
    if (!juego) {
        return res.status(404).json({ error: "Juego no encontrado" });
    }
    
    // Actualizar todos los campos
    juego.titulo = titulo;
    juego.genero = genero;
    juego.plataforma = plataforma;
    
    res.json({
        mensaje: "Juego actualizado exitosamente",
        juego: juego
    });
});
```

### 🧪 Ejemplo de actualización:

**Method:** PUT  
**URL:** `http://localhost:3000/juego/1`  
**Body:**
```json
{
    "titulo": "The Legend of Zelda: Breath of the Wild",
    "genero": "Aventura/Acción",
    "plataforma": "Nintendo Switch"
}
```

### 👥 Práctica en Parejas: "Corrige la Información"

**Actividad (20 min):**
1. **Persona A:** Crea 3 juegos con información "incorrecta"
2. **Persona B:** Los corrige usando PUT
3. Intercambian roles
4. Verifican los cambios con GET

**Ejemplos de "errores":** nombres incompletos, géneros genéricos, plataformas obsoletas

---

## 🗑️ Parte 6: Método DELETE + Reto GameTracker (30 min)

### DELETE - Eliminar Juegos (15 min)

**DELETE = Remover un juego de la biblioteca**

### Código de Práctica

```javascript
// Agregar al servidor.js

// Eliminar juego
app.delete('/juego/:id', (req, res) => {
    const id = parseInt(req.params.id);
    
    const indice = juegos.findIndex(j => j.id === id);
    
    if (indice === -1) {
        return res.status(404).json({ error: "Juego no encontrado" });
    }
    
    const juegoEliminado = juegos.splice(indice, 1)[0];
    
    res.json({ 
        mensaje: "Juego eliminado de tu biblioteca", 
        juego: juegoEliminado,
        totalRestante: juegos.length
    });
});
```

### 🔄 Ejercicio Final CRUD Completo

**Flujo completo en RapidAPI Client (10 min):**
1. GET `/juegos` - Ver biblioteca actual
2. POST `/juegos` - Agregar tu juego favorito
3. GET `/juego/:id` - Ver el juego creado
4. PUT `/juego/:id` - Actualizar información
5. DELETE `/juego/:id` - Eliminar el juego
6. GET `/juegos` - Verificar que se eliminó

---

## 🎮 ¡PRESENTACIÓN DEL RETO GAMETRACKER!

### El Desafío Final

**Ahora que dominas los métodos HTTP, ¡es hora del desafío final!**

| Tecnología | Descripción |
|------------|-------------|
| 🖥️ **Backend** | Node.js + Express + MongoDB |
| ⚛️ **Frontend** | React + CSS |
| 🗄️ **Base de Datos** | Mongoose + Atlas |
| 📝 **CRUD** | Juegos + Reseñas |

### 🎯 El Reto: Crear tu GameTracker Personal

**Una aplicación completa donde puedas:**
- 📚 Gestionar tu biblioteca de videojuegos
- ⭐ Escribir reseñas con puntuaciones
- 🔍 Filtrar por género, plataforma, etc.
- 📊 Ver estadísticas de tus juegos
- ✅ Marcar juegos como completados

### 🗄️ Entidades del CRUD

#### 1. Videojuegos (Games)
```javascript
{
  _id: ObjectId,
  titulo: String,
  genero: String,           // "Acción", "RPG", "Estrategia", etc.
  plataforma: String,       // "PC", "PlayStation", "Xbox", etc.
  añoLanzamiento: Number,
  desarrollador: String,
  imagenPortada: String,    // URL de la imagen
  descripcion: String,
  completado: Boolean,
  fechaCreacion: Date
}
```

#### 2. Reseñas (Reviews)
```javascript
{
  _id: ObjectId,
  juegoId: ObjectId,        // Referencia al videojuego
  puntuacion: Number,       // 1-5 estrellas
  textoReseña: String,
  horasJugadas: Number,
  dificultad: String,       // "Fácil", "Normal", "Difícil"
  recomendaria: Boolean,
  fechaCreacion: Date,
  fechaActualizacion: Date
}
```

### ⚙️ Backend - API RESTful

#### Endpoints para Videojuegos:
- `GET /api/juegos` - Obtener todos los juegos
- `GET /api/juegos/:id` - Obtener un juego específico
- `POST /api/juegos` - Crear nuevo juego
- `PUT /api/juegos/:id` - Actualizar juego
- `DELETE /api/juegos/:id` - Eliminar juego

#### Endpoints para Reseñas:
- `GET /api/reseñas` - Obtener todas las reseñas
- `GET /api/reseñas/juego/:juegoId` - Reseñas de un juego
- `POST /api/reseñas` - Crear nueva reseña
- `PUT /api/reseñas/:id` - Actualizar reseña
- `DELETE /api/reseñas/:id` - Eliminar reseña

#### Requisitos Técnicos:
- Validaciones con Mongoose
- Relaciones entre entidades (populate)
- Manejo de errores
- Middleware de validación

### ⚛️ Frontend - React

#### Componentes Principales:
- **ListaJuegos** - Vista de la biblioteca
- **TarjetaJuego** - Card individual
- **FormularioJuego** - Agregar/editar
- **ListaReseñas** - Vista de reseñas
- **FormularioReseña** - Escribir reseñas

#### Funcionalidades Core:
- Ver biblioteca completa
- Agregar nuevos juegos
- Marcar como completados
- Sistema de puntuación ⭐
- Escribir y editar reseñas
- Diseño responsive

### ✨ Características Extra (Opcionales)

#### Filtros y Búsqueda:
- Filtro por género
- Filtro por plataforma
- Solo juegos completados
- Búsqueda por título

#### Funcionalidades Avanzadas:
- Estadísticas personales
- Modo oscuro 🌙
- Drag & drop para imágenes
- Gráficos de progreso

### 📋 Entregables

| ✅ | Componente | Descripción |
|----|------------|-------------|
| ✅ | **Backend** | API REST completa con estructura de carpetas clara, modelos de Mongoose y validaciones implementadas |
| ✅ | **Frontend** | Aplicación React funcional con todos los componentes, navegación y consumo de API |
| ✅ | **Documentación** | README detallado con instrucciones de instalación y uso |
| ✅ | **Datos de Prueba** | Base de datos populada con videojuegos y reseñas de ejemplo |

---

## 📋 Código Completo Final

```javascript
// servidor.js - GameTracker API Completa
const express = require('express');
const app = express();

app.use(express.json());

let juegos = [
    { id: 1, titulo: "The Legend of Zelda", genero: "Aventura", plataforma: "Nintendo Switch" },
    { id: 2, titulo: "God of War", genero: "Acción", plataforma: "PlayStation" },
    { id: 3, titulo: "Halo", genero: "FPS", plataforma: "Xbox" }
];

let proximoId = 4;

// GET - Obtener juego por ID
app.get('/juego/:id', (req, res) => {
    const id = parseInt(req.params.id);
    const juego = juegos.find(j => j.id === id);
    
    if (!juego) {
        return res.status(404).json({ error: "Juego no encontrado" });
    }
    
    res.json(juego);
});

// GET - Obtener todos los juegos con filtros
app.get('/juegos', (req, res) => {
    const limite = parseInt(req.query.limite);
    const genero = req.query.genero;
    const plataforma = req.query.plataforma;
    const buscar = req.query.buscar;
    
    let resultado = juegos;
    
    if (genero) {
        resultado = resultado.filter(j => 
            j.genero.toLowerCase().includes(genero.toLowerCase())
        );
    }
    
    if (plataforma) {
        resultado = resultado.filter(j => 
            j.plataforma.toLowerCase().includes(plataforma.toLowerCase())
        );
    }
    
    if (buscar) {
        resultado = resultado.filter(j => 
            j.titulo.toLowerCase().includes(buscar.toLowerCase())
        );
    }
    
    if (limite) {
        resultado = resultado.slice(0, limite);
    }
    
    res.json({ total: resultado.length, juegos: resultado });
});

// POST - Crear nuevo juego
app.post('/juegos', (req, res) => {
    const { titulo, genero, plataforma } = req.body;
    
    if (!titulo || !genero || !plataforma) {
        return res.status(400).json({ 
            error: "Se requieren todos los campos" 
        });
    }
    
    const nuevoJuego = {
        id: proximoId++,
        titulo,
        genero,
        plataforma
    };
    
    juegos.push(nuevoJuego);
    res.status(201).json({ 
        mensaje: "Juego agregado exitosamente",
        juego: nuevoJuego 
    });
});

// PUT - Actualizar juego
app.put('/juego/:id', (req, res) => {
    const id = parseInt(req.params.id);
    const { titulo, genero, plataforma } = req.body;
    
    if (!titulo || !genero || !plataforma) {
        return res.status(400).json({ 
            error: "Se requieren todos los campos" 
        });
    }
    
    const juego = juegos.find(j => j.id === id);
    
    if (!juego) {
        return res.status(404).json({ error: "Juego no encontrado" });
    }
    
    juego.titulo = titulo;
    juego.genero = genero;
    juego.plataforma = plataforma;
    
    res.json({
        mensaje: "Juego actualizado exitosamente",
        juego: juego
    });
});

// DELETE - Eliminar juego
app.delete('/juego/:id', (req, res) => {
    const id = parseInt(req.params.id);
    
    const indice = juegos.findIndex(j => j.id === id);
    
    if (indice === -1) {
        return res.status(404).json({ error: "Juego no encontrado" });
    }
    
    const juegoEliminado = juegos.splice(indice, 1)[0];
    res.json({ 
        mensaje: "Juego eliminado exitosamente", 
        juego: juegoEliminado,
        totalRestante: juegos.length
    });
});

app.listen(3000, () => {
    console.log('🎮 GameTracker API funcionando en http://localhost:3000');
});
```

> 💡 **Próximos Pasos:** En las siguientes clases agregaremos MongoDB, Mongoose, y crearemos el frontend en React para completar el GameTracker.

---

## 📚 Resumen de lo Aprendido

### 🎯 Conceptos Clave Dominados:
- **Parámetros de URL:** `:id` → `req.params.id`
- **Query Parameters:** `?limite=5` → `req.query.limite`
- **POST:** Crear recursos con `req.body`
- **PUT:** Actualizar recursos completos
- **DELETE:** Eliminar recursos
- **Validaciones:** Verificar datos antes de procesarlos
- **Status Codes:** 200, 201, 400, 404

### 🛠️ Habilidades Prácticas:
- ✅ Crear APIs RESTful completas
- ✅ Manejar diferentes tipos de parámetros
- ✅ Implementar operaciones CRUD
- ✅ Validar datos de entrada
- ✅ Estructurar respuestas JSON
- ✅ Probar APIs con herramientas como RapidAPI Client

### 🚀 Lo Que Viene:
- Conexión a MongoDB Atlas
- Modelos con Mongoose
- Relaciones entre entidades (Juegos ↔ Reseñas)
- Frontend en React
- Despliegue de la aplicación

---

## 🎊 ¡Felicidades Gamers Developers!

**Han completado exitosamente los fundamentos de APIs REST**  
**Ahora están listos para el desafío GameTracker completo 🎮**

### Tarea para Casa:
Piensen en 5 videojuegos que les gustaría agregar a su GameTracker y 3 características extra que les gustaría implementar.

---

**¿Listos para convertirse en desarrolladores Full Stack de videojuegos? 🚀**