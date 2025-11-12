# MERN - App de Recetas (Proyecto inicial)

Este documento contiene **todo el código** y las instrucciones paso a paso para crear una aplicación MERN que guarda recetas con: nombre, ingredientes e instrucciones. También indica en **qué carpeta** debe ir cada archivo y los comandos para ejecutar el proyecto.

---

## Estructura del proyecto (recomendada)

```
mern-recetas/            # carpeta raíz del proyecto
├─ backend/              # backend (Node + Express)
│  ├─ .env.example
│  ├─ package.json
│  ├─ server.js
│  ├─ /models
│  │  └─ Recipe.js
│  └─ /routes
│     └─ recipes.js
│
└─ frontend/             # frontend (React)
   ├─ package.json
   └─ src/
      ├─ index.js
      ├─ App.js
      └─ /components
         ├─ RecipeForm.js
         └─ RecipeList.js
```

---

## Requisitos previos

* Node.js y npm instalados
* Cuenta en MongoDB Atlas **o** tener MongoDB local

---

## 1) Crear la carpeta del proyecto y subcarpetas

En tu terminal (preferiblemente en la carpeta donde quieras el proyecto):

```bash
mkdir mern-recetas
cd mern-recetas
mkdir backend frontend
```

---

## 2) Backend (backend)

### 2.1 `backend/package.json`

Crea la carpeta `backend` y dentro inicializa npm:

```bash
cd backend
npm init -y
npm install express mongoose cors dotenv
```

`package.json` se generará automáticamente. Opcionalmente añade un script de start en `package.json`:

```json
{
  "name": "mern-recetas-backend",
  "version": "1.0.0",
  "main": "server.js",
  "scripts": {
    "start": "node server.js",
    "dev": "nodemon server.js"
  }
}
```

> Si quieres usar `nodemon` para desarrollo: `npm install -D nodemon`.

### 2.2 `backend/.env.example`

Crea un archivo `.env` (no subas a GitHub) y un `.env.example` para referencia:

```
PORT=5000
MONGO_URI=mongodb+srv://<usuario>:<password>@cluster0.mongodb.net/recetas?retryWrites=true&w=majority
```

Sustituye la URI por la tuya si usas Atlas. Si usas Mongo local: `mongodb://localhost:27017/recetas`.

### 2.3 `backend/server.js`

Archivo principal del servidor (colócalo en `backend/server.js`):

```js
const express = require('express');
const mongoose = require('mongoose');
const cors = require('cors');
require('dotenv').config();

const recipesRouter = require('./routes/recipes');

const app = express();
app.use(cors());
app.use(express.json());

const PORT = process.env.PORT || 5000;
const MONGO_URI = process.env.MONGO_URI || 'mongodb://localhost:27017/recetas';

mongoose.connect(MONGO_URI, {
  useNewUrlParser: true,
  useUnifiedTopology: true,
}).then(() => {
  console.log('Conectado a MongoDB');
}).catch((err) => {
  console.error('Error conectando a MongoDB:', err.message);
});

app.use('/api/recipes', recipesRouter);

app.get('/', (req, res) => {
  res.send('API de Recetas funcionando');
});

app.listen(PORT, () => {
  console.log(`Servidor escuchando en puerto ${PORT}`);
});
```

### 2.4 `backend/models/Recipe.js`

Crea la carpeta `backend/models` y dentro `Recipe.js` con el esquema Mongoose:

```js
const mongoose = require('mongoose');

const RecipeSchema = new mongoose.Schema({
  name: { type: String, required: true },
  ingredients: { type: [String], required: true },
  instructions: { type: String, required: true },
  createdAt: { type: Date, default: Date.now },
});

module.exports = mongoose.model('Recipe', RecipeSchema);
```

### 2.5 `backend/routes/recipes.js`

Crea `backend/routes/recipes.js` con las rutas básicas: obtener todas y crear una nueva.

```js
const express = require('express');
const router = express.Router();
const Recipe = require('../models/Recipe');

// GET /api/recipes - lista todas las recetas
router.get('/', async (req, res) => {
  try {
    const recipes = await Recipe.find().sort({ createdAt: -1 });
    res.json(recipes);
  } catch (err) {
    res.status(500).json({ error: 'Error al obtener recetas' });
  }
});

// POST /api/recipes - crea receta
router.post('/', async (req, res) => {
  try {
    const { name, ingredients, instructions } = req.body;
    if (!name || !ingredients || !instructions) {
      return res.status(400).json({ error: 'Faltan campos requeridos' });
    }

    let ingrArray = ingredients;
    if (typeof ingredients === 'string') {
      ingrArray = ingredients
        .split(/,|\n/)
        .map(s => s.trim())
        .filter(Boolean);
    }

    const newRecipe = new Recipe({ name, ingredients: ingrArray, instructions });
    await newRecipe.save();
    res.status(201).json(newRecipe);
  } catch (err) {
    console.error(err);
    res.status(500).json({ error: 'Error al guardar receta' });
  }
});

module.exports = router;
```

---

## 3) Frontend (frontend) - React

En la carpeta raíz (`mern-recetas`) genera la app React:

```bash
cd ../  # vuelve a mern-recetas si estás en backend/
cd frontend
npx create-react-app .
```

Instala `concurrently` en la raíz si quieres levantar ambos con un solo comando (opcional).

### 3.1 `frontend/src/index.js`

```js
import React from 'react';
import ReactDOM from 'react-dom/client';
import App from './App';

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<React.StrictMode><App /></React.StrictMode>);
```

