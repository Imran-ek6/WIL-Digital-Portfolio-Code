<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>FuelFit</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <div class="app">
        <div id="content" class="content"></div>
        <nav class="bottom-nav">
            <a data-page="home" class="active">🏠<br>Home</a>
            <a data-page="meals">🍴<br>Meals</a>
            <a data-page="workouts">🏋️<br>Workouts</a>
            <a data-page="progress">📈<br>Progress</a>
            <a data-page="about">ℹ️<br>About</a>
            <a data-page="contact">✉️<br>Contact</a>
        </nav>
    </div>

    <script src="script.js"></script>
</body>
</html>

const content = document.getElementById('content');
const navLinks = document.querySelectorAll('.bottom-nav a');
const profileKey = 'fuelfit_profile';

let profile = JSON.parse(localStorage.getItem(profileKey)) || {
    goal: 'muscle_gain',
    daily_calorie_target: 2500,
    daily_burn_target: 300,
    calories_consumed: 0,
    calories_burned: 0
};

const goals = {
    muscle_gain: { label: 'Muscle Gain', calories: 2500, burn: 300 },
    weight_loss: { label: 'Weight Loss', calories: 1800, burn: 500 },
    maintenance: { label: 'Maintenance', calories: 2200, burn: 200 }
};

const allMeals = {
    muscle_gain: [
        { name: 'Avocado Egg Toast', calories: 550, time: 15, protein: 35, carbs: 20, fat: 40, category: 'Breakfast', img: 'https://www.eatingwell.com/thmb/ZHXRyJgN5ikb5Zk0SMw1XhQ8W9g=/1500x0/filters:no_upscale():max_bytes(150000):strip_icc()/EWL-267169-avocado-egg-toast-Hero-02-037627ac211748fc857b5a69989fa8e9.jpg' },
        { name: 'Protein Oats Shake', calories: 600, time: 10, protein: 45, carbs: 60, fat: 15, category: 'Breakfast', img: 'https://madaboutfood.co/wp-content/uploads/2025/11/Protein-Oatmeal-4-edited.jpg' },
        { name: 'BBQ Chicken Rice Bowl', calories: 750, time: 30, protein: 60, carbs: 80, fat: 25, category: 'Lunch', img: 'https://www.simplyrecipes.com/thmb/14P7cr3y293xVZjhBAmF-6dSvcI=/1500x0/filters:no_upscale():max_bytes(150000):strip_icc()/__opt__aboutcom__coeus__resources__content_migration__simply_recipes__uploads__2016__07__2016-08-03-BBQ-Chicken-Bowls-12-0585976085e246f3aaf1df864d090395.jpg' },
        { name: 'Steak & Rice Bowl', calories: 650, time: 30, protein: 50, carbs: 55, fat: 22, category: 'Dinner', img: 'https://hungryhealthyhappy.com/wp-content/uploads/2025/01/steak-and-rice-bowl-featured-500x375.jpg' }
    ],
    weight_loss: [
        { name: 'Greek Yogurt & Berries', calories: 250, time: 5, protein: 20, carbs: 25, fat: 5, category: 'Breakfast', img: 'https://www.virtua.org/-/media/Project/Virtua-Tenant/Virtua/Images/Articles/Stock/recipe-for-gut-health-berry-yogurt-parfait.jpg?h=900&iar=0&w=1600&hash=4F61821DF22E51FAB9A3A0D00BB7FFDD' },
        { name: 'Grilled Chicken Salad', calories: 400, time: 20, protein: 45, carbs: 15, fat: 18, category: 'Lunch', img: 'https://www.mealpro.net/wp-content/uploads/2023/03/Weight-Loss-Chicken-Salad.jpg' },
        { name: 'Grilled Fish & Veggies', calories: 380, time: 25, protein: 40, carbs: 20, fat: 12, category: 'Dinner', img: 'https://www.healthyfood.com/wp-content/uploads/2016/09/Grilled-fish-and-vege-rice.jpg' }
    ],
    maintenance: [
        { name: 'Oatmeal with Fruit & Nuts', calories: 450, time: 10, protein: 15, carbs: 60, fat: 20, category: 'Breakfast', img: 'https://s.yimg.com/ny/api/res/1.2/OzmP4NfSFmfJAC7ki9Ba9Q--/YXBwaWQ9aGlnaGxhbmRlcjt3PTY0MDtoPTQ4MA--/https://media.zenfs.com/en/aol_insider_articles_823/121a97d6662dedacf06030d8fa52b70a' },
        { name: 'Quinoa Chicken Bowl', calories: 550, time: 25, protein: 40, carbs: 50, fat: 20, category: 'Lunch', img: 'https://styleblueprint.com/wp-content/uploads/2025/01/Southwest-Chicken-Quinoa-Salad-feat.jpeg' },
        { name: 'Chicken Pasta with Veggies', calories: 600, time: 30, protein: 45, carbs: 65, fat: 18, category: 'Dinner', img: 'https://www.allrecipes.com/thmb/_7K5t-zfB8zwhC29CBhveU70DYY=/1500x0/filters:no_upscale():max_bytes(150000):strip_icc()/One-Pan-Chicken-Pasta-Romano-4x3-1-2000-8de21961bb3a4a5abd6eaa3c9c7668df.jpg' }
    ]
};

