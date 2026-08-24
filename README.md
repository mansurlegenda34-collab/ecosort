# ecosort
<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<title>EcoSort AI</title>

<script src="https://cdn.jsdelivr.net/npm/@tensorflow/tfjs@4.22.0/dist/tf.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/@teachablemachine/image@0.8.5/dist/teachablemachine-image.min.js"></script>

<style>
* {
    box-sizing: border-box;
}

body {
    margin: 0;
    font-family: Arial, sans-serif;
    color: white;
    min-height: 100vh;
    background:
        radial-gradient(circle at 20% 20%, #35d07f55, transparent 30%),
        radial-gradient(circle at 80% 80%, #168f5a55, transparent 30%),
        linear-gradient(135deg, #061b12, #0b4028, #061b12);
}

.container {
    width: 92%;
    max-width: 1000px;
    margin: auto;
    padding: 30px 0 60px;
}

header {
    display: flex;
    justify-content: space-between;
    align-items: center;
}

.logo {
    font-size: 28px;
    font-weight: bold;
}

.logo span {
    color: #72e6a0;
}

.badge {
    background: #ffffff20;
    padding: 10px 16px;
    border-radius: 30px;
}

.hero {
    text-align: center;
    padding: 60px 10px 35px;
}

.hero h1 {
    font-size: 52px;
    margin: 15px 0;
}

.hero h1 span {
    color: #72e6a0;
}

.hero p {
    color: #c9ddd1;
    font-size: 18px;
}

.card {
    background: rgba(255,255,255,0.96);
    color: #173126;
    border-radius: 28px;
    padding: 30px;
    margin-top: 25px;
    box-shadow: 0 20px 60px #0006;
}

.camera {
    min-height: 320px;
    background: #edf5ef;
    border-radius: 20px;
    display: flex;
    justify-content: center;
    align-items: center;
    overflow: hidden;
}

button {
    border: none;
    border-radius: 15px;
    padding: 16px 25px;
    font-size: 16px;
    font-weight: bold;
    cursor: pointer;
}

.start {
    margin-top: 20px;
    background: #208d59;
    color: white;
}

.start:hover {
    background: #176f45;
}

#status {
    text-align: center;
    margin-top: 18px;
    font-weight: bold;
}

.prediction {
    margin-top: 18px;
}

.prediction-title {
    display: flex;
    justify-content: space-between;
    font-weight: bold;
}

.bar {
    height: 12px;
    margin-top: 7px;
    background: #e2e9e4;
    border-radius: 20px;
    overflow: hidden;
}

.fill {
    height: 100%;
    background: linear-gradient(90deg, #208d59, #72e6a0);
    transition: width 0.4s;
}

.result {
    text-align: center;
    margin-top: 25px;
    padding: 25px;
    border-radius: 20px;
    background: #e9f7ee;
}

.result .emoji {
    font-size: 50px;
}

.categories {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 15px;
}

.category {
    padding: 20px;
    border-radius: 18px;
}

.category b {
    display: block;
    margin-top: 8px;
}

.plastic { background: #e1efff; }
.paper { background: #fff0c9; }
.glass { background: #e1f5e8; }
.danger { background: #ffe1e1; }
.metal { background: #e6e7e8; }
.other { background: #eeeeee; }

.info {
    display: grid;
    grid-template-columns: repeat(3,1fr);
    gap: 15px;
}

.info-box {
    text-align: center;
    background: #f0f5f1;
    padding: 20px;
    border-radius: 18px;
}

.info-box strong {
    display: block;
    color: #208d59;
    font-size: 28px;
}

footer {
    text-align: center;
    color: #9bb5a7;
    margin-top: 40px;
}

@media(max-width:700px) {
    .hero h1 {
        font-size: 38px;
    }

    .categories,
    .info {
        grid-template-columns: 1fr 1fr;
    }
}
</style>
</head>

<body>

<div class="container">

<header>
    <div class="logo">♻️ Eco<span>Sort</span></div>
    <div class="badge">🤖 AI</div>
</header>

<section class="hero">
    <div>SMART ECO ASSISTANT</div>

    <h1>
        Сортируй.<br>
        <span>Меняй будущее.</span>
    </h1>

    <p>
        Покажи мусор камере — EcoSort попробует определить его категорию.
    </p>
</section>

<div class="card">

    <h2>📷 AI-проверка</h2>

    <p>
        Нажми кнопку и разреши доступ к камере.
    </p>

    <div class="camera">
        <div id="webcam"></div>
    </div>

    <div style="text-align:center;">
        <button class="start" onclick="startAI()">
            🚀 Запустить EcoSort AI
        </button>
    </div>

    <div id="status">
        Нажми кнопку, чтобы начать.
    </div>

    <div id="predictions"></div>

    <div id="result"></div>

</div>

<div class="card">

<h2>♻️ Категории</h2>

<div class="categories">

<div class="category plastic">
🧴
<b>Пластик</b>
Бутылки и упаковка
</div>

<div class="category paper">
📄
<b>Бумага</b>
Картон и бумага
</div>

<div class="category glass">
🍾
<b>Стекло</b>
Бутылки и банки
</div>

<div class="category danger">
☢️
<b>Опасные</b>
Опасные отходы
</div>

<div class="category metal">
🥫
<b>Металл</b>
Металлические отходы
</div>

<div class="category other">
❓
<b>Другое</b>
Другие отходы
</div>

</div>

</div>

<div class="card">

<h2>🌱 Моя статистика</h2>

<div class="info">

<div class="info-box">
<strong id="reports">0</strong>
Фотоотчётов
</div>

<div class="info-box">
<strong id="points">0</strong>
Eco-баллов
</div>

<div class="info-box">
<strong>3</strong>
Дня между проверками
</div>

</div>

</div>

<footer>
EcoSort MVP • Сделано для экологичного будущего 🌍
</footer>

</div>

<script>

const MODEL_URL =
"https://teachablemachine.withgoogle.com/models/G7dMpMWEi/";

let model;
let webcam;
let maxPredictions;
let running = false;

async function startAI() {

    if (running) return;

    running = true;

    const status =
        document.getElementById("status");

    status.innerHTML =
        "⏳ Загружаю твою AI-модель...";

    try {

        const modelURL =
            MODEL_URL + "model.json";

        const metadataURL =
            MODEL_URL + "metadata.json";

        model = await tmImage.load(
            modelURL,
            metadataURL
        );

        maxPredictions =
            model.getTotalClasses();

        webcam =
            new tmImage.Webcam(
                400,
                400,
                true
            );

        await webcam.setup();

        await webcam.play();

        document
            .getElementById("webcam")
            .appendChild(webcam.canvas);

        status.innerHTML =
            "🟢 AI работает! Покажи мусор камере.";

        createPredictionBoxes();

        window.requestAnimationFrame(loop);

    } catch(error) {

        console.error(error);

        status.innerHTML =
            "❌ AI не загрузился. Открой консоль браузера для подробной ошибки.";

        running = false;
    }
}

function createPredictionBoxes() {

    const container =
        document.getElementById("predictions");

    container.innerHTML = "";

    for(let i = 0; i < maxPredictions; i++) {

        const box =
            document.createElement("div");

        box.className = "prediction";

        box.innerHTML = `
            <div class="prediction-title">
                <span id="name-${i}"></span>
                <span id="percent-${i}">0%</span>
            </div>

            <div class="bar">
                <div
                    class="fill"
                    id="bar-${i}"
                    style="width:0%">
                </div>
            </div>
        `;

        container.appendChild(box);
    }
}

async function loop() {

    webcam.update();

    await predict();

    window.requestAnimationFrame(loop);
}

async function predict() {

    const predictions =
        await model.predict(webcam.canvas);

    let best = predictions[0];

    for(const prediction of predictions) {

        if(
            prediction.probability >
            best.probability
        ) {
            best = prediction;
        }
    }

    predictions.forEach((prediction,index) => {

        const percent =
            Math.round(
                prediction.probability * 100
            );

        document.getElementById(
            "name-" + index
        ).innerHTML =
            getEmoji(prediction.className)
            + " "
            + prediction.className;

        document.getElementById(
            "percent-" + index
        ).innerHTML =
            percent + "%";

        document.getElementById(
            "bar-" + index
        ).style.width =
            percent + "%";

    });

    showResult(best);
}

function showResult(best) {

    const percent =
        Math.round(
            best.probability * 100
        );

    const result =
        document.getElementById("result");

    if(percent >= 70) {

        result.innerHTML = `
            <div class="result">
                <div class="emoji">
                    ${getEmoji(best.className)}
                </div>

                <h2>
                    ${best.className}
                </h2>

                <p>
                    AI уверенно определил категорию
                </p>

                <h2>
                    ${percent}%
                </h2>

                <p>
                    🌱 Спасибо за правильную сортировку!
                </p>
            </div>
        `;

    } else {

        result.innerHTML = `
            <div class="result">
                <div class="emoji">🤔</div>

                <h2>
                    Не уверен
                </h2>

                <p>
                    Попробуй показать объект ближе
                    и при хорошем освещении.
                </p>
            </div>
        `;
    }
}

function getEmoji(name) {

    const text =
        name.toLowerCase();

    if(text.includes("пласт"))
        return "🧴";

    if(text.includes("бумаг"))
        return "📄";

    if(text.includes("стек"))
        return "🍾";

    if(text.includes("опас"))
        return "☢️";

    if(text.includes("металл"))
        return "🥫";

    return "❓";
}

</script>

</body>
</html>
