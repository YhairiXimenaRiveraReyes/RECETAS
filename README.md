# MERN - App de Recetas (Cómo ejecutarlo)

Este documento explica **cómo ejecutar completamente** la aplicación MERN de recetas, desde instalar dependencias hasta ver el proyecto funcionando.

---

## 🧩 1) Preparar el entorno

Asegúrate de tener instalado:

* **Node.js** (versión 16 o superior)
* **npm** (viene con Node)
* **MongoDB local** o una cuenta en **MongoDB Atlas**

Verifica con:

```bash
node -v
npm -v
```

Si no tienes MongoDB local, usa Atlas y copia la conexión (`MONGO_URI`).

---

## ⚙️ 2) Instalar dependencias

En tu carpeta raíz `mern-recetas/`, asegúrate de tener las carpetas:

```
mern-recetas/
├─ backend/
└─ frontend/
```

### 🟢 Backend

```bash
cd backend
npm install
```

Esto instalará `express`, `mongoose`, `cors`, `dotenv`, etc. según el `package.json`.

### 🟣 Frontend

Desde la raíz del proyecto, entra a la carpeta del cliente:

```bash
cd ../frontend
npm install
```

Esto instalará todas las dependencias de React.

---

## 🧰 3) Configurar variables de entorno

En `backend/`, crea un archivo `.env` (puedes copiar `.env.example`) con tu configuración:

```
PORT=5000
MONGO_URI=mongodb+srv://<usuario>:<contraseña>@cluster0.mongodb.net/recetas?retryWrites=true&w=majority
```

Si usas MongoDB local:

```
MONGO_URI=mongodb://localhost:27017/recetas
```

---

## 🚀 4) Ejecutar el backend

Desde la carpeta `backend/`:

```bash
cd backend
npm run dev
```

(Si usas `nodemon`) o simplemente:

```bash
npm start
```

Si todo está correcto, deberías ver algo como:

```
Conectado a MongoDB
Servidor escuchando en puerto 5000
```

Puedes probar la API abriendo en tu navegador:
👉 [http://localhost:5000](http://localhost:5000)

Debería mostrar: `API de Recetas funcionando`

---

## 💻 5) Ejecutar el frontend

Abre **una nueva terminal** (sin cerrar el backend) y ejecuta:

```bash
cd frontend
npm start
```

Esto abrirá la app React en tu navegador, por defecto en:
👉 [http://localhost:3000](http://localhost:3000)

La app mostrará el formulario para guardar recetas y la lista de recetas guardadas.

---

## ⚡ 6) Ejecutar ambos al mismo tiempo (opcional)

Desde la raíz `mern-recetas/`, instala `concurrently`:

```bash
npm init -y
npm install -D concurrently
```

Agrega este script en el `package.json` de la raíz:

```json
"scripts": {
  "dev": "concurrently \"cd backend && npm run dev\" \"cd frontend && npm start\""
}
```

Luego ejecuta:

```bash
npm run dev
```

Esto levantará **backend y frontend al mismo tiempo**.

---

## 🧠 7) Verificando la conexión

Cuando agregues una receta desde el formulario, debería enviarse al backend y guardarse en MongoDB. Si ves tu receta listada debajo del formulario, ¡todo funciona! 😺

Si quieres verificar manualmente:

* Abre [http://localhost:5000/api/recipes](http://localhost:5000/api/recipes)
* Deberías ver un arreglo JSON con tus recetas.

---

## 🧯 8) Errores comunes y soluciones

| Error                              | Posible causa                                  | Solución                                                                       |
| ---------------------------------- | ---------------------------------------------- | ------------------------------------------------------------------------------ |
| `MongooseError: failed to connect` | URI incorrecta o Mongo no en ejecución         | Revisa `.env` o abre MongoDB Compass para verificar conexión                   |
| `CORS error`                       | Peticiones bloqueadas entre frontend y backend | Asegúrate de usar `app.use(cors())` en `server.js` y que los puertos coincidan |
| `fetch failed` o `404`             | Ruta API incorrecta                            | Verifica que el frontend apunte a `http://localhost:5000/api/recipes`          |
| `Port already in use`              | Puerto ocupado                                 | Cambia el `PORT` en `.env` o cierra procesos anteriores                        |

---

## ✅ 9) Comprobación final

Si ambos servidores están funcionando:

* **Backend:** [http://localhost:5000](http://localhost:5000)
* **Frontend:** [http://localhost:3000](http://localhost:3000)

Y si el formulario guarda correctamente recetas, ¡ya tienes tu app MERN completa funcionando! 🎉

---

## 🌱 10) Siguientes pasos sugeridos

* Añadir funciones para editar y borrar recetas.
* Crear un sistema de usuarios (registro/login).
* Mejorar diseño con **TailwindCSS** o **Material UI**.
* Subir el proyecto a GitHub y desplegarlo en **Render**, **Vercel** o **Railway**.