const allWorkouts = {
    muscle_gain: [
        { name: 'Push Day', exercises: 6, time: 45, calories: 280, muscles: ['Chest', 'Shoulders', 'Triceps'], img: 'https://i.shgcdn.com/fb804e90-1738-4f87-a46c-d01040b3a76e/-/format/auto/-/preview/3000x3000/-/quality/lighter/' },
        { name: 'Pull Day', exercises: 6, time: 45, calories: 260, muscles: ['Back', 'Biceps', 'Rear Delts'], img: 'https://cdn.shopify.com/s/files/1/1214/7132/files/mt-barbell-row-900x675_c353b37f-4b69-4db5-bcbe-1c6f2225b973.jpg?v=1706738858' }
    ],
    weight_loss: [
        { name: 'HIIT Cardio Blast', exercises: 8, time: 30, calories: 450, muscles: ['Full Body'], img: 'https://www.nursebuff.com/wp-content/uploads/2019/11/30-day-hiit-challenge.jpg' }
    ],
    maintenance: [
        { name: 'Full Body Strength', exercises: 8, time: 45, calories: 350, muscles: ['All Major Groups'], img: 'https://media-cldnry.s-nbcnews.com/image/upload/t_fit-760w,f_auto,q_auto:best/newscms/2019_13/2804151/better-april-2019-workout-calendar.jpg' }
    ]
};

function saveProfile() {
    localStorage.setItem(profileKey, JSON.stringify(profile));
}

function updateGoal(goal) {
    profile.goal = goal;
    profile.daily_calorie_target = goals[goal].calories;
    profile.daily_burn_target = goals[goal].burn;
    profile.calories_consumed = 0;
    profile.calories_burned = 0;
    saveProfile();
    renderHome();
}

function setActive(page) {
    navLinks.forEach(l => l.classList.remove('active'));
    document.querySelector(`[data-page="${page}"]`).classList.add('active');
}

let currentMealFilter = 'All';

// ADDED: Logging counts + popup
let mealsLoggedToday = parseInt(localStorage.getItem('mealsLoggedToday') || '0');
let workoutsLoggedToday = parseInt(localStorage.getItem('workoutsLoggedToday') || '0');

function logMeal(calories, name) {
    profile.calories_consumed += calories;
    mealsLoggedToday++;
    localStorage.setItem('mealsLoggedToday', mealsLoggedToday);
    saveProfile();
    alert(`${name} logged! +${calories} cal consumed 🔥`);
    renderHome();
    renderMeals();
    renderProgress();
}

function logWorkout(calories, name) {
    profile.calories_burned += calories;
    workoutsLoggedToday++;
    localStorage.setItem('workoutsLoggedToday', workoutsLoggedToday);
    saveProfile();
    alert(`${name} completed! +${calories} cal burned ⚡`);
    renderHome();
    renderWorkouts();
    renderProgress();
}
// END ADDED

// ADDED: Meal details with ingredients and recipe
const mealDetails = {
    'Avocado Egg Toast': {
        info: '550 cal · 15 min · Protein: 35g · Carbs: 20g · Fat: 40g · Breakfast',
        ingredients: '- 2 slices whole grain bread<br>- 1 ripe avocado<br>- 2 eggs<br>- Salt, pepper, chili flakes (optional)',
        recipe: '1. Toast bread.<br>2. Mash avocado with salt/pepper.<br>3. Spread on toast.<br>4. Cook eggs (poached/fried).<br>5. Top toast with eggs and chili flakes.'
    },
    'Protein Oats Shake': {
        info: '600 cal · 10 min · Protein: 45g · Carbs: 60g · Fat: 15g · Breakfast',
        ingredients: '- 50g rolled oats<br>- 1 scoop protein powder<br>- 300ml milk<br>- 1 banana<br>- 1 tbsp peanut butter',
        recipe: '1. Blend all ingredients until smooth.<br>2. Serve chilled.'
    },
    'BBQ Chicken Rice Bowl': {
        info: '750 cal · 30 min · Protein: 60g · Carbs: 80g · Fat: 25g · Lunch',
        ingredients: '- 200g chicken breast<br>- 100g cooked rice<br>- BBQ sauce<br>- Mixed veggies (peppers, onion)',
        recipe: '1. Grill chicken with BBQ sauce.<br>2. Sauté veggies.<br>3. Serve over rice.'
    },
    'Steak & Rice Bowl': {
        info: '650 cal · 30 min · Protein: 50g · Carbs: 55g · Fat: 22g · Dinner',
        ingredients: '- 200g sirloin steak<br>- 100g brown rice<br>- Broccoli, carrots<br>- Garlic, soy sauce',
        recipe: '1. Sear steak medium-rare, slice.<br>2. Stir-fry veggies.<br>3. Serve over rice.'
    },
    'Greek Yogurt & Berries': {
        info: '250 cal · 5 min · Protein: 20g · Carbs: 25g · Fat: 5g · Breakfast',
        ingredients: '- 200g low-fat Greek yogurt<br>- 150g mixed berries<br>- Optional: 1 tsp honey',
        recipe: '1. Layer yogurt and berries in a bowl.<br>2. Drizzle honey if desired.'
    },
    'Grilled Chicken Salad': {
        info: '400 cal · 20 min · Protein: 45g · Carbs: 15g · Fat: 18g · Lunch',
        ingredients: '- 150g grilled chicken breast<br>- Mixed greens<br>- Cherry tomatoes, cucumber, onion<br>- Lemon juice + 1 tsp olive oil',
        recipe: '1. Grill chicken.<br>2. Chop veggies.<br>3. Toss with dressing.'
    },
    'Grilled Fish & Veggies': {
        info: '380 cal · 25 min · Protein: 40g · Carbs: 20g · Fat: 12g · Dinner',
        ingredients: '- 150g white fish<br>- Asparagus, zucchini, peppers<br>- Lemon, garlic, herbs',
        recipe: '1. Season fish and veggies.<br>2. Grill/bake 15-20 min.'
    },
    'Oatmeal with Fruit & Nuts': {
        info: '450 cal · 10 min · Protein: 15g · Carbs: 60g · Fat: 20g · Breakfast',
        ingredients: '- 50g rolled oats<br>- 250ml milk<br>- 1 apple/banana<br>- 10g almonds/walnuts',
        recipe: '1. Cook oats in milk.<br>2. Top with sliced fruit and nuts.'
    },
    'Quinoa Chicken Bowl': {
        info: '550 cal · 25 min · Protein: 40g · Carbs: 50g · Fat: 20g · Lunch',
        ingredients: '- 100g cooked quinoa<br>- 150g chicken breast<br>- Avocado, tomatoes, corn<br>- Lime dressing',
        recipe: '1. Cook quinoa.<br>2. Grill chicken.<br>3. Combine with veggies and dressing.'
    },
    'Chicken Pasta with Veggies': {
        info: '600 cal · 30 min · Protein: 45g · Carbs: 65g · Fat: 18g · Dinner',
        ingredients: '- 100g whole wheat pasta<br>- 150g chicken<br>- Broccoli, spinach, garlic<br>- Light tomato sauce',
        recipe: '1. Cook pasta.<br>2. Sauté chicken and veggies.<br>3. Mix with sauce.'
    }
};

