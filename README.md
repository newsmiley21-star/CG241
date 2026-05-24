<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <meta name="theme-color" content="#0b1329">
    <title>CG241 Express & Shop - Plateforme de Production</title>
    
    <!-- Moteur Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    
    <!-- Graphiques dynamiques de comptabilité analytique -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;500;600;700&display=swap');
        body {
            font-family: 'Plus Jakarta Sans', sans-serif;
            background-color: #0b1329;
            color: #f8fafc;
            overflow-x: hidden;
        }
        .tab-content { display: none; }
        .tab-content.active { display: block; animation: fadeIn 0.4s ease-out; }
        
        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(5px); }
            to { opacity: 1; transform: translateY(0); }
        }

        /* Personnalisation de la barre de défilement */
        ::-webkit-scrollbar { width: 6px; height: 6px; }
        ::-webkit-scrollbar-track { background: #0b1329; }
        ::-webkit-scrollbar-thumb { background: #1e293b; border-radius: 4px; }
        ::-webkit-scrollbar-thumb:hover { background: #eab308; }

        /* Effet Lock Screen */
        .glass-panel {
            background: rgba(15, 23, 42, 0.7);
            backdrop-filter: blur(12px);
            -webkit-backdrop-filter: blur(12px);
        }
    </style>
</head>
<body class="min-h-screen flex flex-col md:flex-row antialiased relative">

    <!-- ÉCRAN DE VERROUILLAGE (PIN par défaut: 2410) -->
    <div id="lock-screen" class="fixed inset-0 z-[9999] flex items-center justify-center bg-[#0b1329] transition-opacity duration-500">
        <div class="glass-panel p-8 rounded-3xl border border-slate-800 shadow-2xl flex flex-col items-center max-w-sm w-full mx-4">
            <div class="w-16 h-16 bg-amber-500 text-blue-900 rounded-2xl flex items-center justify-center font-bold text-3xl shadow-lg shadow-amber-500/20 mb-6">
                CG
            </div>
            <h2 class="text-2xl font-bold text-white mb-2">Accès Restreint</h2>
            <p class="text-slate-400 text-sm text-center mb-8">Veuillez entrer votre code PIN pour accéder à l'écosystème. <br><span class="text-xs text-amber-500">(Démo: 2410)</span></p>
            
            <div class="flex gap-3 mb-6">
                <input type="password" id="pin-1" maxlength="1" class="w-12 h-14 bg-slate-900 border border-slate-700 rounded-xl text-center text-2xl font-bold text-white focus:border-amber-500 focus:ring-1 focus:ring-amber-500 outline-none transition-all" onkeyup="moveToNext(this, 'pin-2')">
                <input type="password" id="pin-2" maxlength="1" class="w-12 h-14 bg-slate-900 border border-slate-700 rounded-xl text-center text-2xl font-bold text-white focus:border-amber-500 focus:ring-1 focus:ring-amber-500 outline-none transition-all" onkeyup="moveToNext(this, 'pin-3', 'pin-1')">
                <input type="password" id="pin-3" maxlength="1" class="w-12 h-14 bg-slate-900 border border-slate-700 rounded-xl text-center text-2xl font-bold text-white focus:border-amber-500 focus:ring-1 focus:ring-amber-500 outline-none transition-all" onkeyup="moveToNext(this, 'pin-4', 'pin-2')">
                <input type="password" id="pin-4" maxlength="1" class="w-12 h-14 bg-slate-900 border border-slate-700 rounded-xl text-center text-2xl font-bold text-white focus:border-amber-500 focus:ring-1 focus:ring-amber-500 outline-none transition-all" onkeyup="moveToNext(this, null, 'pin-3')">
            </div>
            <button onclick="unlockApp()" class="w-full bg-amber-500 hover:bg-amber-600 text-blue-950 font-bold py-3 rounded-xl transition-all shadow-lg">
                Déverrouiller
            </button>
            <p id="lock-error" class="text-rose-500 text-sm mt-4 hidden font-medium">Code incorrect. Réessayez.</p>
        </div>
    </div>

    <!-- SCRIPT PWA & VERROUILLAGE (Exécuté en premier) -->
    <script>
        // Logique de l'écran de verrouillage
        const PIN_SECRET = "2410";
        
        function moveToNext(current, nextFieldID, prevFieldID) {
            if (current.value.length >= 1 && nextFieldID) {
                document.getElementById(nextFieldID).focus();
            } else if (current.value.length === 0 && prevFieldID && event.key === "Backspace") {
                document.getElementById(prevFieldID).focus();
            }
            if(event.key === "Enter") unlockApp();
        }

        function unlockApp() {
            const p1 = document.getElementById('pin-1').value;
            const p2 = document.getElementById('pin-2').value;
            const p3 = document.getElementById('pin-3').value;
            const p4 = document.getElementById('pin-4').value;
            const pin = p1 + p2 + p3 + p4;

            if (pin === PIN_SECRET) {
                const lockScreen = document.getElementById('lock-screen');
                lockScreen.classList.add('opacity-0');
                setTimeout(() => { lockScreen.style.display = 'none'; }, 500);
            } else {
                document.getElementById('lock-error').classList.remove('hidden');
                setTimeout(() => document.getElementById('lock-error').classList.add('hidden'), 3000);
                ['pin-1', 'pin-2', 'pin-3', 'pin-4'].forEach(id => document.getElementById(id).value = '');
                document.getElementById('pin-1').focus();
            }
        }

        // --- GÉNÉRATION DYNAMIQUE DE LA PWA ---
        // 1. Manifest
        const manifest = {
            name: "CG241 Express & Shop",
            short_name: "CG241",
            start_url: window.location.href,
            display: "standalone",
            background_color: "#0b1329",
            theme_color: "#0b1329",
            description: "Écosystème E-com & Logistique",
            icons: [{
                src: "data:image/svg+xml;utf8,<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'><rect width='100' height='100' fill='%23f59e0b'/><text x='50' y='50' font-family='sans-serif' font-size='40' fill='%231e3a8a' text-anchor='middle' alignment-baseline='middle' font-weight='bold'>CG</text></svg>",
                sizes: "192x192 512x512",
                type: "image/svg+xml"
            }]
        };
        const manifestBlob = new Blob([JSON.stringify(manifest)], { type: 'application/json' });
        const manifestUrl = URL.createObjectURL(manifestBlob);
        const link = document.createElement('link');
        link.rel = 'manifest'; link.href = manifestUrl;
        document.head.appendChild(link);

        // 2. Service Worker (Cache basique pour le mode hors-ligne)
        const swCode = `
            const CACHE_NAME = 'cg241-v1';
            self.addEventListener('install', event => {
                self.skipWaiting();
            });
            self.addEventListener('activate', event => {
                event.waitUntil(self.clients.claim());
            });
            self.addEventListener('fetch', event => {
                event.respondWith(
                    fetch(event.request).catch(() => new Response('Hors ligne. Données mockées actives.', {status: 200, statusText: 'Offline'}))
                );
            });
        `;
        const swBlob = new Blob([swCode], { type: 'application/javascript' });
        const swUrl = URL.createObjectURL(swBlob);
        if ('serviceWorker' in navigator) {
            window.addEventListener('load', () => {
                navigator.serviceWorker.register(swUrl).then(() => {
                    console.log('Service Worker PWA enregistré avec succès.');
                }).catch(err => console.log('Erreur SW:', err));
            });
        }
    </script>

    <!-- BARRE LATÉRALE DE NAVIGATION -->
    <aside class="w-full md:w-68 bg-[#0f172a] border-b md:border-b-0 md:border-r border-slate-800 p-6 flex flex-col justify-between shrink-0">
        <div>
            <!-- Logo Identitaire CG241 -->
            <div class="flex items-center gap-3 mb-8 pb-6 border-b border-slate-800">
                <div class="w-11 h-11 bg-amber-500 text-blue-900 rounded-xl flex items-center justify-center font-bold text-2xl shadow-lg shadow-amber-500/20">
                    CG
                </div>
                <div>
                    <h1 class="font-bold text-lg leading-tight text-white tracking-wide">CG241 <span class="text-amber-500">Express</span></h1>
                    <p class="text-xs text-slate-400">Écosystème E-com & Logistique</p>
                </div>
            </div>

            <!-- Menu Tactique -->
            <nav class="space-y-2">
                <button onclick="window.switchTab('vitrine')" id="btn-vitrine" class="w-full flex items-center gap-3 px-4 py-3 text-sm font-medium rounded-xl text-slate-300 hover:bg-slate-800 hover:text-white transition-all">
                    <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M16 11V7a4 4 0 00-8 0v4M5 9h14l1 12H4L5 9z"/></svg>
                    Vitrine Produits
                </button>
                <button onclick="window.switchTab('admin-dashboard')" id="btn-admin-dashboard" class="w-full flex items-center gap-3 px-4 py-3 text-sm font-medium rounded-xl text-slate-300 hover:bg-slate-800 hover:text-white transition-all">
                    <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 19v-6a2 2 0 00-2-2H5a2 2 0 00-2 2v6a2 2 0 002 2h2a2 2 0 002-2zm0 0V9a2 2 0 012-2h2a2 2 0 012 2v10m-6 0a2 2 0 002 2h2a2 2 0 002-2m0 0V5a2 2 0 012-2h2a2 2 0 012 2v14a2 2 0 01-2 2h-2a2 2 0 01-2-2z"/></svg>
                    Admin & Comptabilité
                </button>
                <button onclick="window.switchTab('livreur-hub')" id="btn-livreur-hub" class="w-full flex items-center gap-3 px-4 py-3 text-sm font-medium rounded-xl text-slate-300 hover:bg-slate-800 hover:text-white transition-all">
                    <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 17a2 2 0 11-4 0 2 2 0 014 0zM19 17a2 2 0 11-4 0 2 2 0 014 0z"/><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 16V6a1 1 0 00-1-1H4a1 1 0 00-1 1v10a1 1 0 001 1h1m8-1a1 1 0 011-1v-2.5a1 1 0 01.293-.707l3.147-3.146A1 1 0 0118.854 8H21a1 1 0 011 1v7a1 1 0 01-1 1h-1m-6 0a1 1 0 001 1h2a1 1 0 001-1m-7 0a1 1 0 001 1h2a1 1 0 001-1"/></svg>
                    Espace Livreurs
                </button>
                <button onclick="window.switchTab('archives')" id="btn-archives" class="w-full flex items-center gap-3 px-4 py-3 text-sm font-medium rounded-xl text-slate-300 hover:bg-slate-800 hover:text-white transition-all">
                    <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 8h14M5 8a2 2 0 110-4h14a2 2 0 110 4M5 8v10a2 2 0 002 2h10a2 2 0 002-2V8m-9 4h4"/></svg>
                    Archives Clôturées
                </button>
            </nav>
        </div>

        <!-- Indicateur de Connexion de Cloud -->
        <div class="mt-6 pt-4 border-t border-slate-800">
            <div class="flex items-center gap-2">
                <span id="cloud-indicator" class="w-2.5 h-2.5 bg-red-500 rounded-full animate-pulse"></span>
                <span id="cloud-text" class="text-xs text-slate-400 font-medium">Connexion en cours...</span>
            </div>
            <button onclick="window.showToast('Système mis à jour !', 'success')" class="mt-4 w-full text-xs text-slate-500 hover:text-white flex items-center justify-center gap-1 transition-colors">
                <svg class="w-3 h-3" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10.325 4.317c.426-1.756 2.924-1.756 3.35 0a1.724 1.724 0 002.573 1.066c1.543-.94 3.31.826 2.37 2.37a1.724 1.724 0 001.065 2.572c1.756.426 1.756 2.924 0 3.35a1.724 1.724 0 00-1.066 2.573c.94 1.543-.826 3.31-2.37 2.37a1.724 1.724 0 00-2.572 1.065c-.426 1.756-2.924 1.756-3.35 0a1.724 1.724 0 00-2.573-1.066c-1.543.94-3.31-.826-2.37-2.37a1.724 1.724 0 00-1.065-2.572c-1.756-.426-1.756-2.924 0-3.35a1.724 1.724 0 001.066-2.573c-.94-1.543.826-3.31 2.37-2.37.996.608 2.296.07 2.572-1.065z"/><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z"/></svg>
                Paramètres Système
            </button>
        </div>
    </aside>

    <!-- ZONE PRINCIPALE DE L'APPLICATION -->
    <main class="flex-1 p-6 md:p-8 overflow-y-auto w-full">

        <!-- ONGLET 1 : VITRINE COMMERCIALE -->
        <section id="tab-vitrine" class="tab-content active space-y-6 max-w-6xl mx-auto">
            <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center gap-4">
                <div>
                    <h2 class="text-2xl font-bold text-white tracking-tight">Catalogue & Vitrine de Vente</h2>
                    <p class="text-sm text-slate-400">Gérez vos articles en stock avec les contraintes d'achat en gros (MOQ).</p>
                </div>
                <button onclick="window.openModal('product-modal')" class="bg-amber-500 hover:bg-amber-600 text-blue-950 font-bold text-sm px-4 py-2.5 rounded-xl shadow-lg transition-all flex items-center gap-2 shrink-0">
                    <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 4v16m8-8H4"/></svg>
                    Ajouter un Article
                </button>
            </div>

            <div id="products-grid" class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-6">
                <!-- Injecté par JavaScript -->
            </div>
        </section>

        <!-- ONGLET 2 : DASHBOARD ADMIN & COMPTABILITÉ -->
        <section id="tab-admin-dashboard" class="tab-content space-y-6 max-w-6xl mx-auto">
            <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center gap-4">
                <div>
                    <h2 class="text-2xl font-bold text-white tracking-tight">Direction Administrative</h2>
                    <p class="text-sm text-slate-400">Suivi rigoureux des indicateurs analytiques.</p>
                </div>
                <button onclick="window.exportDataBackup()" class="bg-slate-800 hover:bg-slate-700 border border-slate-700 text-white font-semibold text-sm px-4 py-2.5 rounded-xl transition-all flex items-center gap-2 shrink-0">
                    <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-4l-4 4m0 0l-4-4m4 4V4"/></svg>
                    Sauvegarder les données
                </button>
            </div>

            <div class="grid grid-cols-1 sm:grid-cols-3 gap-4">
                <div class="bg-[#0f172a] border border-slate-800 rounded-2xl p-5 shadow-sm">
                    <span class="text-xs font-semibold uppercase text-slate-400 tracking-wider">Chiffre d'Affaires Brut</span>
                    <div class="text-2xl font-bold text-emerald-400 mt-1" id="admin-ca">0 FCFA</div>
                </div>
                <div class="bg-[#0f172a] border border-slate-800 rounded-2xl p-5 shadow-sm">
                    <span class="text-xs font-semibold uppercase text-slate-400 tracking-wider">Coûts Logistiques</span>
                    <div class="text-2xl font-bold text-rose-400 mt-1" id="admin-co">0 FCFA</div>
                </div>
                <div class="bg-[#0f172a] border border-slate-800 rounded-2xl p-5 shadow-sm">
                    <span class="text-xs font-semibold uppercase text-slate-400 tracking-wider">Marge Nette (Bénéfice)</span>
                    <div class="text-2xl font-bold text-amber-400 mt-1" id="admin-marge">0 FCFA</div>
                </div>
            </div>

            <div class="bg-[#0f172a] border border-slate-800 rounded-2xl p-6 shadow-sm">
                <h3 class="font-bold text-base text-white mb-4">Aperçu Visuel de Rentabilité Réelle</h3>
                <div class="h-64 relative w-full">
                    <canvas id="financialChart"></canvas>
                </div>
            </div>

            <div class="bg-[#0f172a] border border-slate-800 rounded-2xl overflow-hidden shadow-sm">
                <div class="px-6 py-4 bg-slate-900/40 border-b border-slate-800 flex justify-between items-center">
                    <h3 class="font-bold text-base text-white">Création & Supervision des Expéditions</h3>
                    <button onclick="window.openModal('order-modal')" class="bg-blue-600 hover:bg-blue-700 text-white font-semibold text-xs px-3 py-2 rounded-lg transition-all">
                        + Nouvelle Mission
                    </button>
                </div>
                <div class="overflow-x-auto w-full">
                    <table class="w-full text-left border-collapse min-w-[800px]">
                        <thead>
                            <tr class="border-b border-slate-800 text-[11px] font-semibold uppercase text-slate-400 bg-slate-900/60">
                                <th class="py-3 px-6">ID Mission</th>
                                <th class="py-3 px-6">Client / Destination</th>
                                <th class="py-3 px-6">Prix Vente</th>
                                <th class="py-3 px-6">Coût Transport</th>
                                <th class="py-3 px-6 text-amber-400">Code Secret Validation</th>
                                <th class="py-3 px-6">Statut</th>
                            </tr>
                        </thead>
                        <tbody id="admin-orders-tbody" class="text-sm divide-y divide-slate-800/50">
                            <!-- Injecté par JS -->
                        </tbody>
                    </table>
                </div>
            </div>
        </section>

        <!-- ONGLET 3 : HUB DES LIVREURS -->
        <section id="tab-livreur-hub" class="tab-content space-y-6 max-w-6xl mx-auto">
            <div>
                <h2 class="text-2xl font-bold text-white tracking-tight">Tableau de Mission des Livreurs</h2>
                <p class="text-sm text-slate-400">Interface de terrain. Demandez le code secret à l'administration pour valider la livraison.</p>
            </div>
            <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6" id="livreur-missions-container">
                <!-- Injecté dynamiquement -->
            </div>
        </section>

        <!-- ONGLET 4 : ARCHIVES CLÔTURÉES -->
        <section id="tab-archives" class="tab-content space-y-6 max-w-6xl mx-auto">
            <div>
                <h2 class="text-2xl font-bold text-white tracking-tight">Archives Historiques</h2>
                <p class="text-sm text-slate-400">Toutes les missions validées et clôturées.</p>
            </div>
            <div class="bg-[#0f172a] border border-slate-800 rounded-2xl overflow-hidden shadow-sm">
                <div class="overflow-x-auto w-full">
                    <table class="w-full text-left border-collapse min-w-[700px]">
                        <thead>
                            <tr class="border-b border-slate-800 text-[11px] font-semibold uppercase text-slate-400 bg-slate-900/60">
                                <th class="py-3 px-6">ID Archive</th>
                                <th class="py-3 px-6">Client & Zone</th>
                                <th class="py-3 px-6">CA Encaissé</th>
                                <th class="py-3 px-6">Coût Solder</th>
                                <th class="py-3 px-6 text-emerald-400">Statut Historique</th>
                            </tr>
                        </thead>
                        <tbody id="archives-tbody" class="text-sm divide-y divide-slate-800/50">
                            <!-- Injecté par JS -->
                        </tbody>
                    </table>
                </div>
            </div>
        </section>
    </main>

    <!-- ================= MODALS & TOASTS ================= -->

    <!-- TOAST NOTIFICATIONS CONTENEUR -->
    <div id="toast-container" class="fixed bottom-4 right-4 z-[9999] flex flex-col gap-2 pointer-events-none"></div>

    <!-- MODAL CONFIRMATION CUSTOM (Remplace alert/confirm) -->
    <div id="confirm-modal" class="fixed inset-0 bg-black/80 backdrop-blur-sm flex items-center justify-center p-4 hidden z-[8000]">
        <div class="bg-[#0f172a] border border-slate-800 rounded-2xl w-full max-w-sm overflow-hidden shadow-2xl p-6 text-center">
            <h3 class="font-bold text-white text-lg mb-2" id="confirm-title">Confirmation</h3>
            <p class="text-slate-400 text-sm mb-6" id="confirm-msg">Êtes-vous sûr ?</p>
            <div class="flex gap-3 justify-center">
                <button onclick="window.closeConfirmModal()" class="px-4 py-2 bg-slate-800 text-slate-300 rounded-xl text-sm font-medium hover:bg-slate-700">Annuler</button>
                <button id="confirm-action-btn" class="px-4 py-2 bg-rose-500 text-white rounded-xl text-sm font-bold hover:bg-rose-600">Confirmer</button>
            </div>
        </div>
    </div>

    <!-- MODAL : AJOUT PRODUIT -->
    <div id="product-modal" class="fixed inset-0 bg-black/80 backdrop-blur-sm flex items-center justify-center p-4 hidden z-50">
        <div class="bg-[#0f172a] border border-slate-800 rounded-2xl w-full max-w-md overflow-hidden shadow-2xl">
            <div class="px-6 py-4 border-b border-slate-800 flex justify-between items-center bg-slate-900/50">
                <h3 class="font-bold text-white">Ajouter un Article</h3>
                <button onclick="window.closeModal('product-modal')" class="text-slate-400 hover:text-white text-xl">&times;</button>
            </div>
            <form id="product-form" onsubmit="window.saveProduct(event)" class="p-6 space-y-4">
                <div>
                    <label class="block text-xs font-semibold text-slate-400 uppercase tracking-wider mb-1">Nom du Produit</label>
                    <input type="text" id="p-name" required class="w-full bg-slate-900 border border-slate-800 rounded-xl px-3 py-2 text-white text-sm focus:outline-none focus:border-amber-500">
                </div>
                <div class="grid grid-cols-2 gap-4">
                    <div>
                        <label class="block text-xs font-semibold text-slate-400 uppercase tracking-wider mb-1">Prix U. (FCFA)</label>
                        <input type="number" id="p-price" required class="w-full bg-slate-900 border border-slate-800 rounded-xl px-3 py-2 text-white text-sm focus:outline-none focus:border-amber-500">
                    </div>
                    <div>
                        <label class="block text-xs font-semibold text-slate-400 uppercase tracking-wider mb-1">Stock dispo</label>
                        <input type="number" id="p-stock" required class="w-full bg-slate-900 border border-slate-800 rounded-xl px-3 py-2 text-white text-sm focus:outline-none focus:border-amber-500">
                    </div>
                </div>
                <div>
                    <label class="block text-xs font-semibold text-slate-400 uppercase tracking-wider mb-1">MOQ (Qté Minimum)</label>
                    <input type="number" id="p-moq" value="1" required class="w-full bg-slate-900 border border-slate-800 rounded-xl px-3 py-2 text-white text-sm focus:outline-none focus:border-amber-500">
                </div>
                <div>
                    <label class="block text-xs font-semibold text-slate-400 uppercase tracking-wider mb-1">Photo (Fichier Local)</label>
                    <input type="file" id="p-image" accept="image/*" required class="w-full bg-slate-900 border border-slate-800 rounded-xl px-3 py-2 text-xs text-slate-400 file:mr-4 file:py-1 file:px-2 file:rounded-lg file:border-0 file:text-xs file:font-semibold file:bg-amber-500 file:text-blue-950 hover:file:bg-amber-600">
                </div>
                <div class="pt-4 border-t border-slate-800 flex justify-end gap-2">
                    <button type="button" onclick="window.closeModal('product-modal')" class="px-4 py-2 bg-slate-800 text-slate-300 rounded-xl text-xs font-medium">Annuler</button>
                    <button type="submit" class="px-4 py-2 bg-amber-500 text-blue-950 rounded-xl text-xs font-bold hover:bg-amber-600">Enregistrer</button>
                </div>
            </form>
        </div>
    </div>

    <!-- MODAL : CRÉATION MISSION -->
    <div id="order-modal" class="fixed inset-0 bg-black/80 backdrop-blur-sm flex items-center justify-center p-4 hidden z-50">
        <div class="bg-[#0f172a] border border-slate-800 rounded-2xl w-full max-w-md overflow-hidden shadow-2xl">
            <div class="px-6 py-4 border-b border-slate-800 flex justify-between items-center bg-slate-900/50">
                <h3 class="font-bold text-white">Ordre de Mission</h3>
                <button onclick="window.closeModal('order-modal')" class="text-slate-400 hover:text-white text-xl">&times;</button>
            </div>
            <form id="order-form" onsubmit="window.saveOrder(event)" class="p-6 space-y-4">
                <div>
                    <label class="block text-xs font-semibold text-slate-400 uppercase tracking-wider mb-1">Destinataire</label>
                    <input type="text" id="o-customer" required class="w-full bg-slate-900 border border-slate-800 rounded-xl px-3 py-2 text-white text-sm focus:outline-none focus:border-amber-500">
                </div>
                <div>
                    <label class="block text-xs font-semibold text-slate-400 uppercase tracking-wider mb-1">Zone (Quartier)</label>
                    <input type="text" id="o-zone" required placeholder="Ex: Akanda, Owendo" class="w-full bg-slate-900 border border-slate-800 rounded-xl px-3 py-2 text-white text-sm focus:outline-none focus:border-amber-500">
                </div>
                <div class="grid grid-cols-2 gap-4">
                    <div>
                        <label class="block text-xs font-semibold text-slate-400 uppercase tracking-wider mb-1">Prix Course (FCFA)</label>
                        <input type="number" id="o-price" required class="w-full bg-slate-900 border border-slate-800 rounded-xl px-3 py-2 text-white text-sm focus:outline-none focus:border-amber-500">
                    </div>
                    <div>
                        <label class="block text-xs font-semibold text-slate-400 uppercase tracking-wider mb-1">Coût Logistique</label>
                        <input type="number" id="o-cost" required class="w-full bg-slate-900 border border-slate-800 rounded-xl px-3 py-2 text-white text-sm focus:outline-none focus:border-amber-500">
                    </div>
                </div>
                <div class="pt-4 border-t border-slate-800 flex justify-end gap-2">
                    <button type="button" onclick="window.closeModal('order-modal')" class="px-4 py-2 bg-slate-800 text-slate-300 rounded-xl text-xs font-medium">Annuler</button>
                    <button type="submit" class="px-4 py-2 bg-blue-600 text-white rounded-xl text-xs font-bold hover:bg-blue-700">Lancer Mission</button>
                </div>
            </form>
        </div>
    </div>

    <!-- ================= SCRIPT & LOGIQUE MODULAIRE ================= -->
    <script type="module">
        // Import the functions you need from the SDKs you need (Version Modulaire v10)
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.8.0/firebase-app.js";
        import { getAnalytics } from "https://www.gstatic.com/firebasejs/10.8.0/firebase-analytics.js";
        import { getFirestore, collection, addDoc, onSnapshot, deleteDoc, doc, updateDoc } from "https://www.gstatic.com/firebasejs/10.8.0/firebase-firestore.js";

        // Configuration Firebase fournie
        const firebaseConfig = {
            apiKey: "AIzaSyBnFK2ATbYsRZ3RX8bsOYkNEMtk6tsCGsc",
            authDomain: "portail-de-commandes.firebaseapp.com",
            databaseURL: "https://portail-de-commandes-default-rtdb.firebaseio.com",
            projectId: "portail-de-commandes",
            storageBucket: "portail-de-commandes.firebasestorage.app",
            messagingSenderId: "961030938629",
            appId: "1:961030938629:web:0f3d43445b8b358014bc6c",
            measurementId: "G-7ZKSRQS4T3"
        };

        let db = null;
        let localProducts = [];
        let localOrders = [];
        let finChart = null;

        // --- GESTIONNAIRE DE TOASTS & MODALS ---
        window.showToast = (message, type = 'info') => {
            const container = document.getElementById('toast-container');
            const toast = document.createElement('div');
            const bg = type === 'success' ? 'bg-emerald-500' : (type === 'error' ? 'bg-rose-500' : 'bg-blue-500');
            toast.className = `${bg} text-white px-4 py-2 rounded-xl shadow-lg text-sm font-medium transform transition-all duration-300 translate-y-4 opacity-0 flex items-center gap-2`;
            toast.innerHTML = type === 'success' ? `✓ ${message}` : (type === 'error' ? `⚠ ${message}` : `ℹ ${message}`);
            container.appendChild(toast);
            
            // Animation d'entrée
            setTimeout(() => { toast.classList.remove('translate-y-4', 'opacity-0'); }, 10);
            // Auto destruction
            setTimeout(() => {
                toast.classList.add('translate-y-4', 'opacity-0');
                setTimeout(() => toast.remove(), 300);
            }, 3000);
        };

        window.openConfirmModal = (title, message, callback) => {
            document.getElementById('confirm-title').innerText = title;
            document.getElementById('confirm-msg').innerText = message;
            document.getElementById('confirm-modal').classList.remove('hidden');
            const btn = document.getElementById('confirm-action-btn');
            btn.onclick = () => {
                callback();
                window.closeConfirmModal();
            };
        };
        window.closeConfirmModal = () => document.getElementById('confirm-modal').classList.add('hidden');

        // Initialisation de Firebase
        try {
            const app = initializeApp(firebaseConfig);
            const analytics = getAnalytics(app);
            db = getFirestore(app);
            
            document.getElementById('cloud-indicator').className = "w-2.5 h-2.5 bg-emerald-500 rounded-full animate-pulse shadow-[0_0_8px_rgba(16,185,129,0.8)]";
            document.getElementById('cloud-text').innerText = "Firebase Cloud (Modulaire) Connecté";
            document.getElementById('cloud-text').className = "text-xs text-emerald-400 font-medium";
            syncFirebaseData();
        } catch (e) {
            console.error("Erreur Firebase:", e);
            document.getElementById('cloud-indicator').className = "w-2.5 h-2.5 bg-amber-500 rounded-full";
            document.getElementById('cloud-text').innerText = "Mode Hors-Ligne (Données Locales)";
            document.getElementById('cloud-text').className = "text-xs text-amber-500 font-medium";
            loadMockData();
        }

        function loadMockData() {
            localProducts = [
                { id: "P-001", name: "Huile Moteur Synthétique", price: 15000, stock: 45, moq: 5, image: "data:image/svg+xml;utf8,<svg xmlns='http://www.w3.org/2000/svg' width='200' height='200'><rect width='200' height='200' fill='%231e293b'/><text x='100' y='100' font-family='sans-serif' font-size='14' fill='%2394a3b8' text-anchor='middle' alignment-baseline='middle'>Image Produit</text></svg>" }
            ];
            localOrders = [
                { id: "CG-8821", customer: "Garage Central", zone: "Charbonnages", price: 2500, cost: 1000, secretCode: "2417", status: "En cours" },
                { id: "CG-4491", customer: "Client Particulier", zone: "Akanda", price: 5000, cost: 1500, secretCode: "9931", status: "Clôturé" }
            ];
            refreshUI();
        }

        function syncFirebaseData() {
            if (!db) return;
            onSnapshot(collection(db, "produits"), (snapshot) => {
                localProducts = [];
                snapshot.forEach(docSnap => { let d = docSnap.data(); d.fbId = docSnap.id; localProducts.push(d); });
                refreshUI();
            }, (error) => { console.error("Erreur lecture produits", error); loadMockData(); });

            onSnapshot(collection(db, "missions"), (snapshot) => {
                localOrders = [];
                snapshot.forEach(docSnap => { let d = docSnap.data(); d.fbId = docSnap.id; localOrders.push(d); });
                refreshUI();
            }, (error) => console.error("Erreur lecture missions", error));
        }

        // --- EXPORTATION (Attaché à Window pour l'HTML) ---
        window.exportDataBackup = () => {
            const backupData = {
                date_export: new Date().toISOString(),
                nombre_produits: localProducts.length,
                nombre_missions: localOrders.length,
                catalogue_produits: localProducts,
                historique_missions: localOrders
            };
            const dataStr = "data:text/json;charset=utf-8," + encodeURIComponent(JSON.stringify(backupData, null, 2));
            const downloadAnchorNode = document.createElement('a');
            downloadAnchorNode.setAttribute("href", dataStr);
            downloadAnchorNode.setAttribute("download", "cg241_sauvegarde_" + Date.now() + ".json");
            document.body.appendChild(downloadAnchorNode); 
            downloadAnchorNode.click();
            downloadAnchorNode.remove();
            
            window.showToast("Sauvegarde JSON téléchargée avec succès !", "success");
        };

        // --- GESTION PRODUITS ---
        window.saveProduct = (e) => {
            e.preventDefault();
            const file = document.getElementById('p-image').files[0];
            if (!file) return;

            const reader = new FileReader();
            reader.onload = async function(event) {
                const base64Image = event.target.result;
                const newProd = {
                    id: "P-" + Date.now().toString().slice(-4),
                    name: document.getElementById('p-name').value,
                    price: parseFloat(document.getElementById('p-price').value) || 0,
                    stock: parseInt(document.getElementById('p-stock').value) || 0,
                    moq: parseInt(document.getElementById('p-moq').value) || 1,
                    image: base64Image
                };

                try {
                    if (db) {
                        await addDoc(collection(db, "produits"), newProd);
                    } else {
                        localProducts.push(newProd);
                        refreshUI();
                    }
                    window.showToast("Article ajouté au catalogue !", "success");
                    window.closeModal('product-modal'); 
                    document.getElementById('product-form').reset();
                } catch(err) {
                    window.showToast("Erreur lors de l'ajout", "error");
                    console.error(err);
                }
            };
            reader.readAsDataURL(file);
        };

        window.deleteProduct = (prodId, fbId) => {
            window.openConfirmModal("Suppression d'Article", "Voulez-vous vraiment supprimer définitivement cet article du catalogue ?", async () => {
                if (db && fbId) {
                    await deleteDoc(doc(db, "produits", fbId));
                } else {
                    localProducts = localProducts.filter(p => p.id !== prodId);
                    refreshUI();
                }
                window.showToast("Article supprimé.", "info");
            });
        };

        // --- GESTION MISSIONS ---
        window.saveOrder = async (e) => {
            e.preventDefault();
            const randomCode = Math.floor(1000 + Math.random() * 9000).toString();
            const newOrder = {
                id: "CG-" + Math.floor(1000 + Math.random() * 9000),
                customer: document.getElementById('o-customer').value,
                zone: document.getElementById('o-zone').value,
                price: parseFloat(document.getElementById('o-price').value) || 0,
                cost: parseFloat(document.getElementById('o-cost').value) || 0,
                secretCode: randomCode,
                status: "En cours"
            };

            try {
                if (db) {
                    await addDoc(collection(db, "missions"), newOrder);
                } else {
                    localOrders.push(newOrder);
                    refreshUI();
                }
                window.showToast("Nouvelle mission logistique créée !", "success");
                window.closeModal('order-modal'); 
                document.getElementById('order-form').reset();
            } catch(err) {
                window.showToast("Erreur création mission", "error");
            }
        };

        window.verifyAndCloseMission = async (orderId, fbId, inputElementId) => {
            const typedCode = document.getElementById(inputElementId).value;
            const targetOrder = localOrders.find(o => o.id === orderId);

            if (targetOrder && targetOrder.secretCode === typedCode.trim()) {
                if (db && fbId) {
                    await updateDoc(doc(db, "missions", fbId), { status: "Clôturé" });
                } else {
                    targetOrder.status = "Clôturé";
                    refreshUI();
                }
                window.showToast("Code valide ! Mission archivée.", "success");
            } else {
                window.showToast("Code de validation incorrect.", "error");
            }
        };

        // --- RENDU UI ---
        function refreshUI() {
            const grid = document.getElementById('products-grid');
            grid.innerHTML = "";
            localProducts.forEach(p => {
                grid.innerHTML += `
                    <div class="bg-[#0f172a] border border-slate-800 rounded-2xl overflow-hidden shadow-lg flex flex-col hover:border-slate-600 transition-colors">
                        <div class="h-48 overflow-hidden bg-slate-900 border-b border-slate-800 relative">
                            <img src="${p.image}" class="w-full h-full object-cover" alt="Produit" onerror="this.src='data:image/svg+xml;utf8,<svg xmlns=\\\'http://www.w3.org/2000/svg\\\' width=\\\'200\\\' height=\\\'200\\\'><rect width=\\\'200\\\' height=\\\'200\\\' fill=\\\'%231e293b\\\'/><text x=\\\'100\\\' y=\\\'100\\\' font-family=\\\'sans-serif\\\' font-size=\\\'14\\\' fill=\\\'%2394a3b8\\\' text-anchor=\\\'middle\\\' alignment-baseline=\\\'middle\\\'>Erreur Image</text></svg>'">
                        </div>
                        <div class="p-5 flex-1 flex flex-col justify-between space-y-4">
                            <div>
                                <h4 class="font-bold text-base text-white leading-tight truncate">${p.name}</h4>
                                <div class="text-xs text-slate-400 mt-1">Ref: <span class="font-mono text-amber-500">${p.id}</span></div>
                            </div>
                            <div class="grid grid-cols-2 gap-2 bg-slate-900/60 p-3 rounded-xl border border-slate-800/80 text-xs">
                                <div><span class="text-slate-500 block">Stock:</span> <span class="text-white font-semibold">${p.stock}</span></div>
                                <div><span class="text-slate-500 block">MOQ:</span> <span class="text-amber-400 font-bold">${p.moq}</span></div>
                            </div>
                            <div class="flex items-center justify-between pt-1">
                                <span class="text-lg font-extrabold text-emerald-400">${p.price.toLocaleString('fr-FR')} F</span>
                                <button onclick="window.deleteProduct('${p.id}', '${p.fbId || ''}')" class="text-xs bg-slate-800 text-rose-400 hover:bg-rose-600 hover:text-white px-3 py-1.5 rounded-lg transition-all">
                                    Supprimer
                                </button>
                            </div>
                        </div>
                    </div>`;
            });

            let totalCA = 0; let totalCO = 0;
            let activeHtml = ""; let archHtml = ""; let livHtml = "";

            localOrders.forEach(o => {
                if (o.status === "Clôturé") {
                    totalCA += o.price; totalCO += o.cost;
                    archHtml += `
                        <tr class="hover:bg-slate-900/20 transition-colors">
                            <td class="py-3 px-6 font-mono font-bold text-amber-400">${o.id}</td>
                            <td class="py-3 px-6 text-white text-sm">${o.customer} <br><span class="text-xs text-slate-500">${o.zone}</span></td>
                            <td class="py-3 px-6 text-emerald-400 font-medium">${o.price.toLocaleString('fr-FR')} F</td>
                            <td class="py-3 px-6 text-slate-400">${o.cost.toLocaleString('fr-FR')} F</td>
                            <td class="py-3 px-6"><span class="text-[10px] bg-emerald-500/10 text-emerald-400 px-2 py-1 rounded border border-emerald-500/20">Archivée</span></td>
                        </tr>`;
                } else {
                    activeHtml += `
                        <tr class="hover:bg-slate-900/20 transition-colors">
                            <td class="py-3 px-6 font-mono font-bold text-blue-400">${o.id}</td>
                            <td class="py-3 px-6 text-white text-sm">${o.customer} <br><span class="text-xs text-slate-500">${o.zone}</span></td>
                            <td class="py-3 px-6 text-white">${o.price.toLocaleString('fr-FR')} F</td>
                            <td class="py-3 px-6 text-slate-400">${o.cost.toLocaleString('fr-FR')} F</td>
                            <td class="py-3 px-6 font-mono text-amber-400 font-bold tracking-widest">${o.secretCode}</td>
                            <td class="py-3 px-6"><span class="text-[10px] bg-blue-500/10 text-blue-400 px-2 py-1 rounded border border-blue-500/20">En Cours</span></td>
                        </tr>`;

                    livHtml += `
                        <div class="bg-[#0f172a] border border-slate-800 rounded-2xl p-5 shadow-md flex flex-col justify-between hover:border-slate-600 transition-colors">
                            <div>
                                <div class="flex justify-between items-start mb-2">
                                    <span class="text-xs font-mono font-bold text-blue-400 bg-blue-900/20 px-2 py-1 rounded border border-blue-800/30">${o.id}</span>
                                </div>
                                <h4 class="font-bold text-lg text-white">${o.customer}</h4>
                                <p class="text-sm text-amber-400 font-medium mt-1">📍 ${o.zone}</p>
                            </div>
                            <div class="pt-4 mt-4 border-t border-slate-800">
                                <label class="text-[10px] font-bold text-slate-500 uppercase tracking-wider block mb-2">Validation Admin</label>
                                <div class="flex gap-2">
                                    <input type="text" id="verify-${o.id}" placeholder="PIN 4 ch." class="w-full bg-slate-900 border border-slate-700 text-sm rounded-xl px-3 py-2 text-center text-white font-mono focus:border-amber-500 outline-none">
                                    <button onclick="window.verifyAndCloseMission('${o.id}', '${o.fbId || ''}', 'verify-${o.id}')" class="bg-amber-500 hover:bg-amber-600 text-blue-950 font-bold text-xs px-4 py-2 rounded-xl whitespace-nowrap transition-all shadow-md">
                                        Valider
                                    </button>
                                </div>
                            </div>
                        </div>`;
                }
            });

            document.getElementById('admin-ca').innerText = totalCA.toLocaleString('fr-FR') + " F";
            document.getElementById('admin-co').innerText = totalCO.toLocaleString('fr-FR') + " F";
            document.getElementById('admin-marge').innerText = (totalCA - totalCO).toLocaleString('fr-FR') + " F";

            document.getElementById('admin-orders-tbody').innerHTML = activeHtml || `<tr><td colspan="6" class="py-6 text-center text-slate-500 italic">Aucune mission en cours.</td></tr>`;
            document.getElementById('archives-tbody').innerHTML = archHtml || `<tr><td colspan="5" class="py-6 text-center text-slate-500 italic">Aucune archive disponible.</td></tr>`;
            document.getElementById('livreur-missions-container').innerHTML = livHtml || `<div class="col-span-full py-8 text-center text-slate-500 bg-slate-900/20 rounded-xl border border-slate-800 border-dashed">Aucune mission sur le terrain pour le moment.</div>`;

            renderChart(totalCA, totalCO, totalCA - totalCO);
        }

        function renderChart(ca, co, marge) {
            if (finChart) finChart.destroy();
            const ctx = document.getElementById('financialChart').getContext('2d');
            finChart = new Chart(ctx, {
                type: 'bar',
                data: {
                    labels: ['Chiffre d\'Affaires', 'Coûts Logistiques', 'Marge Nette'],
                    datasets: [{
                        data: [ca, co, Math.max(0, marge)], // Ne pas afficher une barre négative visuellement dérangeante sans config spécifique
                        backgroundColor: ['#10b981', '#f43f5e', '#f59e0b'],
                        borderRadius: 6,
                        barThickness: 45
                    }]
                },
                options: {
                    responsive: true, maintainAspectRatio: false,
                    plugins: { legend: { display: false }, tooltip: { backgroundColor: '#1e293b', titleColor: '#fff', bodyColor: '#fff' } },
                    scales: {
                        y: { grid: { color: 'rgba(30, 41, 59, 0.5)' }, ticks: { color: '#94a3b8' } },
                        x: { grid: { display: false }, ticks: { color: '#94a3b8' } }
                    }
                }
            });
        }

        // --- NAVIGATION & DOM HELPER ---
        window.switchTab = (tabId) => {
            document.querySelectorAll('.tab-content').forEach(el => el.classList.remove('active'));
            document.querySelectorAll('aside nav button').forEach(btn => btn.className = "w-full flex items-center gap-3 px-4 py-3 text-sm font-medium rounded-xl text-slate-300 hover:bg-slate-800 hover:text-white transition-all");
            document.getElementById('tab-' + tabId).classList.add('active');
            document.getElementById('btn-' + tabId).className = "w-full flex items-center gap-3 px-4 py-3 text-sm font-bold rounded-xl bg-amber-500 text-blue-950 shadow-md shadow-amber-500/10 transition-all";
        };

        window.openModal = (id) => document.getElementById(id).classList.remove('hidden');
        window.closeModal = (id) => document.getElementById(id).classList.add('hidden');
        
        // Init par défaut
        window.switchTab('vitrine');

    </script>
</body>
</html>
