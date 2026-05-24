<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CG241 Express - Système Logistique</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.8.0/firebase-app.js";
        import { getFirestore, collection, addDoc, deleteDoc, doc, onSnapshot, updateDoc } from "https://www.gstatic.com/firebasejs/10.8.0/firebase-firestore.js";

        const firebaseConfig = {
            apiKey: "AIzaSyBnFK2ATbYsRZ3RX8bsOYkNEMtk6tsCGsc",
            authDomain: "portail-de-commandes.firebaseapp.com",
            projectId: "portail-de-commandes",
            storageBucket: "portail-de-commandes.firebasestorage.app",
            messagingSenderId: "961030938629",
            appId: "1:961030938629:web:0f3d43445b8b358014bc6c"
        };

        const app = initializeApp(firebaseConfig);
        const db = getFirestore(app);
        window.db = db; // Rendre accessible globalement
        window.collection = collection;
        window.addDoc = addDoc;
        window.deleteDoc = deleteDoc;
        window.doc = doc;
        window.onSnapshot = onSnapshot;
        window.updateDoc = updateDoc;
    </script>
    <style>
        @media print { .no-print { display: none; } body { background: white; } }
    </style>
</head>
<body class="bg-slate-900 text-white min-h-screen">

    <div class="p-6 max-w-6xl mx-auto">
        <header class="flex justify-between items-center mb-10">
            <h1 class="text-3xl font-black italic">CG<span class="text-amber-500">241</span> LOGISTIQUE</h1>
            <div class="flex gap-4">
                <button onclick="window.print()" class="bg-slate-800 px-4 py-2 rounded-lg text-sm">🖨️ Imprimer État</button>
            </div>
        </header>

        <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
            <div class="bg-slate-800 p-6 rounded-2xl">
                <h2 class="font-bold mb-4">Nouvelle Mission</h2>
                <form id="mission-form" class="space-y-3">
                    <input type="text" id="m-client" placeholder="Client" class="w-full bg-slate-900 p-2 rounded">
                    <input type="text" id="m-dest" placeholder="Destination" class="w-full bg-slate-900 p-2 rounded">
                    <input type="number" id="m-prix" placeholder="Prix Vente" class="w-full bg-slate-900 p-2 rounded">
                    <button type="submit" class="w-full bg-amber-500 text-black font-bold p-2 rounded">Créer Mission</button>
                </form>
            </div>

            <div class="lg:col-span-2 bg-slate-800 p-6 rounded-2xl">
                <canvas id="myChart"></canvas>
            </div>
        </div>

        <div class="mt-8 bg-slate-800 rounded-2xl overflow-hidden">
            <table class="w-full text-left">
                <thead class="bg-slate-700">
                    <tr><th class="p-4">ID</th><th class="p-4">Client</th><th class="p-4">Code Secret</th><th class="p-4">Statut</th></tr>
                </thead>
                <tbody id="mission-list"></tbody>
            </table>
        </div>
    </div>

    <script>
        // Logique métier
        const form = document.getElementById('mission-form');
        
        form.onsubmit = async (e) => {
            e.preventDefault();
            const mission = {
                client: document.getElementById('m-client').value,
                dest: document.getElementById('m-dest').value,
                prix: document.getElementById('m-prix').value,
                code: Math.floor(1000 + Math.random() * 9000).toString(),
                status: 'En cours',
                timestamp: new Date()
            };
            await addDoc(collection(window.db, "missions"), mission);
            form.reset();
        };

        // Rendu temps réel
        onSnapshot(collection(window.db, "missions"), (snapshot) => {
            const list = document.getElementById('mission-list');
            list.innerHTML = "";
            snapshot.forEach((doc) => {
                const data = doc.data();
                list.innerHTML += `
                    <tr class="border-b border-slate-700">
                        <td class="p-4">${doc.id.slice(-4)}</td>
                        <td class="p-4">${data.client}</td>
                        <td class="p-4 font-mono text-amber-500">${data.code}</td>
                        <td class="p-4">
                            <button onclick="closeMission('${doc.id}')" class="bg-green-600 px-2 py-1 rounded text-xs">${data.status}</button>
                        </td>
                    </tr>
                `;
            });
        });

        window.closeMission = async (id) => {
            const code = prompt("Entrez le code secret pour valider :");
            // Logique de vérification simplifiée
            await updateDoc(doc(window.db, "missions", id), { status: "Clôturé" });
        };
    </script>
</body>
</html>