function showMealDetails(mealName) {
    const details = mealDetails[mealName];
    if (!details) return;

    content.innerHTML = `
        <div class="flex" style="align-items:center;position:relative;margin-bottom:1rem">
            <span class="back" onclick="renderMeals()">&lt;</span>
            <h1 style="margin:0 auto;">${mealName}</h1>
        </div>
        <div class="card" style="text-align:left;">
            <p style="font-size:1.2rem;margin-bottom:1rem;">${details.info}</p>
            <h3 style="color:var(--teal);margin:1.5rem 0 0.5rem;">Ingredients:</h3>
            <p style="line-height:1.8;">${details.ingredients}</p>
            <h3 style="color:var(--teal);margin:1.5rem 0 0.5rem;">Recipe:</h3>
            <p style="line-height:1.8;">${details.recipe}</p>
            <div class="btn" style="margin-top:2rem;" onclick="renderMeals()">Back to Meals</div>
        </div>
    `;
}
// END ADDED

// ADDED: Workout details with exercises list
const workoutDetails = {
    'Push Day': {
        info: '6 exercises · 45 min · 280 cal<br>Muscles: Chest, Shoulders, Triceps',
        exercises: '- Bench Press – 4x10<br>- Overhead Press – 4x10<br>- Incline Dumbbell Press – 3x12<br>- Tricep Dips – 3x15<br>- Lateral Raises – 3x15<br>- Skull Crushers – 3x12'
    },
    'Pull Day': {
        info: '6 exercises · 45 min · 260 cal<br>Muscles: Back, Biceps, Rear Delts',
        exercises: '- Deadlift – 4x8<br>- Pull-Ups or Lat Pulldown – 4x10<br>- Barbell Row – 4x10<br>- Face Pulls – 3x15<br>- Bicep Curls – 3x12<br>- Hammer Curls – 3x12'
    },
    'HIIT Cardio Blast': {
        info: '8 rounds · 30 min · 450 cal<br>Muscles: Full Body',
        exercises: '- 30 sec Burpees<br>- 30 sec Mountain Climbers<br>- 30 sec Jump Squats<br>- 30 sec High Knees<br>- 30 sec rest<br>Repeat 8 times'
    },
    'Full Body Strength': {
        info: '8 exercises · 45 min · 350 cal<br>Muscles: All Major Groups',
        exercises: '- Squats – 4x10<br>- Push-Ups – 3x15<br>- Dumbbell Rows – 3x12 each arm<br>- Lunges – 3x12 each leg<br>- Plank – 3x45 sec<br>- Glute Bridges – 3x15<br>- Overhead Press – 3x12<br>- Deadlift Variation – 3x10'
    }
};

function showWorkoutDetails(workoutName) {
    const details = workoutDetails[workoutName];
    if (!details) return;

    content.innerHTML = `
        <div class="flex" style="align-items:center;position:relative;margin-bottom:1rem">
            <span class="back" onclick="renderWorkouts()">&lt;</span>
            <h1 style="margin:0 auto;">${workoutName}</h1>
        </div>
        <div class="card" style="text-align:left;">
            <p style="font-size:1.2rem;margin-bottom:1rem;">${details.info}</p>
            <h3 style="color:var(--red);margin:1.5rem 0 0.5rem;">Exercises:</h3>
            <p style="line-height:1.8;">${details.exercises}</p>
            <div class="btn" style="margin-top:2rem;" onclick="renderWorkouts()">Back to Workouts</div>
        </div>
    `;
}
// END ADDED

