🧩 1️⃣ Verifica que tienes instalado lo necesario

Antes de comenzar, asegúrate de tener:

Requisito	Cómo comprobar
Node.js (incluye npm)	En la terminal: node -v y npm -v
MongoDB local o Atlas	Si usas local, asegúrate que el servicio esté corriendo.
Si usas MongoDB Atlas
, ten lista tu conexión (MONGO_URI).
Visual Studio Code (opcional pero recomendado)	Abre tu carpeta del proyecto desde “Archivo → Abrir carpeta”.
📁 2️⃣ Estructura de carpetas (debe verse así)
mern-recetas/
├─ backend/
│  ├─ server.js
│  ├─ /routes
│  ├─ /models
│  └─ .env
│
└─ frontend/
   ├─ package.json
   └─ /src


Si aún no la tienes así, revisa el documento anterior y asegúrate de haber creado todos los archivos.

⚙️ 3️⃣ Instala las dependencias
🔹 Backend

Abre una terminal en la carpeta raíz (mern-recetas) y escribe:

cd backend
npm install


Esto instalará Express, Mongoose, CORS y Dotenv (si los agregaste en tu package.json).

Si ves un error, revisa que el archivo package.json esté dentro de backend/.

🔹 Frontend

Abre otra terminal (o usa cd .. para regresar a la raíz) y ejecuta:

cd frontend
npm install


Esto instalará las dependencias de React.

🔐 4️⃣ Crea tu archivo .env en el backend

Dentro de la carpeta backend, crea un archivo llamado .env (sin nombre adicional) con el siguiente contenido:

PORT=5000
MONGO_URI=mongodb://localhost:27017/recetas


Si usas MongoDB Atlas, reemplaza la URI con tu conexión:

MONGO_URI=mongodb+srv://usuario:contraseña@cluster0.mongodb.net/recetas


Guarda el archivo.

🧠 5️⃣ Inicia el servidor (backend)

Desde tu carpeta backend, ejecuta:

npm start


o si tienes nodemon instalado:

npm run dev


Deberías ver en la terminal algo como:

Conectado a MongoDB
Servidor escuchando en puerto 5000


✅ Esto significa que tu API está funcionando en:

http://localhost:5000/


Puedes probar en tu navegador:

http://localhost:5000/api/recipes


Debería mostrar [] si no hay recetas aún.

🎨 6️⃣ Inicia el cliente (frontend)

Abre otra terminal (sin cerrar la del backend) y ejecuta:

cd frontend
npm start


Esto abrirá tu aplicación en el navegador, normalmente en:

http://localhost:3000


Deberías ver el título "Mi Libro de Recetas", el formulario para crear recetas y la lista vacía al inicio.

🚀 7️⃣ Guarda una receta de prueba

En el formulario, escribe por ejemplo:

Nombre: Galletas de chocolate 🍪

Ingredientes: harina, azúcar, mantequilla, chocolate

Instrucciones: Mezclar todo, hornear 20 min.

Presiona “Guardar receta”.

Si todo funciona, verás el mensaje “Receta guardada con éxito” y aparecerá en la lista de abajo.

🔄 8️⃣ Ejecutar ambos al mismo tiempo (opcional pero útil)

Si no quieres abrir dos terminales, puedes usar concurrently en la raíz.

Desde la carpeta raíz (mern-recetas/), ejecuta:

npm init -y
npm install -D concurrently


Luego, abre o crea un package.json en la raíz con este script:

"scripts": {
  "dev": "concurrently \"cd backend && npm run dev\" \"cd frontend && npm start\""
}


Y ahora, con un solo comando:

npm run dev


Esto iniciará backend + frontend a la vez 😎.

💡 9️⃣ Posibles errores y soluciones rápidas
Error	Causa posible	Solución
Error: Cannot find module 'express'	No instalaste dependencias	Corre npm install en backend
MongoNetworkError	MongoDB no está encendido	Asegúrate que Mongo corra (mongod) o usa Atlas
CORS	React no puede conectar con backend	En backend/server.js asegúrate de usar app.use(cors())
fetch failed	API caída o URL incorrecta	Revisa que el backend esté en puerto 5000
🧁 10️⃣ ¡Listo! 🎉

Tu aplicación ahora debería funcionar perfectamente:

Guarda recetas desde el formulario.

Las lista automáticamente debajo.

Puedes cerrar y volver a abrir, y las recetas seguirán guardadas en tu base de datos.