### 3.2 `frontend/src/App.js`

```js
import React from 'react';
import RecipeForm from './components/RecipeForm';
import RecipeList from './components/RecipeList';

function App() {
  return (
    <div style={{ maxWidth: 900, margin: '40px auto', padding: '0 20px' }}>
      <h1>Mi Libro de Recetas</h1>
      <RecipeForm />
      <hr />
      <RecipeList />
    </div>
  );
}

export default App;
```

### 3.3 `frontend/src/components/RecipeForm.js`

```js
import React, { useState } from 'react';

function RecipeForm() {
  const [name, setName] = useState('');
  const [ingredients, setIngredients] = useState('');
  const [instructions, setInstructions] = useState('');
  const [loading, setLoading] = useState(false);
  const [message, setMessage] = useState(null);

  const handleSubmit = async (e) => {
    e.preventDefault();
    setLoading(true);
    setMessage(null);

    try {
      const res = await fetch('http://localhost:5000/api/recipes', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ name, ingredients, instructions }),
      });

      if (!res.ok) {
        const err = await res.json();
        throw new Error(err.error || 'Error al guardar');
      }

      const data = await res.json();
      setMessage('Receta guardada con éxito');
      setName('');
      setIngredients('');
      setInstructions('');

      window.dispatchEvent(new CustomEvent('recipe:created', { detail: data }));
    } catch (err) {
      setMessage(err.message);
    } finally {
      setLoading(false);
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <div>
        <label>Nombre de la receta</label>
        <input value={name} onChange={e => setName(e.target.value)} required />
      </div>

      <div>
        <label>Ingredientes (separa por comas o escribe en líneas nuevas)</label>
        <textarea value={ingredients} onChange={e => setIngredients(e.target.value)} rows={4} required />
      </div>

      <div>
        <label>Instrucciones</label>
        <textarea value={instructions} onChange={e => setInstructions(e.target.value)} rows={6} required />
      </div>

      <button type="submit" disabled={loading}>{loading ? 'Guardando...' : 'Guardar receta'}</button>

      {message && <p>{message}</p>}
    </form>
  );
}

export default RecipeForm;
```

### 3.4 `frontend/src/components/RecipeList.js`

```js
import React, { useEffect, useState } from 'react';

function RecipeList() {
  const [recipes, setRecipes] = useState([]);
  const [loading, setLoading] = useState(true);

  const fetchRecipes = async () => {
    try {
      setLoading(true);
      const res = await fetch('http://localhost:5000/api/recipes');
      const data = await res.json();
      setRecipes(data);
    } catch (err) {
      console.error('Error al cargar recetas', err);
    } finally {
      setLoading(false);
    }
  };

  useEffect(() => {
    fetchRecipes();

    const handler = () => fetchRecipes();
    window.addEventListener('recipe:created', handler);
    return () => window.removeEventListener('recipe:created', handler);
  }, []);

  if (loading) return <p>Cargando recetas...</p>;
  if (recipes.length === 0) return <p>No hay recetas guardadas.</p>;

  return (
    <div>
      <h2>Recetas guardadas</h2>
      <ul>
        {recipes.map(r => (
          <li key={r._id} style={{ marginBottom: 12 }}>
            <h3>{r.name}</h3>
            <strong>Ingredientes:</strong>
            <ul>
              {r.ingredients.map((ing, idx) => <li key={idx}>{ing}</li>)}
            </ul>
            <strong>Instrucciones:</strong>
            <p style={{ whiteSpace: 'pre-wrap' }}>{r.instructions}</p>
          </li>
        ))}
      </ul>
    </div>
  );
}

export default RecipeList;
```

---

## 4) CORS y puertos

* El servidor por defecto corre en `http://localhost:5000`.
* El cliente en `http://localhost:3000`.
* En `server.js` ya activamos `cors()` con la configuración por defecto para permitir peticiones desde el cliente.

Si necesitas restringir orígenes, configura `cors({ origin: 'http://localhost:3000' })`.

---

## 5) Ejecutar el proyecto

Desde `backend/`:

```bash
npm run dev   # si usas nodemon
# o
npm start
```

Desde `frontend/`:

```bash
npm start
```

Opcional: desde la raíz `mern-recetas` puedes instalar `concurrently` y scripts para ejecutar ambos al mismo tiempo:

```bash
npm init -y
npm install -D concurrently
```

En `package.json` de la raíz:

```json
"scripts": {
  "dev": "concurrently \"cd backend && npm run dev\" \"cd frontend && npm start\""
}
```

---

## 6) Mejoras futuras (sugerencias)

* Añadir validaciones más completas en backend.
* Permitir editar y borrar recetas (agregar rutas PUT/DELETE).
* Añadir autenticación (register/login) si quieres guardar recetas por usuario.
* Guardar imágenes para cada receta (subida a Cloudinary o similar).
* Mejorar el estilo con CSS o una librería (Tailwind, Material UI).

---

## 7) ¿Problemas comunes y soluciones rápidas?

* `Mongoose` no se conecta: revisa `MONGO_URI` y asegúrate que la IP de Atlas permita tu conexión (orígenes/whitelist).
* Errores CORS: revisa que `cors()` esté configurado y que la URL del cliente sea la correcta.
* `fetch` retorna 404: revisa que la ruta `/api/recipes` esté correctamente montada en `server.js`.

---

## 8) Archivos completos (rápida referencia)

> Los archivos principales ya están incluidos arriba en secciones separadas. Copia y pega tal cual en las rutas indicadas.