// ADDED FOR 7-DAY DOUBLE LINE GRAPH
function getDateKey(date = new Date()) {
    return date.toISOString().split('T')[0]; // YYYY-MM-DD
}

function getLast7DaysData() {
    const data = [];
    for (let i = 6; i >= 0; i--) {
        const date = new Date();
        date.setDate(date.getDate() - i);
        const key = getDateKey(date);
        const dayData = JSON.parse(localStorage.getItem(`day_${key}`) || '{"consumed":0,"burned":0}');
        const dayName = date.toLocaleDateString('en-US', { weekday: 'short' }).slice(0, 3);
        data.push({ day: dayName, consumed: dayData.consumed || 0, burned: dayData.burned || 0 });
    }
    return data;
}

function saveTodayData() {
    const todayKey = getDateKey();
    localStorage.setItem(`day_${todayKey}`, JSON.stringify({
        consumed: profile.calories_consumed,
        burned: profile.calories_burned
    }));
}
// Call this whenever calories change
function logMeal(calories, name) {
    profile.calories_consumed += calories;
    mealsLoggedToday++;
    localStorage.setItem('mealsLoggedToday', mealsLoggedToday);
    saveProfile();
    saveTodayData(); // ADDED
    alert(`${name} logged! +${calories} cal consumed 🔥`);
    renderHome();
    renderMeals();
    renderProgress();
}

function logWorkout(calories, name) {
    profile.calories_burned += calories;
    workoutsLoggedToday++;
    localStorage.setItem('workoutsLoggedToday', workoutsLoggedToday);
    saveProfile();
    saveTodayData(); // ADDED
    alert(`${name} completed! +${calories} cal burned ⚡`);
    renderHome();
    renderWorkouts();
    renderProgress();
}
// END ADDED

// ADDED: Reset All Progress with confirmation popup
function resetAllProgress() {
    if (confirm('⚠️ WARNING: This will permanently erase ALL your progress!\n\n- Logged meals & workouts\n- Calorie history (7-day graph)\n- Daily counters\n\nAre you absolutely sure?')) {
        // Reset profile calories
        profile.calories_consumed = 0;
        profile.calories_burned = 0;
        saveProfile();

        // Reset daily counters
        mealsLoggedToday = 0;
        workoutsLoggedToday = 0;
        localStorage.setItem('mealsLoggedToday', '0');
        localStorage.setItem('workoutsLoggedToday', '0');

        // Clear all daily history data
        const keys = Object.keys(localStorage);
        keys.forEach(key => {
            if (key.startsWith('day_')) {
                localStorage.removeItem(key);
            }
        });

        alert('All progress has been reset! Starting fresh 🔥');
        renderProgress(); // Refresh page to show empty rings/graph
    }
}
// END ADDED

// ADDED: Local logos from your laptop
const LOGO1_URL = 'LOGO1.png';  // Home page logo
const LOGO2_URL = 'LOGO2.png';  // About & Contact pages logo
// END ADDED

window.renderHome = function() {
    setActive('home');
    const remaining = profile.daily_calorie_target - profile.calories_consumed + profile.calories_burned;
    const outerCirc = 754;   // outer ring circumference (r=120)
    const innerCirc = 660;   // inner ring circumference (r=105)
    const consumedOffset = innerCirc - (profile.calories_consumed / profile.daily_calorie_target) * innerCirc;
    const burnedOffset = outerCirc - (profile.calories_burned / profile.daily_burn_target) * outerCirc;

    content.innerHTML = `
        <!-- LOGO1 at the very top on Home page -->
        <div style="text-align:center;margin:2rem 0;">
            <img src="${LOGO1_URL}" alt="FuelFit Logo" style="height:120px;">
        </div>

        <h1 class="welcome-title">Welcome back!</h1>
        <p class="subtitle">Let's crush your goals today</p>

        <select class="goal-select" onchange="updateGoal(this.value)">
            <option value="muscle_gain" ${profile.goal === 'muscle_gain' ? 'selected' : ''}>Muscle Gain</option>
            <option value="weight_loss" ${profile.goal === 'weight_loss' ? 'selected' : ''}>Weight Loss</option>
            <option value="maintenance" ${profile.goal === 'maintenance' ? 'selected' : ''}>Maintenance</option>
        </select>

        <div class="ring-container">
            <svg width="280" height="280" viewBox="0 0 280 280">
                <!-- Grey base for outer ring -->
                <circle cx="140" cy="140" r="120" fill="none" stroke="#2a3f5f" stroke-width="12"/>
                <!-- Inner ring: Consumed (teal) -->
                <circle cx="140" cy="140" r="105" fill="none" stroke="var(--teal)" stroke-width="12" stroke-dasharray="${innerCirc}" stroke-dashoffset="${consumedOffset}" transform="rotate(-90 140 140)"/>
                <!-- Outer ring: Burned (red) -->
                <circle cx="140" cy="140" r="120" fill="none" stroke="var(--red)" stroke-width="12" stroke-dasharray="${outerCirc}" stroke-dashoffset="${burnedOffset}" transform="rotate(-90 140 140)"/>
            </svg>
            <div class="ring-text-center">
                <div class="cal-number">${remaining > 0 ? remaining : 0}</div>
                <div class="cal-label">cal remaining</div>
            </div> <!-- ADDED FOR CENTERED CAL REMAINING TEXT -->
        </div>

        <div style="margin:2rem 0; display:flex; justify-content:center; gap:3rem; font-size:1.1rem;">
            <span style="color:var(--teal)">● Eaten (${profile.calories_consumed} cal)</span>
            <span style="color:var(--red)">● Burned (${profile.calories_burned} cal)</span>
        </div>

        <div class="grid-2">
            <div class="card"><p style="color:var(--teal)">Meals Today</p><strong style="font-size:2rem">${mealsLoggedToday} / 4</strong></div>
            <div class="card"><p style="color:var(--red)">Workouts</p><strong style="font-size:2rem">${workoutsLoggedToday} done</strong></div>
            <div class="card"><p>Target</p><strong style="font-size:2rem">${profile.daily_calorie_target} cal</strong></div>
            <div class="card"><p>Burn Goal</p><strong style="font-size:2rem">${profile.daily_burn_target} cal</strong></div>
        </div>

        <div class="card" onclick="renderMeals()"><div class="flex">🍴 Meal Plans <span>&gt;</span></div><p>Discover meals for your goal</p></div>
        <div class="card" onclick="renderWorkouts()"><div class="flex">🏋️ Workouts <span>&gt;</span></div><p>Start your training</p></div>
        <div class="card" onclick="renderProgress()"><div class="flex">📈 View Progress <span>&gt;</span></div><p>Track your journey over time</p></div>
    `;
};

