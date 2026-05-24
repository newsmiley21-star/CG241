pwa_code = """<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CG241 - Portail Logistique Global</title>
    <script src="https://cdn.jsdelivr.net/npm/@tailwindcss/browser@4"></script>
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" integrity="sha256-p4NxAoJBhIIN+hmNHrzRCf9tD/miZyoHS5obTRR9BMY=" crossorigin=""/>
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js" integrity="sha256-20nQCchB9co0qIjJZRGuk2/Z9VM+kNiyxNV1lvTlZBo=" crossorigin=""></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap');
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f1f5f9;
        }
        .custom-scrollbar::-webkit-scrollbar {
            width: 6px;
            height: 6px;
        }
        .custom-scrollbar::-webkit-scrollbar-track {
            background: #f1f5f9;
        }
        .custom-scrollbar::-webkit-scrollbar-thumb {
            background: #cbd5e1;
            border-radius: 4px;
        }
        #map-desktop, #map-mobile {
            z-index: 1;
        }
    </style>
</head>
<body class="h-screen overflow-hidden flex flex-col">

    <header class="bg-white border-b border-slate-200 px-6 py-3 flex items-center justify-between shrink-0 shadow-sm">
        <div class="flex items-center gap-8">
            <div class="flex items-center gap-2">
                <span class="text-2xl font-black tracking-wider text-[#0284c7]">CG<span class="text-[#eab308]">241</span></span>
                <span class="text-xs font-semibold bg-slate-100 text-slate-600 px-2 py-1 rounded border border-slate-200">Global Logistics Portal</span>
            </div>
            <div class="hidden md:flex bg-slate-100 p-1 rounded-lg border border-slate-200">
                <button onclick="switchView('desktop')" id="btn-view-desktop" class="px-4 py-1.5 rounded-md text-xs font-medium bg-white text-slate-800 shadow-sm transition-all flex items-center gap-2">
                    <i class="fa-solid var(--fa-computer) fa-desktop"></i> Administration
                </button>
                <button onclick="switchView('mobile')" id="btn-view-mobile" class="px-4 py-1.5 rounded-md text-xs font-medium text-slate-600 hover:text-slate-900 transition-all flex items-center gap-2">
                    <i class="fa-solid fa-mobile-screen-button"></i> Application Client
                </button>
            </div>
        </div>

        <div class="flex items-center gap-4">
            <button class="p-2 text-slate-500 hover:text-slate-800 hover:bg-slate-100 rounded-full transition-colors relative">
                <i class="fa-regular fa-bell text-lg"></i>
                <span class="absolute top-1 right-1 w-4 h-4 bg-red-500 text-[10px] text-white font-bold rounded-full flex items-center justify-center">1</span>
            </button>
            <button class="p-2 text-slate-500 hover:text-slate-800 hover:bg-slate-100 rounded-full transition-colors">
                <i class="fa-solid fa-gear text-lg"></i>
            </button>
            <div class="h-8 w-px bg-slate-200 mx-1"></div>
            <div class="flex items-center gap-3 cursor-pointer p-1.5 hover:bg-slate-50 rounded-lg transition-colors">
                <div class="w-9 h-9 rounded-full bg-[#0284c7] text-white font-bold flex items-center justify-center shadow-inner">
                    CG
                </div>
                <div class="hidden sm:block text-left">
                    <div class="text-xs font-semibold text-slate-800">CG241 Admin</div>
                    <div class="text-[10px] text-slate-500">Libreville, Gabon</div>
                </div>
                <i class="fa-solid fa-chevron-down text-xs text-slate-400"></i>
            </div>
        </div>
    </header>

    <div class="flex flex-1 overflow-hidden relative">

        <div id="view-desktop" class="flex flex-1 overflow-hidden w-full transition-all duration-300">
            <aside class="w-16 md:w-20 bg-white border-r border-slate-200 flex flex-col items-center py-6 justify-between shrink-0">
                <div class="flex flex-col gap-5 w-full px-2">
                    <button class="w-full aspect-square rounded-xl flex flex-col items-center justify-center gap-1 text-slate-400 hover:text-[#0284c7] hover:bg-slate-50 transition-all">
                        <i class="fa-solid fa-house text-lg"></i>
                        <span class="text-[9px] font-medium hidden md:block">Accueil</span>
                    </button>
                    <button class="w-full aspect-square rounded-xl flex flex-col items-center justify-center gap-1 text-[#0284c7] bg-sky-50 border border-sky-100 font-semibold transition-all">
                        <i class="fa-solid fa-box text-lg"></i>
                        <span class="text-[9px] font-medium hidden md:block">Commandes</span>
                    </button>
                    <button class="w-full aspect-square rounded-xl flex flex-col items-center justify-center gap-1 text-slate-400 hover:text-[#0284c7] hover:bg-slate-50 transition-all">
                        <i class="fa-solid fa-calendar-days text-lg"></i>
                        <span class="text-[9px] font-medium hidden md:block">Planning</span>
                    </button>
                    <button class="w-full aspect-square rounded-xl flex flex-col items-center justify-center gap-1 text-slate-400 hover:text-[#0284c7] hover:bg-slate-50 transition-all">
                        <i class="fa-solid fa-truck text-lg"></i>
                        <span class="text-[9px] font-medium hidden md:block">Flotte</span>
                    </button>
                    <button class="w-full aspect-square rounded-xl flex flex-col items-center justify-center gap-1 text-slate-400 hover:text-[#0284c7] hover:bg-slate-50 transition-all">
                        <i class="fa-solid fa-chart-line text-lg"></i>
                        <span class="text-[9px] font-medium hidden md:block">Rapports</span>
                    </button>
                </div>
                
                <button class="w-12 h-12 rounded-xl flex items-center justify-center text-slate-400 hover:text-red-500 hover:bg-red-50 transition-all">
                    <i class="fa-solid fa-right-from-bracket text-lg"></i>
                </button>
            </aside>

            <main class="flex-1 p-6 overflow-y-auto custom-scrollbar space-y-6">
                <div class="flex items-center justify-between">
                    <div>
                        <h1 class="text-xl font-bold text-slate-800">Tableau de Bord de Commandes</h1>
                        <p class="text-xs text-slate-500">Gestion opérationnelle en temps réel - Région Estuaire, Libreville</p>
                    </div>
                    <div id="connection-status" class="flex items-center gap-2 text-xs font-medium bg-emerald-50 text-emerald-700 px-3 py-1.5 rounded-full border border-emerald-200">
                        <span class="w-2 h-2 rounded-full bg-emerald-500 animate-pulse"></span> Mode Connecté
                    </div>
                </div>

                <div class="grid grid-cols-1 xl:grid-cols-3 gap-6">
                    
                    <div class="xl:col-span-1 space-y-6 flex flex-col">
                        <div class="bg-white rounded-xl border border-slate-200 shadow-sm overflow-hidden">
                            <div class="bg-[#eab308] text-white px-4 py-3 font-semibold text-sm flex items-center gap-2">
                                <i class="fa-solid fa-plus-circle"></i> Commander une Livraison
                            </div>
                            <form id="orderForm" onsubmit="handleNewOrder(event)" class="p-4 space-y-3.5">
                                <div>
                                    <label class="block text-[11px] font-semibold text-slate-500 uppercase tracking-wider mb-1">Origine</label>
                                    <div class="relative">
                                        <i class="fa-solid fa-location-dot absolute left-3 top-3 text-slate-400 text-xs"></i>
                                        <input type="text" id="origin" required value="Libreville, Gabon (Charbonnages)" class="w-full bg-slate-50 border border-slate-200 rounded-lg pl-9 pr-3 py-2 text-xs focus:bg-white focus:border-[#0284c7] outline-none transition-all">
                                    </div>
                                </div>
                                <div>
                                    <label class="block text-[11px] font-semibold text-slate-500 uppercase tracking-wider mb-1">Destination</label>
                                    <div class="relative">
                                        <i class="fa-solid fa-location-crosshairs absolute left-3 top-3 text-slate-400 text-xs"></i>
                                        <select id="destination" class="w-full bg-slate-50 border border-slate-200 rounded-lg pl-9 pr-3 py-2 text-xs focus:bg-white focus:border-[#0284c7] outline-none transition-all appearance-none cursor-pointer">
                                            <option value="Owendo, Gabon">Owendo, Gabon</option>
                                            <option value="Akanda, Gabon">Akanda, Gabon</option>
                                            <option value="Libreville, Gabon (PK9)">Libreville, Gabon (PK9)</option>
                                            <option value="Libreville, Gabon (Nombakélé)">Libreville, Gabon (Nombakélé)</option>
                                        </select>
                                        <i class="fa-solid fa-chevron-down absolute right-3 top-3.5 text-slate-400 text-[10px] pointer-events-none"></i>
                                    </div>
                                </div>
                                <div class="grid grid-cols-2 gap-3">
                                    <div>
                                        <label class="block text-[11px] font-semibold text-slate-500 uppercase tracking-wider mb-1">Poids (kg)</label>
                                        <input type="text" id="weight" required value="200 kg" class="w-full bg-slate-50 border border-slate-200 rounded-lg px-3 py-2 text-xs focus:bg-white focus:border-[#0284c7] outline-none transition-all">
                                    </div>
                                    <div>
                                        <label class="block text-[11px] font-semibold text-slate-500 uppercase tracking-wider mb-1">Priorité</label>
                                        <div class="relative">
                                            <select id="priority" class="w-full bg-slate-50 border border-slate-200 rounded-lg px-3 py-2 text-xs focus:bg-white focus:border-[#0284c7] outline-none transition-all appearance-none cursor-pointer">
                                                <option value="Immédiat">Immédiat</option>
                                                <option value="Standard">Standard</option>
                                            </select>
                                            <i class="fa-solid fa-chevron-down absolute right-3 top-3.5 text-slate-400 text-[10px] pointer-events-none"></i>
                                        </div>
                                    </div>
                                </div>
                                <div>
                                    <label class="block text-[11px] font-semibold text-slate-500 uppercase tracking-wider mb-1">Type de Colis</label>
                                    <div class="relative">
                                        <i class="fa-solid fa-box-open absolute left-3 top-3 text-slate-400 text-xs"></i>
                                        <select id="packageType" class="w-full bg-slate-50 border border-slate-200 rounded-lg pl-9 pr-3 py-2 text-xs focus:bg-white focus:border-[#0284c7] outline-none transition-all appearance-none cursor-pointer">
                                            <option value="Pièces Détachées / Moto">Pièces Détachées / Moto</option>
                                            <option value="Marchandises Générales">Marchandises Générales</option>
                                            <option value="Électronique / High-Tech">Électronique / High-Tech</option>
                                        </select>
                                        <i class="fa-solid fa-chevron-down absolute right-3 top-3.5 text-slate-400 text-[10px] pointer-events-none"></i>
                                    </div>
                                </div>
                                <button type="submit" class="w-full bg-[#0284c7] hover:bg-sky-700 text-white font-semibold py-2.5 rounded-lg text-xs transition-colors shadow-md flex items-center justify-center gap-2 mt-2">
                                    <i class="fa-solid fa-paper-plane"></i> Commander
                                </button>
                            </form>
                        </div>

                        <div class="bg-white rounded-xl border border-slate-200 shadow-sm p-4 flex items-center justify-between">
                            <div class="flex items-center gap-3">
                                <div class="w-12 h-12 rounded-xl bg-slate-100 flex items-center justify-center text-slate-700 text-xl border border-slate-200">
                                    <i class="fa-solid fa-barcode"></i>
                                </div>
                                <div>
                                    <h4 class="text-xs font-bold text-slate-800">Scanner Analytique</h4>
                                    <p class="text-[11px] text-slate-500">Génération automatique de labels QR</p>
                                </div>
                            </div>
                            <button onclick="triggerLabelGeneration()" class="bg-slate-100 hover:bg-slate-200 text-slate-700 border border-slate-200 px-3 py-1.5 rounded-lg text-xs font-medium transition-colors">
                                Générer
                            </button>
                        </div>
                    </div>

                    <div class="xl:col-span-2 space-y-6 flex flex-col">
                        <div class="bg-white rounded-xl border border-slate-200 shadow-sm overflow-hidden flex flex-col flex-1 min-h-[350px]">
                            <div class="bg-slate-50 border-b border-slate-200 px-4 py-3 flex items-center justify-between">
                                <span class="text-xs font-bold text-slate-700 flex items-center gap-2">
                                    <i class="fa-solid fa-earth-africa text-[#0284c7]"></i> Suivi en Temps Réel (Libreville Map)
                                </span>
                                <span class="text-[11px] text-slate-500 flex items-center gap-1.5">
                                    <span class="w-2 h-2 rounded-full bg-[#0284c7] animate-ping"></span> 3 Véhicules actifs
                                </span>
                            </div>
                            <div id="map-desktop" class="w-full flex-1 bg-slate-100"></div>
                        </div>
                    </div>

                </div>

                <div class="grid grid-cols-1 xl:grid-cols-3 gap-6">
                    <div class="xl:col-span-2 bg-white rounded-xl border border-slate-200 shadow-sm overflow-hidden">
                        <div class="px-4 py-3 border-b border-slate-200 bg-slate-50 flex items-center justify-between">
                            <h3 class="text-xs font-bold text-slate-700">Dernières Commandes Récentes</h3>
                            <span class="text-[11px] bg-sky-50 text-[#0284c7] border border-sky-100 px-2 py-0.5 rounded font-medium">Historique</span>
                        </div>
                        <div class="overflow-x-auto custom-scrollbar">
                            <table class="w-full text-left text-xs">
                                <thead class="bg-slate-50 text-slate-500 font-semibold border-b border-slate-200">
                                    <tr>
                                        <th class="p-3">ID Commande</th>
                                        <th class="p-3">Origine</th>
                                        <th class="p-3">Destination</th>
                                        <th class="p-3">Type Colis</th>
                                        <th class="p-3">Statut</th>
                                    </tr>
                                </thead>
                                <tbody id="ordersTableBody" class="divide-y divide-slate-100 text-slate-700">
                                    <tr class="hover:bg-slate-50 transition-colors">
                                        <td class="p-3 font-semibold text-[#0284c7]">CG436618</td>
                                        <td class="p-3">Libreville (Charbonnages)</td>
                                        <td class="p-3">Owendo Port</td>
                                        <td class="p-3">Pièces Détachées</td>
                                        <td class="p-3"><span class="bg-amber-50 text-amber-700 border border-amber-200 px-2 py-0.5 rounded-full text-[10px] font-medium">En cours</span></td>
                                    </tr>
                                    <tr class="hover:bg-slate-50 transition-colors">
                                        <td class="p-3 font-semibold text-[#0284c7]">CG436733</td>
                                        <td class="p-3">Libreville (Centre Ville)</td>
                                        <td class="p-3">Akanda (Avorbam)</td>
                                        <td class="p-3">Électronique</td>
                                        <td class="p-3"><span class="bg-emerald-50 text-emerald-700 border border-emerald-200 px-2 py-0.5 rounded-full text-[10px] font-medium">Livré</span></td>
                                    </tr>
                                    <tr class="hover:bg-slate-50 transition-colors">
                                        <td class="p-3 font-semibold text-[#0284c7]">CG496738</td>
                                        <td class="p-3">Libreville (Charbonnages)</td>
                                        <td class="p-3">PK9 Entrepôt</td>
                                        <td class="p-3">Marchandises</td>
                                        <td class="p-3"><span class="bg-sky-50 text-sky-700 border border-sky-200 px-2 py-0.5 rounded-full text-[10px] font-medium">Planifié</span></td>
                                    </tr>
                                </tbody>
                            </table>
                        </div>
                    </div>

                    <div class="xl:col-span-1 bg-white rounded-xl border border-slate-200 shadow-sm p-4 flex flex-col justify-between space-y-4">
                        <div>
                            <h3 class="text-xs font-bold text-slate-700 mb-2 flex items-center gap-2">
                                <i class="fa-solid fa-chart-pie text-amber-500"></i> Comptabilité Analytique
                            </h3>
                            <p class="text-[11px] text-slate-500 mb-3">Analyse financière mensuelle des livraisons</p>
                            <div class="w-full aspect-[4/3] flex items-center justify-center bg-slate-50 rounded-lg p-2 border border-slate-100">
                                <canvas id="analyticsChart"></canvas>
                            </div>
                        </div>
                        <div class="pt-2 border-t border-slate-100 grid grid-cols-2 gap-2 text-center">
                            <div class="bg-slate-50 p-2 rounded border border-slate-100">
                                <div class="text-[10px] text-slate-500 uppercase tracking-wider font-semibold">Marge Nette</div>
                                <div class="text-xs font-bold text-slate-800">74.2%</div>
                            </div>
                            <div class="bg-slate-50 p-2 rounded border border-slate-100">
                                <div class="text-[10px] text-slate-500 uppercase tracking-wider font-semibold">Chiffre d'Aff.</div>
                                <div class="text-xs font-bold text-slate-800">4.8M FCFA</div>
                            </div>
                        </div>
                    </div>
                </div>
            </main>
        </div>

        <div id="view-mobile" class="hidden flex-1 overflow-hidden w-full items-center justify-center p-4 md:p-8 bg-slate-800 transition-all duration-300 overflow-y-auto">
            <div class="w-full max-w-[380px] aspect-[9/19] bg-slate-900 rounded-[40px] shadow-2xl border-[10px] border-slate-950 overflow-hidden flex flex-col relative">
                <div class="absolute top-0 left-1/2 -translate-x-1/2 w-32 h-6 bg-slate-950 rounded-b-2xl z-50 flex items-center justify-center">
                    <div class="w-12 h-1 bg-slate-800 rounded-full mb-1"></div>
                </div>

                <div class="bg-white border-b border-slate-100 pt-8 pb-3 px-4 flex items-center justify-between shrink-0">
                    <div>
                        <span class="text-lg font-black tracking-wider text-[#0284c7]">CG<span class="text-[#eab308]">241</span></span>
                        <span class="text-[10px] font-bold text-slate-400 block -mt-1">Client App</span>
                    </div>
                    <div class="flex items-center gap-2">
                        <span class="w-2 h-2 rounded-full bg-emerald-500"></span>
                        <i class="fa-regular fa-bell text-slate-600 text-sm"></i>
                    </div>
                </div>

                <div class="flex-1 overflow-y-auto custom-scrollbar bg-slate-50 p-4 space-y-4 pb-20">
                    <div class="grid grid-cols-2 gap-3">
                        <button onclick="alert('Formulaire de commande express client')" class="bg-white p-4 rounded-2xl border border-slate-100 shadow-sm flex flex-col items-center justify-center text-center gap-2 hover:bg-sky-50 group transition-all">
                            <div class="w-10 h-10 rounded-xl bg-sky-50 group-hover:bg-white text-[#0284c7] flex items-center justify-center shadow-inner text-lg">
                                <i class="fa-solid fa-box-archive"></i>
                            </div>
                            <span class="text-xs font-bold text-slate-800 leading-tight">Commander une Livraison</span>
                        </button>
                        <button onclick="alert('Suivi de colis par numéro')" class="bg-white p-4 rounded-2xl border border-slate-100 shadow-sm flex flex-col items-center justify-center text-center gap-2 hover:bg-sky-50 group transition-all">
                            <div class="w-10 h-10 rounded-xl bg-amber-50 group-hover:bg-white text-amber-500 flex items-center justify-center shadow-inner text-lg">
                                <i class="fa-solid fa-truck-ramp-box"></i>
                            </div>
                            <span class="text-xs font-bold text-slate-800 leading-tight">Suivre un Colis</span>
                        </button>
                    </div>

                    <div class="bg-white rounded-2xl border border-slate-100 shadow-sm overflow-hidden flex flex-col h-48">
                        <div class="px-3 py-2 bg-slate-50 border-b border-slate-100 flex items-center justify-between">
                            <span class="text-[11px] font-bold text-slate-700">Live tracker</span>
                            <span class="text-[9px] font-semibold bg-emerald-100 text-emerald-800 px-1.5 py-0.5 rounded">En route</span>
                        </div>
                        <div id="map-mobile" class="w-full flex-1 bg-slate-200"></div>
                    </div>

                    <div class="bg-white rounded-2xl border border-slate-100 shadow-sm p-4 flex items-center justify-between cursor-pointer hover:bg-slate-50 transition-colors">
                        <div class="flex items-center gap-3">
                            <div class="w-9 h-9 rounded-full bg-amber-100 text-amber-600 flex items-center justify-center text-sm shadow-inner">
                                <i class="fa-solid fa-star"></i>
                            </div>
                            <div>
                                <h4 class="text-xs font-bold text-slate-800">Mes Points Fidélité</h4>
                                <p class="text-[10px] text-slate-400">Gagnez des réductions sur vos livraisons</p>
                            </div>
                        </div>
                        <div class="flex items-center gap-1">
                            <span class="text-sm font-black text-slate-800">10</span>
                            <i class="fa-solid fa-chevron-right text-[10px] text-slate-400"></i>
                        </div>
                    </div>
                </div>

                <nav class="absolute bottom-0 inset-x-0 bg-white border-t border-slate-100 py-2 px-6 flex items-center justify-between z-40">
                    <button class="flex flex-col items-center text-[#0284c7]">
                        <i class="fa-solid fa-house text-sm"></i>
                        <span class="text-[9px] font-bold mt-0.5">Accueil</span>
                    </button>
                    <button class="flex flex-col items-center text-slate-400 hover:text-slate-700">
                        <i class="fa-solid fa-compass text-sm"></i>
                        <span class="text-[9px] font-medium mt-0.5">Explorer</span>
                    </button>
                    <button class="flex flex-col items-center text-slate-400 hover:text-slate-700 relative">
                        <i class="fa-solid fa-bell text-sm"></i>
                        <span class="absolute -top-1 -right-1 w-2 h-2 bg-red-500 rounded-full"></span>
                        <span class="text-[9px] font-medium mt-0.5">Alertes</span>
                    </button>
                    <button class="flex flex-col items-center text-slate-400 hover:text-slate-700">
                        <i class="fa-solid fa-user text-sm"></i>
                        <span class="text-[9px] font-medium mt-0.5">Profil</span>
                    </button>
                </nav>

            </div>
        </div>

    </div>

    <script>
        // Programmatic PWA Setup using dynamic blobs to enable self-contained offline capabilities
        window.addEventListener('load', () => {
            // Generate Manifest dynamically
            const manifestObj = {
                "name": "CG241 Global Logistics Portal",
                "short_name": "CG241",
                "start_url": window.location.href,
                "display": "standalone",
                "background_color": "#f1f5f9",
                "theme_color": "#0284c7",
                "description": "Système de suivi logistique complet et portail de livraison pour CG241 au Gabon.",
                "icons": [
                    {
                        "src": "data:image/svg+xml;utf8,<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 128 128' fill='%230284c7'><rect width='128' height='128' rx='28'/><text x='64' y='78' font-size='42' fill='white' font-weight='black' font-family='sans-serif' text-anchor='middle'>CG</text></svg>",
                        "sizes": "128x128",
                        "type": "image/svg+xml"
                    }
                ]
            };
            const manifestBlob = new Blob([JSON.stringify(manifestObj)], { type: 'application/json' });
            const manifestURL = URL.createObjectURL(manifestBlob);
            const linkTag = document.createElement('link');
            linkTag.rel = 'manifest';
            linkTag.href = manifestURL;
            document.head.appendChild(linkTag);

            // Register Service Worker from dynamic inline blob
            if ('serviceWorker' in navigator) {
                const swCode = `
                    const CACHE_NAME = 'cg241-v1';
                    self.addEventListener('install', (e) => {
                        self.skipWaiting();
                    });
                    self.addEventListener('activate', (e) => {
                        e.waitUntil(clients.claim());
                    });
                    self.addEventListener('fetch', (e) => {
                        // Simply proxy everything online, gracefully fallback when connection drops
                        e.respondWith(
                            fetch(e.request).catch(() => caches.match(e.request))
                        );
                    });
                `;
                const swBlob = new Blob([swCode], { type: 'application/javascript' });
                const swURL = URL.createObjectURL(swBlob);
                navigator.serviceWorker.register(swURL)
                    .then(() => console.log('CG241 Service Worker fonctionnel (PWA Blob Actif)'))
                    .catch(err => console.error('Erreur d\\'enregistrement PWA:', err));
            }

            // Sync connection status indicators
            window.addEventListener('online', updateOnlineStatus);
            window.addEventListener('offline', updateOnlineStatus);
            
            function updateOnlineStatus() {
                const statusEl = document.getElementById('connection-status');
                if(navigator.onLine) {
                    statusEl.className = "flex items-center gap-2 text-xs font-medium bg-emerald-50 text-emerald-700 px-3 py-1.5 rounded-full border border-emerald-200";
                    statusEl.innerHTML = '<span class="w-2 h-2 rounded-full bg-emerald-500 animate-pulse"></span> Mode Connecté';
                } else {
                    statusEl.className = "flex items-center gap-2 text-xs font-medium bg-rose-50 text-rose-700 px-3 py-1.5 rounded-full border border-rose-200";
                    statusEl.innerHTML = '<span class="w-2 h-2 rounded-full bg-rose-500"></span> Mode Hors-Ligne';
                }
            }
        });

        // VIEW NAVIGATION TOGGLE
        function switchView(mode) {
            const deskView = document.getElementById('view-desktop');
            const mobView = document.getElementById('view-mobile');
            const btnDesk = document.getElementById('btn-view-desktop');
            const btnMob = document.getElementById('btn-view-mobile');

            if(mode === 'desktop') {
                deskView.classList.remove('hidden');
                deskView.classList.add('flex');
                mobView.classList.add('hidden');
                mobView.classList.remove('flex');
                btnDesk.className = "px-4 py-1.5 rounded-md text-xs font-medium bg-white text-slate-800 shadow-sm transition-all flex items-center gap-2";
                btnMob.className = "px-4 py-1.5 rounded-md text-xs font-medium text-slate-600 hover:text-slate-900 transition-all flex items-center gap-2";
                setTimeout(() => { mapDesktop.invalidateSize() }, 100);
            } else {
                deskView.classList.add('hidden');
                deskView.classList.remove('flex');
                mobView.classList.remove('hidden');
                mobView.classList.add('flex');
                btnMob.className = "px-4 py-1.5 rounded-md text-xs font-medium bg-white text-slate-800 shadow-sm transition-all flex items-center gap-2";
                btnDesk.className = "px-4 py-1.5 rounded-md text-xs font-medium text-slate-600 hover:text-slate-900 transition-all flex items-center gap-2";
                setTimeout(() => { mapMobile.invalidateSize() }, 100);
            }
        }

        // MAPS INTIALIZATION (Libreville Focus)
        // Center coordinates for Libreville, Gabon
        const librevilleCoords = [0.4162, 9.4673];
        
        // Initialize Desktop Map
        const mapDesktop = L.map('map-desktop').setView(librevilleCoords, 13);
        L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
            attribution: '© OpenStreetMap contributors'
        }).addTo(mapDesktop);

        // Initialize Mobile Map
        const mapMobile = L.map('map-mobile').setView(librevilleCoords, 12);
        L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
            attribution: '© OpenStreetMap'
        }).addTo(mapMobile);

        // Custom delivery truck icon placeholder using FontAwesome styled HTML markers
        function createTruckMarker() {
            return L.divIcon({
                html: '<div class="w-8 h-8 rounded-full bg-[#0284c7] border-2 border-white flex items-center justify-center text-white shadow-md shadow-slate-400"><i class="fa-solid fa-truck text-[11px]"></i></div>',
                className: '',
                iconSize: [32, 32],
                iconAnchor: [16, 16]
            });
        }

        // Add dummy active trucks across Libreville (Charbonnages, Owendo, PK9)
        const truckLocations = [
            {coords: [0.4485, 9.4491], name: "Chauffeur Akanda - Colis CG436618"},
            {coords: [0.3120, 9.5011], name: "Livreur Owendo Port"},
            {coords: [0.3950, 9.4810], name: "Transit Charbonnages Hub"}
        ];

        truckLocations.forEach(loc => {
            L.marker(loc.coords, {icon: createTruckMarker()}).addTo(mapDesktop).bindPopup(`<b>${loc.name}</b><br>Statut: En mouvement`);
            L.marker(loc.coords, {icon: createTruckMarker()}).addTo(mapMobile).bindPopup(`<b>${loc.name}</b>`);
        });

        // FORM HANDLER & REAL-TIME INTERACTION
        function handleNewOrder(event) {
            event.preventDefault();
            const origin = document.getElementById('origin').value;
            const dest = document.getElementById('destination').value;
            const weight = document.getElementById('weight').value;
            const packageType = document.getElementById('packageType').value;

            // Generate random unique order code
            const randId = 'CG' + Math.floor(100000 + Math.random() * 900000);

            // Add new row to table
            const tbody = document.getElementById('ordersTableBody');
            const newRow = document.createElement('tr');
            newRow.className = "hover:bg-slate-50 transition-colors border-b border-slate-100";
            newRow.innerHTML = `
                <td class="p-3 font-semibold text-[#0284c7]">${randId}</td>
                <td class="p-3">${origin}</td>
                <td class="p-3">${dest}</td>
                <td class="p-3">${packageType} (${weight})</td>
                <td class="p-3"><span class="bg-sky-50 text-sky-700 border border-sky-200 px-2 py-0.5 rounded-full text-[10px] font-medium">Reçu (Firebase)</span></td>
            `;
            tbody.insertBefore(newRow, tbody.firstChild);

            alert(`Félicitations ! Commande enregistrée avec succès. ID: ${randId}`);
            document.getElementById('orderForm').reset();
        }

        function triggerLabelGeneration() {
            alert("Génération de l'étiquette code-barres en cours... Prêt pour impression thermique.");
        }

        // ANALYTICS CHARTS GENERATION
        const ctx = document.getElementById('analyticsChart').getContext('2d');
        new Chart(ctx, {
            type: 'bar',
            data: {
                labels: ['Zone Port', 'Zone Nord', 'Zone PK', 'Marseille'],
                datasets: [{
                    label: 'Coûts (FCFA)',
                    data: [150000, 220000, 180000, 240000],
                    backgroundColor: '#eab308',
                    borderRadius: 4
                }, {
                    label: 'Revenus (FCFA)',
                    data: [210000, 250000, 190000, 280000],
                    backgroundColor: '#0284c7',
                    borderRadius: 4
                }]
            },
            options: {
                responsive: true,
                maintainAspectRatio: false,
                plugins: {
                    legend: { labels: { font: { size: 10 } } }
                },
                scales: {
                    y: { ticks: { font: { size: 9 } } },
                    x: { ticks: { font: { size: 9 } } }
                }
            }
        });
    </script>
</body>
</html>
"""

with open("cg241_pwa_portal.html", "w", encoding="utf-8") as f:
    f.write(pwa_code)
print("PWA application HTML generated successfully.")
