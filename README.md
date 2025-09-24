# 🎮 Clase Full Stack - APIs REST
## URLs Dinámicas, Métodos HTTP y Presentación del Reto GameTracker

**Duración:** 3 horas  
**Objetivo:** Dominar APIs RESTful con práctica de Torneos de Gaming y presentar el reto GameTracker

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
- El equipo ganador elige el primer jugador del ejemplo

### Preguntas de Repaso:
1. ¿Qué es Express y para qué sirve?
2. ¿Cómo se crea un servidor básico?
3. ¿Qué hace `app.use(express.json())`?
4. ¿En qué puerto corremos normalmente el servidor?

> ⚡ **Verificación:** Asegúrate de que todos tengan el proyecto de la clase anterior funcionando antes de continuar.

---

## 🔗 Parte 2: Parámetros de URL (30 min)

### Proyecto de Práctica: API de Torneos de Gaming 🏆

**Concepto:** Vamos a crear una API para gestionar jugadores de un torneo de videojuegos

**Piénsalo como una lista de participantes:**
- URL normal: `/jugadores` → "Muéstrame todos los jugadores"
- URL con parámetro: `/jugador/1` → "Muéstrame el jugador número 1"

### Código de Práctica

```javascript
// servidor.js
const express = require('express');
const app = express();

app.use(express.json());

// Array de jugadores del torneo (será nuestra base de datos temporal)
let jugadores = [
    { id: 1, nickname: "DragonSlayer", juego: "League of Legends", nivel: "Pro", pais: "Colombia" },
    { id: 2, nickname: "ShadowNinja", juego: "CS:GO", nivel: "Semi-Pro", pais: "México" },
    { id: 3, nickname: "FireMage", juego: "Valorant", nivel: "Amateur", pais: "Argentina" }
];

// Obtener jugador por ID
app.get('/jugador/:id', (req, res) => {
    const id = parseInt(req.params.id);
    const jugador = jugadores.find(j => j.id === id);
    
    if (!jugador) {
        return res.status(404).json({ error: "Jugador no encontrado en el torneo" });
    }
    
    res.json(jugador);
});

app.listen(3000, () => {
    console.log('🏆 API Torneo Gaming en http://localhost:3000');
});
```

### 🧪 Pruebas en el navegador:
- `http://localhost:3000/jugador/1`
- `http://localhost:3000/jugador/2`
- `http://localhost:3000/jugador/999`

### 🏆 Dinámica: "Encuentra tu Gamer"

**Actividad (10 min):**
- Cada estudiante agrega un jugador con su propio nickname
- Prueban obtener diferentes jugadores por ID
- El que encuentre el error 404 más creativo gana un punto

---

## 🔍 Parte 3: Query Parameters (30 min)

### Concepto con Filtros de Torneo

**Como filtrar participantes de un torneo:**
- `/jugadores?juego=Valorant` → "Solo jugadores de Valorant"
- `/jugadores?nivel=Pro` → "Solo jugadores profesionales"
- `/jugadores?pais=Colombia&limite=2` → "2 jugadores colombianos"

### Código de Práctica

```javascript
// Agregar al servidor.js

// Obtener todos los jugadores (con filtros opcionales)
app.get('/jugadores', (req, res) => {
    const limite = parseInt(req.query.limite);
    const juego = req.query.juego;
    const nivel = req.query.nivel;
    const pais = req.query.pais;
    const buscar = req.query.buscar;
    
    let resultado = jugadores;
    
    // Filtrar por juego
    if (juego) {
        resultado = resultado.filter(j => 
            j.juego.toLowerCase().includes(juego.toLowerCase())
        );
    }
    
    // Filtrar por nivel
    if (nivel) {
        resultado = resultado.filter(j => 
            j.nivel.toLowerCase().includes(nivel.toLowerCase())
        );
    }
    
    // Filtrar por país
    if (pais) {
        resultado = resultado.filter(j => 
            j.pais.toLowerCase().includes(pais.toLowerCase())
        );
    }
    
    // Búsqueda por nickname
    if (buscar) {
        resultado = resultado.filter(j => 
            j.nickname.toLowerCase().includes(buscar.toLowerCase())
        );
    }
    
    // Limitar resultados
    if (limite) {
        resultado = resultado.slice(0, limite);
    }
    
    res.json({
        total: resultado.length,
        jugadores: resultado
    });
});
```