window.renderMeals = function() {
    setActive('meals');
    const baseMeals = allMeals[profile.goal] || [];
    const meals = currentMealFilter === 'All' ? baseMeals : baseMeals.filter(m => m.category === currentMealFilter);

    const mealsHtml = meals.map(m => `
        <div class="card" style="position:relative;">
            <img src="${m.img}" class="meal-img" alt="${m.name}">
            <span class="meal-label">${m.category.toUpperCase()}</span>
            <h2>${m.name}</h2>
            <p>🔥 ${m.calories} cal &nbsp; ⏱ ${m.time} min</p>
            <div class="grid-3">
                <div class="card text-center"><p>Protein</p><strong>${m.protein}g</strong></div>
                <div class="card text-center"><p>Carbs</p><strong>${m.carbs}g</strong></div>
                <div class="card text-center"><p>Fat</p><strong>${m.fat}g</strong></div>
            </div>
            <div class="grid-2" style="margin-top:1rem;">
                <div class="btn btn-outline" onclick="showMealDetails('${m.name.replace(/'/g, "\\'")}')">View</div>
                <div class="btn" onclick="logMeal(${m.calories}, '${m.name.replace(/'/g, "\\'")}')">+ Log</div>
            </div>
        </div>
    `).join('');

    content.innerHTML = `
        <div class="flex" style="align-items:center;position:relative;margin-bottom:1rem">
            <span class="back" onclick="renderHome()">&lt;</span>
            <h1 style="margin:0 auto;">Meal Plans</h1>
        </div>
        <p>Meals tailored for ${goals[profile.goal].label}</p>
        <input type="text" placeholder="Search meals..." class="card" style="padding:1.2rem;background:var(--card);border:none;">
        <div class="tabs">
            <div class="tab ${currentMealFilter==='All' ? 'tab-active' : ''}" onclick="currentMealFilter='All'; renderMeals()">All</div>
            <div class="tab ${currentMealFilter==='Breakfast' ? 'tab-active' : ''}" onclick="currentMealFilter='Breakfast'; renderMeals()">Breakfast</div>
            <div class="tab ${currentMealFilter==='Lunch' ? 'tab-active' : ''}" onclick="currentMealFilter='Lunch'; renderMeals()">Lunch</div>
            <div class="tab ${currentMealFilter==='Dinner' ? 'tab-active' : ''}" onclick="currentMealFilter='Dinner'; renderMeals()">Dinner</div>
        </div>
        <div class="grid-2">
            <div class="card"><p>Today's Intake</p><strong>${profile.calories_consumed} / ${profile.daily_calorie_target} cal</strong></div>
            <div class="card"><p>Meals logged</p><strong>${mealsLoggedToday}</strong></div>
        </div>
        ${mealsHtml || '<p>No meals in this category yet.</p>'}
    `;
};

window.renderWorkouts = function() {
    setActive('workouts');
    const workouts = allWorkouts[profile.goal] || [];
    const workoutsHtml = workouts.map(w => `
        <div class="card flex" style="gap:1rem;align-items:center;">
            <img src="${w.img}" style="width:120px;height:120px;border-radius:16px;object-fit:cover;" alt="${w.name}">
            <div style="flex:1;">
                <h3>${w.name}</h3>
                <p>${w.exercises} exercises • ${w.time} min • 🔥 ${w.calories} cal</p>
                <div class="tabs" style="margin-top:0.5rem;">
                    ${w.muscles.map(m => `<div class="tab">${m}</div>`).join('')}
                </div>
                <div class="grid-2" style="margin-top:1rem;">
                    <div class="btn btn-outline" onclick="showWorkoutDetails('${w.name.replace(/'/g, "\\'")}')">View</div>
                    <div class="btn" onclick="logWorkout(${w.calories}, '${w.name.replace(/'/g, "\\'")}')">Start</div>
                </div>
            </div>
        </div>
    `).join('');

    content.innerHTML = `
        <div class="flex" style="align-items:center;position:relative;margin-bottom:1rem">
            <span class="back" onclick="renderHome()">&lt;</span>
            <h1 style="margin:0 auto;">Workouts</h1>
        </div>
        <p>Training for ${goals[profile.goal].label}</p>
        <div class="grid-2">
            <div class="card" style="color:var(--red)"><p>Today's Burn</p><strong>${profile.calories_burned} / ${profile.daily_burn_target} cal</strong></div>
            <div class="card" style="color:var(--red)"><p>Workouts done</p><strong>${workoutsLoggedToday}</strong></div>
        </div>
        ${workoutsHtml || '<p>No workouts for this goal yet.</p>'}
    `;
};

