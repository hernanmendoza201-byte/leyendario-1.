<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Leyendario de Valledupar</title>
    
    <!-- Carga de Fuentes de Google: Playfair Display (Serif para Títulos) y Lato (Sans-Serif para Cuerpo) -->
    <link href="https://fonts.googleapis.com/css2?family=Lato:wght@300;400;700&family=Playfair+Display:wght@700;900&display=swap" rel="stylesheet">
    
    <!-- Carga de Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    // Paleta de Colores Solicitada
                    colors: {
                        'deep-blue': '#1A1A2E', // Fondo Gris Oscuro/Azul Medianoche
                        'sepia-parchment': '#F4EBD9', // Fondo de Fichas (Beige/Sepia)
                        'indigo-accent': '#6A0DAD', // Acento Primario (Morado/Índigo)
                        // Acento Secundario (Botones/Interacción) - CAMBIO a Dorado Enriquecido
                        'rich-gold': '#FFBF00', 
                    },
                    // Tipografía Solicitada
                    fontFamily: {
                        title: ['"Playfair Display"', 'serif'],
                        body: ['Lato', 'sans-serif'],
                    },
                }
            }
        }
    </script>
    <style>
        /* Aplicar el fondo principal y la fuente del cuerpo */
        body {
            background-color: #1A1A2E; /* deep-blue */
            font-family: 'Lato', sans-serif; /* body */
            color: #F4EBD9; /* sepia-parchment */
        }
        
        /* Estilos para las tarjetas de leyendas */
        .legend-card {
            transition: transform 0.3s, box-shadow 0.3s;
            cursor: pointer;
            border: 1px solid rgba(106, 13, 173, 0.5); /* Borde sutil Indigo */
        }
        .legend-card:hover {
            transform: translateY(-8px) scale(1.02);
            /* Sombra de neón turquesa C A M B I A D A a Dorado */
            box-shadow: 0 0 30px rgba(255, 191, 0, 0.4); 
        }
        /* Estilo para que las imágenes sean más nítidas y se destaquen */
        .legend-image {
             filter: brightness(0.95) contrast(1.1);
        }
        
        /* Contenedor de historias con scroll personalizado para estética oscura */
        .story-container {
            max-height: 500px;
            overflow-y: auto;
            border-top: 1px solid rgba(106, 13, 173, 0.5);
            padding-top: 1rem;
        }
        
        /* Estilo del botón de enviar con efecto dorado */
        #story-form button[type="submit"] {
            box-shadow: 0 0 10px rgba(255, 191, 0, 0.5); /* Sombra inicial dorada */
            transition: all 0.2s;
        }
        #story-form button[type="submit"]:hover {
            box-shadow: 0 0 20px #FFBF00, 0 0 40px #FFBF00; /* Efecto brillo dorado */
            color: #1A1A2E; /* Texto oscuro sobre dorado */
        }
    </style>
