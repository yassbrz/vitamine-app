<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Expert Vitamine K</title>
    <style>
        body { font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif; background: #121212; color: white; text-align: center; padding: 20px; margin: 0; }
        .card { max-width: 450px; margin: 20px auto; background: #1e1e1e; padding: 30px; border-radius: 25px; box-shadow: 0 15px 35px rgba(0,0,0,0.6); border: 1px solid #333; }
        h1 { color: #2ecc71; font-size: 24px; margin-bottom: 10px; }
        p { color: #aaa; font-size: 14px; margin-bottom: 25px; }
        .btn-main { background: #2ecc71; color: white; border: none; padding: 18px; border-radius: 15px; font-size: 18px; font-weight: bold; width: 100%; cursor: pointer; transition: 0.3s; box-shadow: 0 4px 15px rgba(46, 204, 113, 0.3); }
        .btn-main:active { transform: scale(0.98); background: #27ae60; }
        #preview { width: 100%; border-radius: 15px; margin-top: 20px; display: none; border: 2px solid #333; }
        #loading { display: none; margin: 20px 0; color: #f1c40f; font-weight: bold; animation: blink 1.5s infinite; }
        @keyframes blink { 0% { opacity: 1; } 50% { opacity: 0.5; } 100% { opacity: 1; } }
        #resultat { margin-top: 25px; padding: 20px; background: #2a2a2a; border-radius: 15px; text-align: left; border-left: 5px solid #2ecc71; line-height: 1.6; font-size: 16px; }
        .footer { margin-top: 30px; font-size: 12px; color: #555; }
    </style>
</head>
<body>

<div class="card">
    <h1>🥬 Expert Vitamine K</h1>
    <p>Prenez une photo d'un aliment pour connaître son taux de Vitamine K.</p>
    
    <button class="btn-main" onclick="document.getElementById('photoInput').click()">📸 ANALYSER L'ALIMENT</button>
    
    <input type="file" id="photoInput" accept="image/*" capture="environment" style="display: none;" onchange="analyserImage(event)">

    <div id="loading">⌛ Analyse par l'IA en cours...</div>
    
    <img id="preview" alt="Aperçu de l'aliment">
    
    <div id="resultat">En attente d'une analyse...</div>
    
    <div class="footer">Propulsé par Gemini 1.5 Flash • Analyse instantanée</div>
</div>

<script>
    // Ta clé API Google Gemini
    const API_KEY = "AIzaSyC2OLQp6L3kMNCI1V3nw7bWDkc03Kvw_yE"; 

    async function analyserImage(event) {
        const file = event.target.files[0];
        if (!file) return;

        // Afficher l'aperçu et le chargement
        const reader = new FileReader();
        reader.onload = async function(e) {
            const preview = document.getElementById('preview');
            preview.src = e.target.result;
            preview.style.display = 'block';
            
            document.getElementById('loading').style.display = 'block';
            document.getElementById('resultat').innerHTML = "L'IA examine votre photo...";

            const base64Image = e.target.result.split(',')[1];

            try {
                const response = await fetch(`https://generativelanguage.googleapis.com/v1beta/models/gemini-1.5-flash:generateContent?key=${API_KEY}`, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({
                        contents: [{
                            parts: [
                                { text: "Identifie précisément cet aliment. Donne son taux de Vitamine K pour 100g. Si c'est un plat composé, estime les ingrédients principaux. Réponds de manière claire et courte en français." },
                                { inline_data: { mime_type: "image/jpeg", data: base64Image } }
                            ]
                        }]
                    })
                });

                const data = await response.json();
                
                if (data.candidates && data.candidates[0].content.parts[0].text) {
                    const texteFinal = data.candidates[0].content.parts[0].text;
                    // Formatter un peu le texte pour le rendu HTML
                    document.getElementById('resultat').innerHTML = texteFinal.replace(/\n/g, "<br>");
                } else {
                    document.getElementById('resultat').innerText = "L'IA n'a pas pu identifier l'aliment. Réessayez avec une photo plus nette.";
                }

            } catch (error) {
                console.error(error);
                document.getElementById('resultat').innerText = "Erreur de connexion avec l'IA. Vérifiez votre clé API ou votre connexion.";
            } finally {
                document.getElementById('loading').style.display = 'none';
            }
        };
        reader.readAsDataURL(file);
    }
</script>

</body>
</html>