window.renderProgress = function() {
    setActive('progress');
    const remaining = profile.daily_calorie_target - profile.calories_consumed + profile.calories_burned;
    const outerCirc = 754;
    const innerCirc = 660;
    const consumedOffset = innerCirc - (profile.calories_consumed / profile.daily_calorie_target) * innerCirc;
    const burnedOffset = outerCirc - (profile.calories_burned / profile.daily_burn_target) * outerCirc;

    // Get last 7 days data
    const weekData = getLast7DaysData();
    const maxCal = Math.max(...weekData.flatMap(d => [d.consumed, d.burned]), profile.daily_calorie_target) || 2500;
    const height = 200;
    const width = 340;
    const padding = 40;
    const graphHeight = height - padding * 2;
    const graphWidth = width - padding * 2;
    const pointSpacing = graphWidth / 6;

    // Generate path for consumed (teal line)
    let consumedPath = `M ${padding} ${height - padding - (weekData[0].consumed / maxCal * graphHeight)}`;
    let burnedPath = `M ${padding} ${height - padding - (weekData[0].burned / maxCal * graphHeight)}`;
    for (let i = 1; i < 7; i++) {
        const x = padding + i * pointSpacing;
        const yConsumed = height - padding - (weekData[i].consumed / maxCal * graphHeight);
        const yBurned = height - padding - (weekData[i].burned / maxCal * graphHeight);
        consumedPath += ` L ${x} ${yConsumed}`;
        burnedPath += ` L ${x} ${yBurned}`;
    }

    content.innerHTML = `
        <div class="flex" style="align-items:center;position:relative;margin-bottom:1rem">
            <span class="back" onclick="renderHome()">&lt;</span>
            <h1 style="margin:0 auto;">Your Progress</h1>
        </div>
        <p class="text-center">Track your fitness journey</p>
        <h2 class="text-center">Today</h2>
        <div class="ring-container">
            <svg width="280" height="280" viewBox="0 0 280 280">
                <circle cx="140" cy="140" r="120" fill="none" stroke="#2a3f5f" stroke-width="12"/>
                <circle cx="140" cy="140" r="105" fill="none" stroke="var(--teal)" stroke-width="12" stroke-dasharray="${innerCirc}" stroke-dashoffset="${consumedOffset}" transform="rotate(-90 140 140)"/>
                <circle cx="140" cy="140" r="120" fill="none" stroke="var(--red)" stroke-width="12" stroke-dasharray="${outerCirc}" stroke-dashoffset="${burnedOffset}" transform="rotate(-90 140 140)"/>
            </svg>
            <div class="ring-text-center">
                <div class="cal-number">${remaining > 0 ? remaining : 0}</div>
                <div class="cal-label">cal remaining</div>
            </div>
        </div>

        <!-- 7-Day Double Line Graph -->
        <div class="card" style="margin:2rem 0;padding:1.5rem;">
            <h3 style="text-align:center;margin-bottom:1rem;color:var(--red);">Calories (7 Days)</h3>
            <svg width="${width}" height="${height}" viewBox="0 0 ${width} ${height}" style="background:rgba(255,255,255,0.05);border-radius:16px;">
                <!-- Grid lines -->
                <line x1="${padding}" y1="${height - padding}" x2="${width - padding}" y2="${height - padding}" stroke="#2a3f5f" stroke-width="1"/>
                <line x1="${padding}" y1="${height - padding - graphHeight/4}" x2="${width - padding}" y2="${height - padding - graphHeight/4}" stroke="#2a3f5f" stroke-width="1" opacity="0.5"/>
                <line x1="${padding}" y1="${height - padding - graphHeight/2}" x2="${width - padding}" y2="${height - padding - graphHeight/2}" stroke="#2a3f5f" stroke-width="1" opacity="0.5"/>
                <line x1="${padding}" y1="${height - padding - 3*graphHeight/4}" x2="${width - padding}" y2="${height - padding - 3*graphHeight/4}" stroke="#2a3f5f" stroke-width="1" opacity="0.5"/>
                <line x1="${padding}" y1="${padding}" x2="${width - padding}" y2="${padding}" stroke="#2a3f5f" stroke-width="1" opacity="0.5"/>

                <!-- Consumed line (teal) -->
                <path d="${consumedPath}" fill="none" stroke="var(--teal)" stroke-width="4" stroke-linecap="round" stroke-linejoin="round"/>
                <!-- Burned line (red) -->
                <path d="${burnedPath}" fill="none" stroke="var(--red)" stroke-width="4" stroke-linecap="round" stroke-linejoin="round"/>

                <!-- Day labels -->
                ${weekData.map((d, i) => `<text x="${padding + i * pointSpacing}" y="${height - 10}" text-anchor="middle" fill="#aaa" font-size="12">${d.day}</text>`).join('')}
            </svg>
            <div style="display:flex;justify-content:center;gap:3rem;margin-top:1rem;font-size:1rem;">
                <span style="color:var(--teal)">● Consumed</span>
                <span style="color:var(--red)">● Burned</span>
            </div>
        </div>

        <!-- Reset All Progress Button -->
        <div class="btn" style="background:var(--red);max-width:320px;margin:2rem auto;" onclick="resetAllProgress()">
            Reset All Progress
        </div>

        <div class="text-center" style="margin:2rem 0;">
            <span style="color:var(--teal)">● Eaten (${profile.calories_consumed} cal)</span> &nbsp;&nbsp;&nbsp;
            <span style="color:var(--red)">● Burned (${profile.calories_burned} cal)</span>
        </div>
        <div class="card"><p>Goal</p><strong>${goals[profile.goal].label}</strong></div>
        <div class="card" style="color:var(--teal)"><p>Daily Target</p><strong>${profile.daily_calorie_target} cal</strong></div>
        <div class="card" style="color:var(--red)"><p>Burn Target</p><strong>${profile.daily_burn_target} cal</strong></div>
    `;
};