</head>
<body class="font-body">

    <div id="app" class="min-h-screen flex flex-col">

        <!-- Encabezado Principal -->
        <header class="bg-deep-blue text-white shadow-xl p-6 border-b-4 border-indigo-accent">
            <h1 class="text-5xl font-title font-bold text-rich-gold mb-2 tracking-wider">LEYENDARIO VALLENATO</h1>
            <p class="text-center text-sepia-parchment/80">Mitos y Leyendas de Valledupar</p>
        </header>

        <!-- Contenedor principal de la aplicación -->
        <main class="flex-grow p-4 sm:p-8">

            <!-- Vista de Cuadrícula (Principal) -->
            <div id="grid-view" class="space-y-10">
                <div class="flex flex-col sm:flex-row items-center justify-between gap-4 mb-8">
                    <h2 class="text-3xl font-title font-semibold text-indigo-accent">Explora las Leyendas</h2>
                    <!-- Buscador de Términos -->
                    <input type="text" id="search-input" oninput="filterLegends()" placeholder="Buscar leyenda o mito..."
                        class="p-3 border border-indigo-accent/50 rounded-xl w-full sm:w-80 shadow-inner bg-deep-blue text-sepia-parchment placeholder-gray-500 focus:ring-2 focus:ring-rich-gold transition duration-150">
                </div>

                <!-- Cuadrícula de Leyendas -->
                <div id="legends-grid" class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-8">
                    <!-- Las tarjetas de las leyendas se inyectarán aquí -->
                </div>
            </div>

            <!-- Vista de Detalle (Oculta por defecto) -->
            <div id="detail-view" class="hidden">
                <button onclick="showGridView()" class="mb-8 inline-flex items-center text-rich-gold hover:text-indigo-accent transition duration-150 font-bold tracking-wider">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6 mr-2" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                        <path stroke-linecap="round" stroke-linejoin="round" d="M10 19l-7-7m0 0l7-7m-7 7h18" />
                    </svg>
                    Volver al Leyendario
                </button>

                <div id="legend-detail-content" class="bg-sepia-parchment p-6 rounded-2xl shadow-2xl text-deep-blue">
                    <!-- El contenido de la leyenda se inyectará aquí -->
                </div>
            </div>

        </main>

        <!-- Pie de página con firma -->
        <footer class="bg-deep-blue text-sepia-parchment/60 p-4 text-center text-sm mt-10 border-t border-indigo-accent/50">
            <p>&copy; 2025 Leyendario Vallenato | Creado por Neret_z</p>
        </footer>

    </div>

    <!-- Carga de Firebase SDKs -->
    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, signInAnonymously, signInWithCustomToken, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { getFirestore, doc, onSnapshot, collection, query, where, serverTimestamp, addDoc } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";
        import { setLogLevel } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

        // Configuración Firebase (Variables globales provistas por el entorno)
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';
        const firebaseConfig = typeof __firebase_config !== 'undefined' ? JSON.parse(__firebase_config) : {};
        const initialAuthToken = typeof __initial_auth_token !== 'undefined' ? __initial_auth_token : null;

        let db, auth, userId = null;
        let isAuthReady = false;
        let unsubscribeStories = null; // Para gestionar el listener de Firestore

        // MOCK DATA: Información de las leyendas de Valledupar
        const LEGENDS_DATA = [
            {
                id: 'sirena-hurtado',
                name: 'La Sirena de Hurtado',
                imagePlaceholder: 'https://imagenes2.eltiempo.com/files/image_1200_600/uploads/2018/04/27/5ae353fbbd809.jpeg', 
                galleryImages: [
                    'https://mir-s3-cdn-cf.behance.net/project_modules/1400/3ab91d74419427.5c2f3b9bbdcd5.jpg', 
                    'https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQCzcTOXGjtcSE24a4iFljaYyVe6A_3JHq4ag&s' 
                ],
                officialText: 'La Sirena de Hurtado es quizás la leyenda más icónica de Valledupar, ligada al río Guatapurí. Cuentan que era una joven muy hermosa que desobedecía a sus padres para ir al río en Viernes Santo. Como castigo, fue convertida en sirena, y su canto es tan hermoso como melancólico, anunciando el destino de quienes se atreven a escucharla a orillas del río.',
                sourcedAccounts: [
                    { username: 'RioLover98', story: 'Mi abuela me contó que de niño vio algo brillante en la orilla cerca del balneario. Dice que era ella, peinándose con un peine de oro. Nunca se atrevió a mirarla dos veces.' },
                    { username: 'HistoriasVallenatas', story: 'En un foro de Reddit, alguien dijo que un pescador la escuchó cantar una noche y al día siguiente encontró una perla gigante, pero perdió el habla. El precio de la belleza, supongo.' }
                ]
            },
            {
                id: 'el-hombre-caiman',
                name: 'El Hombre Caimán',
                imagePlaceholder: 'https://www.todacolombia.com/imagenes/folclor/mitos-y-leyendas/Leyenda_El_Hombre_Caiman_1366x768.jpg', // Portada Hombre Caimán
                galleryImages: [
                    'https://s3.amazonaws.com/rtvc-assets-misenal.tv/ms-public/inline-images/Mitos-colombianos-Hombre-caiman-ilustrado.jpg', // Galería 1 (Ilustración)
                    'https://i.pinimg.com/736x/f6/1b/42/f61b42043a6dd4824675d6c555a77e58.jpg' // Galería 2 (Ilustración)
                ],
                officialText: 'Aunque más asociada a Plato, Magdalena, la cercanía con Valledupar hace que el Hombre Caimán sea parte de la tradición oral. Se dice que un hombre, por espiar a mujeres, tomó una pócima para convertirse en caimán, pero por un error en la dosis, quedó mitad hombre y mitad caimán, condenado a vivir en los ríos.',
                sourcedAccounts: [
                    { username: 'MitosCosteños', story: 'Un amigo de mi tío asegura haber visto escamas raras en el río Cesar, cerca de la desembocadura. Pensó que era un caimán grande, pero la forma en que se movía era demasiado humana. No volvió a pescar solo.' },
                    { username: 'LeyendaFan', story: 'Leí en un viejo libro que su lamento se parece al de un niño. Es su parte humana pidiendo ayuda, pero nadie se atreve a acercarse por miedo a su parte animal.' }
                ]
            },
            {
                id: 'el-santo-eccehomo',
                name: 'El Santo Eccehomo',
                imagePlaceholder: 'https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcRkGsczufsB1TEM8BXhZ0CbrgUZISoHC6_qDg&s', // Portada Eccehomo
                galleryImages: [
                    'https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcSVFKZkrAvUyCuXQPjq9EU-V_N2zOrovxYiGA&s', // Galería 1
                    'https://panoramacultural.com.co/media/images/articulos/2019/04/15060245.jpg' // Galería 2
                ],
                officialText: 'El Santo Eccehomo es la devoción religiosa más arraigada en Valledupar, venerado especialmente en Patillal y en la Catedral de la ciudad. Aunque es una tradición de fe y no un "mito" como tal, su historia de milagros es parte esencial del leyendario local. La imagen, que representa a Jesús atado y coronado de espinas, es considerada milagrosa y protectora, y su festividad es un momento de gran fervor popular.',
                sourcedAccounts: [
                    { username: 'DevotoPatillal', story: 'Mi familia siempre ha ido a Patillal a pagar promesas. Una vez, mi hijo estaba muy enfermo y le rezamos al Santo. Al día siguiente, empezó a mejorar milagrosamente. Su poder es innegable.' },
                    { username: 'FeVallenata', story: 'En la Catedral, hay una placa que relata cómo la ciudad se salvó de una sequía histórica después de que la imagen fue sacada en procesión. El Santo Eccehomo es el guardián de Valledupar.' }
                ]
            },
            {
                id: 'la-doroy', 
                name: 'La Doroy', 
                imagePlaceholder: 'https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcSMEQwVQnNzLhTT7QfMHJ-bBZOzWMce4gl8iw&s', // Portada Doroy
                galleryImages: [
                    'https://spheraimpacta.com/wp-content/uploads/2025/01/Doroy-y-los-vientos-navidenos-Gal8.webp', // Galería 1
                    'https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcRAetjvyPfHe-Xm-E5_A98ZryRLJ1B08Ze3Lg&s' // Galería 2
                ],
                officialText: 'La Doroy es una leyenda ancestral de la Sierra Nevada, conocida por las comunidades indígenas como una gigantesca serpiente o dragón de agua. Se le considera la protectora y guardiana de los tesoros y del equilibrio hídrico de la sierra. No solo custodia riquezas, sino que también es responsable de fenómenos naturales intensos; su furia puede desatar tormentas o derrumbes, siendo un símbolo del poder indomable de la naturaleza.', 
                sourcedAccounts: [
                    { username: 'TesoroEscondido', story: 'Un tío me dijo que unos guaqueros intentaron subir a una zona prohibida y al bajar, juraron que vieron un rastro de escamas gigantesco en el suelo. No encontraron oro, solo el miedo.' },
                    { username: 'ArhuacoTradicion', story: 'En la parte alta, los Mamos dicen que La Doroy es un espíritu que no debe ser perturbado. Cuando los ríos bajan con mucha fuerza, es porque ella se está moviendo, manteniendo el equilibrio de todo lo que fluye.' } 
                ]
            }
        ];

        let legendsWithUserStories = LEGENDS_DATA.map(l => ({ ...l, userStories: [] }));
        let currentLegendId = null;


        // Función de inicialización de Firebase y Autenticación
        async function initialize() {
            try {
                // Configuración de Logging de Firestore
                setLogLevel('Debug');

                const app = initializeApp(firebaseConfig);
                db = getFirestore(app);
                auth = getAuth(app);

                // Manejo de Autenticación
                if (initialAuthToken) {
                    await signInWithCustomToken(auth, initialAuthToken);
                } else {
                    await signInAnonymously(auth);
                }

                // Esperar a que el estado de autenticación cambie
                onAuthStateChanged(auth, (user) => {
                    if (user) {
                        userId = user.uid;
                    } else {
                        // Generar un ID de usuario anónimo temporal si la autenticación falla
                        userId = crypto.randomUUID();
                    }
                    isAuthReady = true;
                    console.log(`Firebase inicializado. UserID: ${userId}`);
                    renderGridView(); // Renderizar después de la inicialización de Firebase
                });

            } catch (error) {
                console.error("Error al inicializar Firebase o autenticación:", error);
                // Asegurar la renderización incluso si Firebase falla
                isAuthReady = true;
                renderGridView();
            }
        }

        // --- Lógica de Vistas y Renderización ---

        // 1. Renderizar la Vista de Cuadrícula
        function renderGridView() {
            const gridContainer = document.getElementById('legends-grid');
            const searchInput = document.getElementById('search-input').value.toLowerCase();
            gridContainer.innerHTML = '';
            
            legendsWithUserStories
                .filter(legend => legend.name.toLowerCase().includes(searchInput))
                .forEach(legend => {
                const card = document.createElement('div');
                card.className = 'legend-card bg-sepia-parchment rounded-xl overflow-hidden shadow-lg hover:shadow-2xl transition duration-300 ease-in-out';
                card.setAttribute('onclick', `showDetailView('${legend.id}')`);
                card.innerHTML = `
                    <img src="${legend.imagePlaceholder}" alt="Imagen de ${legend.name}" class="legend-image w-full h-48 object-cover object-center border-b-4 border-indigo-accent" onerror="this.onerror=null;this.src='https://placehold.co/400x300/ccc/333?text=No+Imagen';" />
                    <div class="p-4 text-deep-blue">
                        <h3 class="text-2xl font-title font-bold text-indigo-accent text-center">${legend.name}</h3>
                    </div>
                `;
                gridContainer.appendChild(card);
            });

            document.getElementById('grid-view').classList.remove('hidden');
            document.getElementById('detail-view').classList.add('hidden');
        }

        // 2. Función de Búsqueda y Filtrado
        function filterLegends() {
            renderGridView(); // Re-renderiza la cuadrícula con el filtro aplicado.
        }
        window.filterLegends = filterLegends; // Exponer al ámbito global para oninput

        // 3. Mostrar Vista de Detalle
        function showDetailView(legendId) {
            currentLegendId = legendId;
            const legend = legendsWithUserStories.find(l => l.id === legendId);
            if (!legend) return;

            // 1. Renderizar el contenido base (galería, texto oficial, relatos pre-cargados)
            const detailContent = document.getElementById('legend-detail-content');
            detailContent.innerHTML = `
                <h2 class="text-5xl font-title font-extrabold text-indigo-accent mb-6 border-b-4 border-indigo-accent pb-2">${legend.name}</h2>
                
                <!-- Galería de Imágenes -->
                <div class="grid grid-cols-1 sm:grid-cols-2 gap-4 mb-8">
                    <img src="${legend.galleryImages[0]}" alt="Galería ${legend.name} 1" class="w-full h-auto rounded-lg shadow-md object-cover object-center aspect-video" onerror="this.onerror=null;this.src='https://placehold.co/800x600/ccc/333?text=Galeria+1';">
                    <img src="${legend.galleryImages[1]}" alt="Galería ${legend.name} 2" class="w-full h-auto rounded-lg shadow-md object-cover object-center aspect-video" onerror="this.onerror=null;this.src='https://placehold.co/800x600/ccc/333?text=Galeria+2';">
                </div>

                <!-- Texto Oficial (Relato de Libros/Wikis) -->
                <section class="mb-10 p-6 bg-sepia-parchment/70 rounded-xl shadow-inner border border-indigo-accent/30">
                    <h3 class="text-3xl font-title font-semibold text-deep-blue mb-3">El Relato Oficial</h3>
                    <p class="text-lg leading-relaxed text-deep-blue/90">${legend.officialText}</p>
                </section>

                <!-- Relatos Personales (Sourced y de Usuario) -->
                <section class="mb-10 text-deep-blue">
                    <h3 class="text-3xl font-title font-semibold text-indigo-accent mb-4">Relatos de la Comunidad</h3>
                    <div id="personal-stories" class="story-container space-y-4">
                        <!-- Relatos Sourced (MOCK DATA) -->
                        ${legend.sourcedAccounts.map(account => `
                            <div class="p-4 bg-yellow-100 border-l-4 border-amber-500 rounded-lg shadow-sm">
                                <p class="font-bold text-sm text-amber-700 mb-1">Usuario: ${account.username}</p>
                                <p class="italic text-gray-800">"${account.story}"</p>
                            </div>
                        `).join('')}
                        <!-- Aquí se añadirán las historias de Firestore -->
                    </div>
                </section>

                <!-- Formulario de Contribución del Usuario -->
                <section class="p-6 bg-indigo-accent/10 rounded-2xl shadow-lg border border-indigo-accent">
                    <h3 class="text-3xl font-title font-bold text-indigo-accent mb-4">¡Comparte tu Propia Leyenda!</h3>
                    <form id="story-form" onsubmit="submitPersonalStory(event)">
                        <div class="mb-4">
                            <label for="userName" class="block text-sm font-medium text-deep-blue mb-1">Tu Nombre o Usuario:</label>
                            <input type="text" id="userName" required class="p-3 border border-indigo-accent/50 rounded-lg w-full shadow-sm bg-white focus:border-rich-gold focus:ring focus:ring-rich-gold/50 transition" placeholder="Ej: LeyenderoAnónimo">
                        </div>
                        <div class="mb-6">
                            <label for="userStory" class="block text-sm font-medium text-deep-blue mb-1">Tu Relato Personal:</label>
                            <textarea id="userStory" rows="4" required class="p-3 border border-indigo-accent/50 rounded-lg w-full shadow-sm bg-white focus:border-rich-gold focus:ring focus:ring-rich-gold/50 transition" placeholder="Cuentanos tu experiencia o lo que has oído..."></textarea>
                        </div>
                        <button type="submit" class="w-full bg-rich-gold text-deep-blue font-bold py-3 rounded-lg hover:bg-white transition duration-300 transform hover:scale-[1.01] shadow-md hover:shadow-xl">
                            Enviar mi Relato
                        </button>
                        <p id="message-box" class="mt-3 text-sm text-center text-red-700 hidden"></p>
                    </form>
                </section>
            `;

            // 2. Mostrar la vista de detalle y ocultar la de cuadrícula
            document.getElementById('grid-view').classList.add('hidden');
            document.getElementById('detail-view').classList.remove('hidden');

            // 3. Iniciar el listener de Firestore para las historias del usuario
            setupStoriesListener(legendId);
        }
        window.showDetailView = showDetailView; // Exponer al ámbito global para onclick

        // 4. Volver a la Vista de Cuadrícula
        function showGridView() {
            if (unsubscribeStories) {
                unsubscribeStories(); // Detener el listener de Firestore
                unsubscribeStories = null;
            }
            currentLegendId = null;
            renderGridView();
        }
        window.showGridView = showGridView; // Exponer al ámbito global para onclick

        // --- Funciones de Firebase/Firestore ---

        // 5. Configurar Listener para Historias de Usuarios
        function setupStoriesListener(legendId) {
            if (!isAuthReady || !userId || !db) {
                console.error("Firestore no está listo. No se puede adjuntar listener.");
                return;
            }

            // Si hay un listener activo, lo detenemos primero
            if (unsubscribeStories) {
                unsubscribeStories();
            }

            // La colección debe ser pública para que todos los usuarios la vean
            const storiesCollectionPath = `/artifacts/${appId}/public/data/leyendario-accounts`;
            const q = query(
                collection(db, storiesCollectionPath),
                // Filtrar por la leyenda actual
                where("legendId", "==", legendId)
                // Se ordena en el cliente para evitar el error de índice
            );

            // onSnapshot crea el listener en tiempo real
            unsubscribeStories = onSnapshot(q, (snapshot) => {
                const newStories = [];
                snapshot.forEach((doc) => {
                    newStories.push({ id: doc.id, ...doc.data() });
                });

                // Ordenar los resultados en el cliente (JavaScript) para suplir la eliminación del orderBy en Firestore.
                newStories.sort((a, b) => {
                    // La propiedad timestamp es un objeto Timestamp de Firebase.
                    // Usamos toDate() y getTime() para obtener un valor numérico comparable.
                    // Si el timestamp es nulo (poco probable con serverTimestamp), se usa 0 como fallback.
                    const timeA = a.timestamp?.toDate ? a.timestamp.toDate().getTime() : 0;
                    const timeB = b.timestamp?.toDate ? b.timestamp.toDate().getTime() : 0;
                    return timeA - timeB; // Orden ascendente (más antiguo primero)
                });

                // Actualizar la mock data global con las historias en tiempo real
                const legendIndex = legendsWithUserStories.findIndex(l => l.id === legendId);
                if (legendIndex !== -1) {
                    legendsWithUserStories[legendIndex].userStories = newStories;
                }
                
                // Re-renderizar SÓLO la sección de historias
                renderUserStories(newStories);

            }, (error) => {
                console.error("Error al escuchar historias en Firestore: ", error);
                // Si el error persiste, aún se muestra el mensaje al usuario
                document.getElementById('message-box').textContent = "Error al cargar relatos de usuarios.";
                document.getElementById('message-box').classList.remove('hidden');
            });
        }

        // 6. Renderizar las Historias de Usuarios (real-time update)
        function renderUserStories(stories) {
            const storiesContainer = document.getElementById('personal-stories');
            if (!storiesContainer) return;

            // Primero, mantener los relatos SOURCED (mock data)
            const legend = legendsWithUserStories.find(l => l.id === currentLegendId);
            const sourcedHtml = legend.sourcedAccounts.map(account => `
                <div class="p-4 bg-yellow-100 border-l-4 border-amber-500 rounded-lg shadow-sm">
                    <p class="font-bold text-sm text-amber-700 mb-1">Usuario: ${account.username}</p>
                    <p class="italic text-gray-800">"${account.story}"</p>
                </div>
            `).join('');

            // Luego, añadir las historias de Firestore
            const userStoriesHtml = stories.map(story => `
                <div class="p-4 bg-indigo-accent/5 border-l-4 border-indigo-accent rounded-lg shadow-md text-deep-blue">
                    <p class="font-bold text-sm text-indigo-accent mb-1">Usuario: ${story.userName || 'Anónimo'}</p>
                    <p class="text-gray-800">"${story.story}"</p>
                </div>
            `).join('');

            storiesContainer.innerHTML = sourcedHtml + userStoriesHtml;
            // Scroll al final para ver el nuevo relato inmediatamente
            storiesContainer.scrollTop = storiesContainer.scrollHeight;
        }

        // 7. Manejar el envío del Formulario
        async function submitPersonalStory(event) {
            event.preventDefault();
            const form = document.getElementById('story-form');
            const messageBox = document.getElementById('message-box');
            messageBox.classList.add('hidden');
            messageBox.textContent = '';
            messageBox.classList.remove('text-rich-gold'); // Limpiar color de éxito anterior
            messageBox.classList.add('text-red-700');


            if (!isAuthReady || !db || !currentLegendId) {
                messageBox.textContent = "Error: La aplicación no está lista o la leyenda no está seleccionada.";
                messageBox.classList.remove('hidden');
                return;
            }

            const userName = document.getElementById('userName').value.trim() || 'Anónimo';
            const userStory = document.getElementById('userStory').value.trim();

            if (userStory.length < 10) {
                 messageBox.textContent = "El relato debe tener al menos 10 caracteres.";
                 messageBox.classList.remove('hidden');
                 return;
            }

            try {
                const storiesCollectionPath = `/artifacts/${appId}/public/data/leyendario-accounts`;
                const docRef = await addDoc(collection(db, storiesCollectionPath), {
                    legendId: currentLegendId,
                    userName: userName,
                    story: userStory,
                    userId: userId, // Registrar el ID del usuario que envía
                    timestamp: serverTimestamp() // Usar timestamp del servidor para ordenar
                });

                // Limpiar el formulario después del envío exitoso
                document.getElementById('userStory').value = '';
                
                messageBox.textContent = "¡Gracias por compartir tu relato! Se ha añadido al Leyendario.";
                messageBox.classList.remove('hidden');
                messageBox.classList.remove('text-red-700');
                messageBox.classList.add('text-rich-gold'); // Usar Dorado para mensaje de éxito

            } catch (e) {
                console.error("Error al añadir documento: ", e);
                messageBox.textContent = "Hubo un error al enviar el relato. Por favor, inténtalo de nuevo.";
                messageBox.classList.remove('hidden');
                messageBox.classList.add('text-red-700');
            }
        }
        window.submitPersonalStory = submitPersonalStory; // Exponer al ámbito global para onsubmit


        // Iniciar la aplicación al cargar la página
        window.onload = initialize;
    </script>
</body>
</html>
