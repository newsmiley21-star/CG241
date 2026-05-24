<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CG241 - Portail Logistique</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap');
        body { font-family: 'Inter', sans-serif; background-color: #f1f5f9; }
        .custom-scrollbar::-webkit-scrollbar { width: 6px; }
        .custom-scrollbar::-webkit-scrollbar-thumb { background: #cbd5e1; border-radius: 4px; }
    </style>
</head>
<body class="h-screen overflow-hidden flex flex-col">

    <div id="login-screen" class="fixed inset-0 z-[100] bg-slate-800 flex items-center justify-center p-4">
        <div class="bg-white w-full max-w-md rounded-2xl shadow-2xl p-8 text-center">
            <h2 class="text-3xl font-black tracking-wider text-[#0284c7]">CG<span class="text-[#eab308]">241</span></h2>
            <p class="text-xs text-slate-500 font-semibold mb-6">Accès Sécurisé</p>
            <form id="firebase-login-form" class="space-y-4 text-left">
                <div id="login-error" class="hidden bg-red-50 text-red-600 text-[11px] p-3 rounded-lg border border-red-200">Identifiants incorrects.</div>
                <input type="email" id="auth-email" required placeholder="Email" class="w-full bg-slate-50 border p-2 rounded-lg text-xs outline-none focus:border-[#0284c7]">
                <input type="password" id="auth-password" required placeholder="Mot de passe" class="w-full bg-slate-50 border p-2 rounded-lg text-xs outline-none focus:border-[#0284c7]">
                <button type="submit" id="btn-login" class="w-full bg-[#0284c7] text-white font-semibold py-2 rounded-lg text-xs">Se Connecter</button>
            </form>
        </div>
    </div>

    <div id="app-content" class="flex-1 flex flex-col hidden h-screen">
        <header class="bg-white border-b px-6 py-3 flex items-center justify-between">
            <span class="text-xl font-black text-[#0284c7]">CG<span class="text-[#eab308]">241</span></span>
            <button onclick="logout()" class="text-xs bg-red-100 text-red-600 px-3 py-1 rounded">Déconnexion</button>
        </header>

        <div class="flex-1 flex overflow-hidden">
            <aside class="w-20 bg-white border-r flex flex-col items-center py-6 gap-6">
                <i class="fa-solid fa-house text-[#0284c7]"></i>
                <i class="fa-solid fa-box text-slate-400"></i>
                <i class="fa-solid fa-truck text-slate-400"></i>
            </aside>
            <main class="flex-1 p-6 overflow-y-auto" id="main-view">
                </main>
        </div>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.8.1/firebase-app.js";
        import { getAuth, signInWithEmailAndPassword, onAuthStateChanged, signOut } from "https://www.gstatic.com/firebasejs/10.8.1/firebase-auth.js";
        import { getDatabase, ref, get } from "https://www.gstatic.com/firebasejs/10.8.1/firebase-database.js";

        const firebaseConfig = {
            apiKey: "AIzaSyBnFK2ATbYsRZ3RX8bsOYkNEMtk6tsCGsc",
            authDomain: "portail-de-commandes.firebaseapp.com",
            databaseURL: "https://portail-de-commandes-default-rtdb.firebaseio.com",
            projectId: "portail-de-commandes",
            storageBucket: "portail-de-commandes.firebasestorage.app",
            messagingSenderId: "961030938629",
            appId: "1:961030938629:web:0f3d43445b8b358014bc6c"
        };

        const app = initializeApp(firebaseConfig);
        const auth = getAuth(app);
        const db = getDatabase(app);

        // --- AUTH LOGIC ---
        onAuthStateChanged(auth, async (user) => {
            if (user) {
                document.getElementById('login-screen').style.display = 'none';
                document.getElementById('app-content').classList.remove('hidden');
                
                const snapshot = await get(ref(db, 'users/' + user.uid));
                const role = snapshot.exists() ? snapshot.val().role : 'livreur';
                window.switchView(role === 'admin' ? 'desktop' : 'mobile');
            } else {
                document.getElementById('login-screen').style.display = 'flex';
                document.getElementById('app-content').classList.add('hidden');
            }
        });

        document.getElementById('firebase-login-form').onsubmit = (e) => {
            e.preventDefault();
            signInWithEmailAndPassword(auth, document.getElementById('auth-email').value, document.getElementById('auth-password').value)
                .catch(() => document.getElementById('login-error').classList.remove('hidden'));
        };

        window.logout = () => signOut(auth);

        // --- PWA & UI LOGIC ---
        window.switchView = (mode) => {
            const main = document.getElementById('main-view');
            main.innerHTML = mode === 'desktop' ? 
                `<h1 class="text-2xl font-bold">Dashboard Admin</h1><div id="map" class="h-64 bg-slate-200 mt-4"></div>` : 
                `<h1 class="text-2xl font-bold">Interface Livreur</h1><button class="bg-blue-500 text-white p-4 mt-4">Scanner Colis</button>`;
            
            if(mode === 'desktop') {
                const map = L.map('map').setView([0.4162, 9.4673], 13);
                L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png').addTo(map);
            }
        };

        // Enregistrement Service Worker PWA
        if ('serviceWorker' in navigator) {
            const swCode = `self.addEventListener('fetch', () => {});`;
            const blob = new Blob([swCode], {type: 'application/javascript'});
            navigator.serviceWorker.register(URL.createObjectURL(blob));
        }
    </script>
</body>
</html>