### 🧪 Pruebas de filtros:
- `http://localhost:3000/jugadores`
- `http://localhost:3000/jugadores?limite=2`
- `http://localhost:3000/jugadores?juego=valorant`
- `http://localhost:3000/jugadores?nivel=pro`
- `http://localhost:3000/jugadores?pais=colombia&limite=1`

### 💪 Ejercicio Grupal: "Construye tu Filtro de Torneo"

**En equipos (15 min):**
1. Cada equipo agrega 5 jugadores más al array
2. Crean 3 URLs diferentes con combinaciones de filtros
3. Presentan sus filtros más útiles al grupo
4. Votan por el filtro más práctico para organizar torneos

---

## ☕ DESCANSO - 15 MINUTOS
*¡Perfecto momento para hablar de sus nicknames favoritos y juegos competitivos!*

---

## 📝 Parte 4: Método POST - Registrar Jugadores (30 min)

### Concepto: Inscribir Nuevos Participantes

**POST = Registrar un nuevo jugador en el torneo**

### Código de Práctica

```javascript
// Agregar al servidor.js

let proximoId = 4;

// Registrar nuevo jugador
app.post('/jugadores', (req, res) => {
    const { nickname, juego, nivel, pais } = req.body;
    
    // Validaciones
    if (!nickname) {
        return res.status(400).json({ error: "El nickname es obligatorio" });
    }
    if (!juego) {
        return res.status(400).json({ error: "El juego es obligatorio" });
    }
    if (!nivel) {
        return res.status(400).json({ error: "El nivel es obligatorio" });
    }
    if (!pais) {
        return res.status(400).json({ error: "El país es obligatorio" });
    }
    
    // Verificar que el nickname no esté repetido
    const nicknameExiste = jugadores.find(j => 
        j.nickname.toLowerCase() === nickname.toLowerCase()
    );
    
    if (nicknameExiste) {
        return res.status(409).json({ error: "Ese nickname ya está registrado" });
    }
    
    const nuevoJugador = {
        id: proximoId++,
        nickname: nickname,
        juego: juego,
        nivel: nivel,
        pais: pais
    };
    
    jugadores.push(nuevoJugador);
    
    res.status(201).json({
        mensaje: "¡Jugador registrado exitosamente en el torneo!",
        jugador: nuevoJugador
    });
});
```

### 🧪 Probar en RapidAPI Client o Postman:

**Method:** POST  
**URL:** `http://localhost:3000/jugadores`  
**Headers:** `Content-Type: application/json`  
**Body:**
```json
{
    "nickname": "ThunderStrike",
    "juego": "Fortnite",
    "nivel": "Amateur",
    "pais": "Chile"
}
```

### 🏁 Competencia: "Registro Masivo de Jugadores"

**Reglas (15 min):**
- Cada estudiante tiene 5 minutos para registrar jugadores
- Gana puntos por: nicknames creativos, variedad de juegos y países
- Pierde puntos por: nicknames duplicados, errores de validación
- Al final vemos quién organizó el torneo más diverso

---

## ✏️ Parte 5: Método PUT - Actualizar Información (30 min)

### Concepto: Editar Datos de Jugadores

**PUT = Actualizar completamente la información de un jugador**

### Código de Práctica

```javascript
// Agregar al servidor.js

// Actualizar información completa del jugador
app.put('/jugador/:id', (req, res) => {
    const id = parseInt(req.params.id);
    const { nickname, juego, nivel, pais } = req.body;
    
    // Validaciones
    if (!nickname || !juego || !nivel || !pais) {
        return res.status(400).json({ 
            error: "Se requieren todos los campos: nickname, juego, nivel, pais" 
        });
    }
    
    const jugador = jugadores.find(j => j.id === id);
    
    if (!jugador) {
        return res.status(404).json({ error: "Jugador no encontrado" });
    }
    
    // Verificar que el nuevo nickname no esté repetido (excepto el actual)
    const nicknameExiste = jugadores.find(j => 
        j.id !== id && j.nickname.toLowerCase() === nickname.toLowerCase()
    );
    
    if (nicknameExiste) {
        return res.status(409).json({ error: "Ese nickname ya está en uso" });
    }
    
    // Actualizar todos los campos
    jugador.nickname = nickname;
    jugador.juego = juego;
    jugador.nivel = nivel;
    jugador.pais = pais;
    
    res.json({
        mensaje: "Información del jugador actualizada exitosamente",
        jugador: jugador
    });
});
```

### 🧪 Ejemplo de actualización:

**Method:** PUT  
**URL:** `http://localhost:3000/jugador/1`  
**Body:**
```json
{
    "nickname": "DragonSlayer_Pro",
    "juego": "League of Legends",
    "nivel": "Pro",
    "pais": "Colombia"
}
```

### 👥 Práctica en Parejas: "Actualización de Perfiles"

**Actividad (20 min):**
1. **Persona A:** Registra 3 jugadores con información básica
2. **Persona B:** Los actualiza con información más completa (cambio de nivel, juego especializado, etc.)
3. Intercambian roles
4. Verifican los cambios con GET

**Ejemplos de actualizaciones:** Amateur → Semi-Pro, cambio de juego principal, país de residencia

---

## 🗑️ Parte 6: Método DELETE + Presentación GameTracker (30 min)

### DELETE - Descalificar Jugadores (15 min)

**DELETE = Remover un jugador del torneo**

### Código de Práctica

```javascript
// Agregar al servidor.js

// Descalificar/eliminar jugador
app.delete('/jugador/:id', (req, res) => {
    const id = parseInt(req.params.id);
    
    const indice = jugadores.findIndex(j => j.id === id);
    
    if (indice === -1) {
        return res.status(404).json({ error: "Jugador no encontrado" });
    }
    
    const jugadorEliminado = jugadores.splice(indice, 1)[0];
    
    res.json({ 
        mensaje: "Jugador descalificado del torneo", 
        jugador: jugadorEliminado,
        participantesRestantes: jugadores.length
    });
});
```

### 🔄 Ejercicio Final CRUD Completo

**Flujo completo en RapidAPI Client (10 min):**
1. GET `/jugadores` - Ver participantes actuales
2. POST `/jugadores` - Registrar un nuevo participante
3. GET `/jugador/:id` - Ver el jugador registrado
4. PUT `/jugador/:id` - Actualizar su información
5. DELETE `/jugador/:id` - Descalificar al jugador
6. GET `/jugadores` - Verificar que se eliminó

---

## 🎮 ¡PRESENTACIÓN DEL RETO GAMETRACKER!

### Transición: De Torneos a Biblioteca Personal

**"Ahora que saben manejar datos de jugadores en torneos, vamos a crear algo personal: su propia biblioteca de videojuegos"**

### El Desafío Final

**¡Es hora del desafío final! Un proyecto completamente diferente:**

| Tecnología | Descripción |
|------------|-------------|
| 🖥️ **Backend** | Node.js + Express + MongoDB |
| ⚛️ **Frontend** | React + CSS |
| 🗄️ **Base de Datos** | Mongoose + Atlas |
| 📝 **CRUD** | Juegos + Reseñas |

### 🎯 El Reto: GameTracker - Tu Biblioteca Personal

**Una aplicación completamente diferente donde puedas:**
- 📚 Gestionar tu colección personal de videojuegos
- ⭐ Escribir reseñas detalladas con puntuaciones
- 🔍 Filtrar tu biblioteca por género, plataforma, etc.
- 📊 Ver estadísticas de tus juegos jugados
- ✅ Marcar juegos como completados
- ⏱️ Registrar horas jugadas

### 🗄️ Entidades del CRUD (Diferentes a la Práctica)

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

### ⚙️ Backend - API RESTful GameTracker

#### Endpoints para Videojuegos:
- `GET /api/juegos` - Obtener todos los juegos de tu biblioteca
- `GET /api/juegos/:id` - Obtener un juego específico
- `POST /api/juegos` - Agregar juego a tu colección
- `PUT /api/juegos/:id` - Actualizar información del juego
- `DELETE /api/juegos/:id` - Remover juego de tu biblioteca

#### Endpoints para Reseñas:
- `GET /api/reseñas` - Obtener todas tus reseñas
- `GET /api/reseñas/juego/:juegoId` - Reseñas de un juego específico
- `POST /api/reseñas` - Escribir nueva reseña
- `PUT /api/reseñas/:id` - Actualizar reseña existente
- `DELETE /api/reseñas/:id` - Eliminar reseña

#### Requisitos Técnicos:
- Validaciones con Mongoose
- Relaciones entre entidades (populate)
- Manejo de errores
- Middleware de validación
- Autenticación (opcional)

### ⚛️ Frontend - React

