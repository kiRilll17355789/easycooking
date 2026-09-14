# easycooking
<!DOCTYPE html>
<html lang="no">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>SmartChef — Global Recipe Finder</title>
    <style>
        body {
            font-family: 'Segoe UI', Arial, sans-serif;
            background-color: #f1f3f5;
            color: #333;
            margin: 0;
            padding: 0;
        }
        header {
            background: linear-gradient(135deg, #2a9d8f, #e76f51);
            color: white;
            text-align: center;
            padding: 30px 20px;
            box-shadow: 0 4px 10px rgba(0,0,0,0.08);
        }
        main {
            max-width: 650px;
            margin: 30px auto;
            padding: 25px;
            background: white;
            border-radius: 16px;
            box-shadow: 0 10px 25px rgba(0,0,0,0.05);
        }
        .form-group {
            margin-bottom: 20px;
        }
        label {
            display: block;
            font-weight: bold;
            margin-bottom: 8px;
            color: #495057;
        }
        input[type="text"] {
            width: 100%;
            padding: 14px;
            border: 2px solid #dee2e6;
            border-radius: 10px;
            box-sizing: border-box;
            font-size: 16px;
            transition: border-color 0.2s;
        }
        input[type="text"]:focus {
            border-color: #2a9d8f;
            outline: none;
        }
        .btn {
            background-color: #2a9d8f;
            color: white;
            border: none;
            padding: 14px 20px;
            font-size: 16px;
            border-radius: 10px;
            cursor: pointer;
            width: 100%;
            font-weight: bold;
            transition: background 0.2s, transform 0.1s;
        }
        .btn:hover { background-color: #264653; }
        .btn:active { transform: scale(0.98); }
        
        .dish-item {
            background: #f8f9fa;
            border: 1px solid #e9ecef;
            padding: 15px;
            margin: 12px 0;
            border-radius: 10px;
            cursor: pointer;
            display: flex;
            justify-content: space-between;
            align-items: center;
            transition: transform 0.2s, border-color 0.2s;
        }
        .dish-item:hover {
            transform: translateX(4px);
            border-color: #2a9d8f;
            background: #f4faf8;
        }
        .recipe-box {
            background: #e2eafc;
            border: 2px dashed #3f37c9;
            padding: 20px;
            margin-top: 25px;
            border-radius: 12px;
            display: none;
            animation: fadeIn 0.4s ease;
        }
        .recipe-img {
            width: 100%;
            height: 250px;
            object-fit: cover;
            border-radius: 8px;
            margin-bottom: 15px;
        }
        
        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }
    </style>
</head>
<body>

<header>
    <h1>🍳 SmartChef: Global API Utgave</h1>
    <p>Søk i en database med tusenvis av retter basert på én hovedingrediens</p>
</header>

<main>
    <div class="form-group">
        <label for="ingredient">Skriv inn ÉN hovedingrediens (på engelsk):</label>
        <input type="text" id="ingredient" placeholder="F.eks: chicken, beef, pork, salmon, potato, tomato...">
    </div>

    <button class="btn" onclick="fetchRecipes()">Søk i live database 🌟</button>

    <div id="results-section" style="margin-top: 30px;">
        <h3>Måltider funnet i databasen:</h3>
        <div id="dishes-list" style="color: #868e96;">Skriv inn en ingrediens og start søket...</div>
    </div>

    <div id="recipe-display" class="recipe-box">
        <img id="recipe-image" class="recipe-img" src="" alt="Rett">
        <h3 id="recipe-title" style="margin-top:0; color: #3f37c9;"></h3>
        <p><strong>Instruksjoner (Engelsk):</strong></p>
        <p id="recipe-text" style="line-height: 1.5; color: #2b2b2b;"></p>
    </div>
</main>

<script>
    async function fetchRecipes() {
        const ingredient = document.getElementById('ingredient').value.toLowerCase().trim();
        const listDiv = document.getElementById('dishes-list');
        const recipeDisplay = document.getElementById('recipe-display');
        
        recipeDisplay.style.display = 'none'; 
        listDiv.innerHTML = 'Søker etter retter...'; 

        if (!ingredient) {
            listDiv.innerHTML = '⚠️ Vennligst skriv inn en ingrediens!';
            return;
        }

        try {
            // Using a free open-source food database API (TheMealDB)
            const response = await fetch(`https://themealdb.com{ingredient}`);
            const data = await response.json();

            if (!data.meals) {
                listDiv.innerHTML = '😔 Ingen retter funnet med den ingrediensen. Prøv vanlige råvarer som: chicken, beef, pork или potato.';
                return;
            }

            listDiv.innerHTML = ''; // Clear status text

            // Display all found items from the database
            data.meals.forEach(meal => {
                const div = document.createElement('div');
                div.className = 'dish-item';
                div.innerHTML = `<span>➡️ <b>${meal.strMeal}</b></span>`;
                
                // Clicking fetches full recipe instructions dynamically
                div.onclick = () => showFullRecipe(meal.idMeal);
                listDiv.appendChild(div);
            });

        } catch (error) {
            listDiv.innerHTML = '❌ Klarte ikke å koble til databasen. Sjekk internettforbindelsen din.';
        }
    }

    async function showFullRecipe(id) {
        const recipeDisplay = document.getElementById('recipe-display');
        
        try {
            const response = await fetch(`https://themealdb.com{id}`);
            const data = await response.json();
            const meal = data.meals[0];

            document.getElementById('recipe-title').innerText = meal.strMeal;
            document.getElementById('recipe-text').innerText = meal.strInstructions;
            document.getElementById('recipe-image').src = meal.strMealThumb;
            
            recipeDisplay.style.display = 'block';
            recipeDisplay.scrollIntoView({ behavior: 'smooth' });
        } catch (error) {
            alert('Kunne ikke laste inn oppskriften.');
        }
    }
</script>
</body>
</html>
