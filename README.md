<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CT241 Dashboard</title>
    <!-- Utilisation de Tailwind CSS pour un design propre -->
    <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-gray-100 p-5">

    <div id="app" class="max-w-md mx-auto">
        <h1 class="text-2xl font-bold mb-6 text-center">Tableau de bord</h1>
        <div id="menu-container" class="space-y-4">
            <!-- Les boutons seront générés ici -->
        </div>
    </div>

    <script>
        const CODE_SECRET = "12901564";

        const menuItems = [
            { 
                title: "Comptabilité & Management", 
                url: "https://docs.google.com/spreadsheets/d/1CugnnRUwLlUzlPOUbYfjcaocHHDiyhYLWsy_Fv3vPgQ/edit?resourcekey=&gid=596661624#gid=596661624", 
                desc: "Analytiques et performances logistiques.",
                protected: true
            }
        ];

        function verifierAcces(url) {
            let saisie = prompt("Entrez le code de verrouillage pour accéder aux finances :");
            if (saisie === CODE_SECRET) {
                window.open(url, "_blank");
            } else if (saisie !== null) {
                alert("Code incorrect ! Accès refusé.");
            }
        }

        const container = document.getElementById('menu-container');

        menuItems.forEach(item => {
            const btn = document.createElement('div');
            btn.className = "bg-white p-4 rounded-xl shadow-md cursor-pointer hover:bg-amber-50 transition border-l-4 border-amber-400";
            btn.innerHTML = `<h2 class="font-bold">${item.title}</h2><p class="text-sm text-gray-600">${item.desc}</p>`;
            
            btn.onclick = () => {
                if (item.protected) {
                    verifierAcces(item.url);
                } else {
                    window.location.href = item.url;
                }
            };
            container.appendChild(btn);
        });
    </script>
</body>
</html>