#### Componentes Principales:
- **BibliotecaJuegos** - Vista principal de la colección
- **TarjetaJuego** - Card individual de cada juego
- **FormularioJuego** - Agregar/editar juegos
- **ListaReseñas** - Vista de reseñas
- **FormularioReseña** - Escribir/editar reseñas
- **EstadisticasPersonales** - Dashboard de estadísticas

#### Funcionalidades Core:
- Ver biblioteca completa con imágenes
- Agregar nuevos juegos con portadas
- Marcar juegos como completados
- Sistema de puntuación con estrellas ⭐
- Escribir reseñas detalladas
- Registro de horas jugadas
- Diseño responsive y atractivo

### ✨ Características Extra (Opcionales)

#### Filtros y Búsqueda:
- Filtro por género (RPG, Acción, etc.)
- Filtro por plataforma (PC, PlayStation, etc.)
- Solo juegos completados/por completar
- Búsqueda por título o desarrollador
- Ordenamiento por fecha, puntuación, etc.

#### Funcionalidades Avanzadas:
- Dashboard de estadísticas personales
- Modo oscuro 🌙
- Drag & drop para subir portadas
- Gráficos de progreso y tiempo jugado
- Lista de deseos (Wishlist)
- Exportar biblioteca a PDF

### 📋 Entregables del Reto

| ✅ | Componente | Descripción |
|----|------------|-------------|
| ✅ | **Backend** | API REST completa con estructura clara, modelos Mongoose y validaciones |
| ✅ | **Frontend** | Aplicación React funcional con diseño atractivo y todas las funcionalidades |
| ✅ | **Documentación** | README detallado con instrucciones de instalación y capturas |
| ✅ | **Datos de Prueba** | Base de datos populada con juegos populares y reseñas ejemplo |
| ✅ | **Deploy** | Aplicación desplegada en plataformas como Netlify/Vercel + Railway/Render |

### 🎯 Diferencias Clave entre Práctica y Reto:

| Aspecto | Práctica (Torneo) | Reto (GameTracker) |
|---------|-------------------|-------------------|
| **Temática** | Jugadores de torneo | Biblioteca personal |
| **Entidades** | Jugadores | Juegos + Reseñas |
| **Funcionalidad** | Gestión de participantes | Colección personal |
| **Complejidad** | Una entidad simple | Dos entidades relacionadas |
| **Frontend** | Solo backend | Full Stack completo |
| **Base de Datos** | Array en memoria | MongoDB + Mongoose |

---

## 📋 Código Completo Final de la Práctica