window.renderAbout = function() {
    setActive('about');
    content.innerHTML = `
        <!-- LOGO2 at the very top on About page -->
        <div style="text-align:center;margin:2rem 0;">
            <img src="${LOGO2_URL}" alt="FuelFit Logo" style="height:120px;">
        </div>

        <div class="flex" style="align-items:center;position:relative;margin-bottom:1rem">
            <span class="back" onclick="renderHome()">&lt;</span>
            <h1 style="margin:0 auto;">About Us</h1>
        </div>

        <h2>What Are We About?</h2>
        <p>FuelFit is a mobile health and fitness app designed to help individuals achieve their wellness goals through personalized meal planning, structured workout programs, hydration tracking, and motivational support.</p>

        <h2>Mission Statement</h2>
        <p>FuelFit's mission is to provide easy-to-use tools that support users on their fitness journey through daily meal plans, workout routines, hydration tracking, helping them stay focused, consistent, and committed to long-term health goals.</p>

        <h2>The Spark</h2>
        <p>FuelFit was born from the frustration many people experience when trying to stay consistent with health goals. Most apps focused only on one area (fitness OR diet), were too complex, or required heavy data usage.</p>

        <h2>Brand Values</h2>

        <div class="card flex" style="gap:1rem;align-items:center;">
            <span style="font-size:2.5rem;color:var(--teal);">❤️</span>
            <div><strong>Simplicity</strong><p>Easy-to-use tools that fit your lifestyle</p></div>
        </div>
        <div class="card flex" style="gap:1rem;align-items:center;">
            <span style="font-size:2.5rem;color:var(--teal);">🎯</span>
            <div><strong>Consistency</strong><p>Daily support to keep you on track</p></div>
        </div>
        <div class="card flex" style="gap:1rem;align-items:center;">
            <span style="font-size:2.5rem;color:var(--teal);">👥</span>
            <div><strong>Accessibility</strong><p>Designed for everyone, everywhere</p></div>
        </div>
        <div class="card flex" style="gap:1rem;align-items:center;">
            <span style="font-size:2.5rem;color:var(--teal);">⚡</span>
            <div><strong>Empowerment</strong><p>Take control of your health journey</p></div>
        </div>
        <div class="card flex" style="gap:1rem;align-items:center;">
            <span style="font-size:2.5rem;color:var(--teal);">🛡️</span>
            <div><strong>Inclusivity</strong><p>Welcoming all fitness levels</p></div>
        </div>
    `;
};

window.renderContact = function() {
    setActive('contact');
    content.innerHTML = `
        <!-- LOGO2 at the very top on Contact page -->
        <div style="text-align:center;margin:2rem 0;">
            <img src="${LOGO2_URL}" alt="FuelFit Logo" style="height:120px;">
        </div>

        <div class="flex" style="align-items:center;position:relative;margin-bottom:1rem">
            <span class="back" onclick="renderHome()">&lt;</span>
            <h1 style="margin:0 auto;">Contact Us</h1>
        </div>

        <h2>Connect with FUELFIT</h2>
        <p>We're here to help with your journey.</p>

        <input id="contact-name" type="text" placeholder="Your Name" class="card" style="padding:1.2rem;background:var(--card);border:none;">
        <input id="contact-email" type="email" placeholder="Your Email" class="card" style="padding:1.2rem;background:var(--card);border:none;">
        <textarea id="contact-message" placeholder="Message" rows="5" class="card" style="padding:1.2rem;background:var(--card);border:none;"></textarea>

        <div class="btn" style="width:100%;max-width:280px;margin:1rem auto;padding:0.8rem 1.2rem;font-size:0.95rem;" onclick="sendContactMessage()">✈️ Send Message</div>

        <p class="text-center" style="margin-top:1rem;color:var(--gray);">Have questions? We typically respond within 24 hours.</p>
    `;
};