```javascript
// servidor.js - API Torneo Gaming Completa
const express = require('express');
const app = express();

app.use(express.json());

let jugadores = [
    { id: 1, nickname: "DragonSlayer", juego: "League of Legends", nivel: "Pro", pais: "Colombia" },
    { id: 2, nickname: "ShadowNinja", juego: "CS:GO", nivel: "Semi-Pro", pais: "México" },
    { id: 3, nickname: "FireMage", juego: "Valorant", nivel: "Amateur", pais: "Argentina" }
];

let proximoId = 4;

// GET - Obtener jugador por ID
app.get('/jugador/:id', (req, res) => {
    const id = parseInt(req.params.id);
    const jugador = jugadores.find(j => j.id === id);
    
    if (!jugador) {
        return res.status(404).json({ error: "Jugador no encontrado" });
    }
    
    res.json(jugador);
});

// GET - Obtener todos los jugadores con filtros
app.get('/jugadores', (req, res) => {
    const limite = parseInt(req.query.limite);
    const juego = req.query.juego;
    const nivel = req.query.nivel;
    const pais = req.query.pais;
    const buscar = req.query.buscar;
    
    let resultado = jugadores;
    
    if (juego) {
        resultado = resultado.filter(j => 
            j.juego.toLowerCase().includes(juego.toLowerCase())
        );
    }
    
    if (nivel) {
        resultado = resultado.filter(j => 
            j.nivel.toLowerCase().includes(nivel.toLowerCase())
        );
    }
    
    if (pais) {
        resultado = resultado.filter(j => 
            j.pais.toLowerCase().includes(pais.toLowerCase())
        );
    }
    
    if (buscar) {
        resultado = resultado.filter(j => 
            j.nickname.toLowerCase().includes(buscar.toLowerCase())
        );
    }
    
    if (limite) {
        resultado = resultado.slice(0, limite);
    }
    
    res.json({ total: resultado.length, jugadores: resultado });
});

// POST - Registrar nuevo jugador
app.post('/jugadores', (req, res) => {
    const { nickname, juego, nivel, pais } = req.body;
    
    if (!nickname || !juego || !nivel || !pais) {
        return res.status(400).json({ 
            error: "Se requieren todos los campos" 
        });
    }
    
    // Verificar nickname único
    const nicknameExiste = jugadores.find(j => 
        j.nickname.toLowerCase() === nickname.toLowerCase()
    );
    
    if (nicknameExiste) {
        return res.status(409).json({ error: "Nickname ya registrado" });
    }
    
    const nuevoJugador = {
        id: proximoId++,
        nickname,
        juego,
        nivel,
        pais
    };
    
    jugadores.push(nuevoJugador);
    res.status(201).json({ 
        mensaje: "Jugador registrado exitosamente",
        jugador: nuevoJugador 
    });
});

// PUT - Actualizar jugador
app.put('/jugador/:id', (req, res) => {
    const id = parseInt(req.params.id);
    const { nickname, juego, nivel, pais } = req.body;
    
    if (!nickname || !juego || !nivel || !pais) {
        return res.status(400).json({ 
            error: "Se requieren todos los campos" 
        });
    }
    
    const jugador = jugadores.find(j => j.id === id);
    
    if (!jugador) {
        return res.status(404).json({ error: "Jugador no encontrado" });
    }
    
    // Verificar nickname único (excepto el actual)
    const nicknameExiste = jugadores.find(j => 
        j.id !== id && j.nickname.toLowerCase() === nickname.toLowerCase()
    );
    
    if (nicknameExiste) {
        return res.status(409).json({ error: "Nickname ya en uso" });
    }
    
    jugador.nickname = nickname;
    jugador.juego = juego;
    jugador.nivel = nivel;
    jugador.pais = pais;
    
    res.json({
        mensaje: "Jugador actualizado exitosamente",
        jugador: jugador
    });
});

// DELETE - Descalificar jugador
app.delete('/jugador/:id', (req, res) => {
    const id = parseInt(req.params.id);
    
    const indice = jugadores.findIndex(j => j.id === id);
    
    if (indice === -1) {
        return res.status(404).json({ error: "Jugador no encontrado" });
    }
    
    const jugadorEliminado = jugadores.splice(indice, 1)[0];
    res.json({ 
        mensaje: "Jugador descalificado del torneo", 
        jugador: jugadorEliminado,
        participantesRestantes: jugadores.length
    });
});

app.listen(3000, () => {
    console.log('🏆 API Torneo Gaming funcionando en http://localhost:3000');
});
```

> 💡 **Próximos Pasos:** En las siguientes clases trabajaremos con MongoDB, Mongoose, y React para crear el GameTracker completo.

---

## 📚 Resumen de lo Aprendido

### 🎯 Conceptos Clave Dominados:
- **Parámetros de URL:** `:id` → `req.params.id`
- **Query Parameters:** `?nivel=Pro` → `req.query.nivel`
- **POST:** Crear recursos con validaciones
- **PUT:** Actualizar recursos completos
- **DELETE:** Eliminar recursos
- **Validaciones avanzadas:** Unicidad, campos requeridos
- **Status Codes:** 200, 201, 400, 404, 409

### 🛠️ Habilidades Prácticas:
- ✅ Crear APIs RESTful completas
- ✅ Manejar diferentes tipos de parámetros
- ✅ Implementar operaciones CRUD
- ✅ Validar datos de entrada con lógica de negocio
- ✅ Estructurar respuestas JSON informativas
- ✅ Probar APIs con herramientas profesionales

### 🚀 Lo Que Viene en el Reto GameTracker:
- Conexión a MongoDB Atlas
- Modelos con Mongoose y relaciones
- Frontend en React con diseño atractivo
- Subida de imágenes
- Autenticación de usuarios
- Despliegue en producción

---

## 🎊 ¡Felicidades Developers!

**Han completado exitosamente los fundamentos de APIs REST con una práctica real de torneos de gaming**  
**Ahora están preparados para el desafío GameTracker completo 🎮**

### Tarea para Casa:
1. **Piensen en su biblioteca personal:** ¿Qué 10 videojuegos agregarían primero a su GameTracker?
2. **Diseñen una funcionalidad extra:** ¿Qué característica única le añadirían que no esté en Steam o Epic Games?
3. **Investiguen:** Busquen 3 APIs de videojuegos que podrían integrar (RAWG, IGDB, etc.)

---

**¡Prepárense para construir su proyecto Full Stack más ambicioso! 🚀**