navLinks.forEach(link => {
    link.addEventListener('click', e => {
        e.preventDefault();
        const page = e.currentTarget.dataset.page;
        window[`render${page.charAt(0).toUpperCase() + page.slice(1)}`]();
    });
});

renderHome();

// Save today's data on load
saveTodayData();

// ADDED: Contact form validation + success popup + smaller button
function sendContactMessage() {
    const name = document.getElementById('contact-name').value.trim();
    const email = document.getElementById('contact-email').value.trim();
    const message = document.getElementById('contact-message').value.trim();

    if (!name || !email || !message) {
        alert('All fields are mandatory. Please fill in Name, Email, and Message.');
        return;
    }

    const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    if (!emailRegex.test(email)) {
        alert('Please enter a valid email address.');
        return;
    }

    alert('Message successfully sent! 🎉');

    // Clear form
    document.getElementById('contact-name').value = '';
    document.getElementById('contact-email').value = '';
    document.getElementById('contact-message').value = '';
}
// END ADDED

:root {
    --bg: #1a2e4a;
    --card: rgba(255,255,255,0.07);
    --red: #e63946;
    --teal: #00ffc5;
    --gray: #a0a0a0;
}

body { margin:0; background:var(--bg); color:#f4f4f4; font-family:system-ui,sans-serif; text-align:center; }
.app { max-width:480px; margin:auto; padding-bottom:80px; }
.content { padding:1rem; }

h1,h2 { color:var(--red); margin:0.5rem 0; font-weight:bold; }
h1 { font-size:1.8rem; }
h2 { font-size:1.6rem; }

.card {
    background:var(--card);
    backdrop-filter:blur(12px);
    border-radius:20px;
    padding:1.2rem;
    margin-bottom:1.2rem;
    transition: transform 0.2s, box-shadow 0.2s;
}

.card:hover { transform: scale(1.02); box-shadow: 0 4px 12px rgba(0,0,0,0.2); }

.flex { display:flex; align-items:center; justify-content:space-between; }
.grid-2 { display:grid; grid-template-columns:1fr 1fr; gap:1rem; }
.grid-3 { display:grid; grid-template-columns:repeat(3,1fr); gap:1rem; }

.tabs {
    display:flex; gap:0.8rem; margin:1.5rem 0; overflow-x:auto;
}
.tab {
    padding:0.6rem 1.2rem; border-radius:999px; background:rgba(255,255,255,0.1); font-size:0.95rem; white-space:nowrap;
    transition: background 0.2s;
}
.tab:hover { background: rgba(255,255,255,0.2); }
.tab-active { background:var(--teal); color:var(--bg); font-weight:bold; }

.btn {
    background:var(--teal); color:var(--bg); padding:0.9rem 1.8rem; border-radius:999px; font-weight:bold; text-align:center;
    transition: background 0.2s, transform 0.2s;
}
.btn:hover { background: #00e0a8; transform: scale(1.05); }
.btn-outline {
    background:transparent; border:1.5px solid var(--teal); color:var(--teal);
}
.btn-outline:hover { background: rgba(0,255,197,0.1); }

.ring-container { position:relative; width:260px; height:260px; margin:2.5rem auto; }
.ring-svg { transform:rotate(-90deg); }
.ring-bg,.ring-eaten,.ring-burned { cx:130; cy:130; r:115; fill:none; stroke-width:24; }
.ring-bg { stroke:#2a3f5f; }
.ring-eaten { stroke:var(--teal); }
.ring-burned { stroke:var(--red); }
.ring-text-center {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    text-align: center;
}
.cal-number {
    font-size: 3rem;
    font-weight: bold;
    line-height: 1;
}
.cal-label {
    font-size: 1rem;
    color: var(--gray);
    margin-top: 0.2rem;
}
.ring-text {
    position:absolute; top:50%; left:50%; transform:translate(-50%,-50%);
    text-align:center; font-size:3rem; font-weight:bold;
}
.ring-text small { display:block; font-size:1.1rem; color:var(--gray); }

.meal-img { 
    width:100%; 
    max-height:180px;   /* Reduced from 220px so images aren't too big */
    object-fit: contain; 
    object-position: center;
    border-radius:20px; 
    margin-bottom:1rem; 
    background:#2a3f5f; /* subtle background if image has transparency */
}

.meal-label {
    position:absolute; top:1.5rem; left:1.5rem; background:var(--red); color:white;
    padding:0.4rem 1rem; border-radius:999px; font-size:0.9rem;
}

.back { font-size:2rem; color:var(--gray); cursor:pointer; }

.bottom-nav {
    position:fixed; bottom:0; left:0; right:0; max-width:480px; margin:auto;
    background:var(--bg); border-top:1px solid rgba(255,255,255,0.1);
    display:flex; justify-content:space-around; padding:1rem 0;
}
.bottom-nav a { color:var(--gray); text-decoration:none; text-align:center; font-size:0.8rem; }
.bottom-nav a.active { color:var(--teal); }

.ring-bg { stroke: #2a3f5f; fill: none; stroke-width: 24; } /* grey background ring */
.ring-eaten { stroke: var(--teal); fill: none; stroke-width: 24; stroke-dasharray: 785; }
.ring-burned { stroke: var(--red); fill: none; stroke-width: 24; stroke-dasharray: 785; }
