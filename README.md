<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Barista: Rintisan Cafe</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>

    <div class="cafe-game">
        <h1>Biji Kopi Impian</h1>

        <div class="stats-panel">
            <p>💰 Uang: <span id="money">$100</span></p>
            <p>⭐ Reputasi: <span id="reputation">10</span></p>
            <p>☕ Inventori Kopi: <span id="coffeeBeans">50</span> gram</p>
            <p>🗓️ Hari: <span id="day">1</span></p>
        </div>

        <div class="story-area">
            <p id="storyText">Selamat datang di awal perjalananmu! Kamu baru saja menyewa tempat kecil ini. Apa yang akan kamu lakukan hari ini?</p>
        </div>

        <div class="action-panel" id="actionPanel">
            <button id="serveButton" data-action="serve">Melayani Pelanggan (+Uang, +Reputasi)</button>
            <button id="buyButton" data-action="buy">Beli Bahan Baku (-Uang, +Kopi)</button>
            <button id="upgradeButton" data-action="upgrade">Upgrade Peralatan (-Uang, +Efisiensi)</button>
            <button id="nextDayButton" data-action="nextDay">Lanjut ke Hari Berikutnya</button>
        </div>

        <div id="resultMessage"></div>
    </div>

    <script src="script.js"></script>
</body>
</html>

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    display: flex;
    justify-content: center;
    align-items: center;
    min-height: 100vh;
    margin: 0;
    background-color: #e8dcc6; /* Warna Krem Hangat */
    color: #333;
}

.cafe-game {
    background: #ffffff;
    padding: 40px;
    border-radius: 15px;
    box-shadow: 0 10px 30px rgba(0, 0, 0, 0.2);
    width: 90%;
    max-width: 600px;
    text-align: center;
}

h1 {
    color: #6f4e37; /* Coklat Kopi */
    margin-bottom: 20px;
}

.stats-panel {
    background-color: #f7f3e8;
    padding: 15px;
    border-radius: 8px;
    margin-bottom: 20px;
    display: flex;
    justify-content: space-around;
    font-size: 1.1em;
    font-weight: bold;
    border: 1px solid #d4c7b8;
}

.story-area {
    min-height: 80px;
    margin: 20px 0;
    padding: 15px;
    border: 2px solid #6f4e37;
    border-radius: 5px;
    background-color: #fff9f0;
    text-align: left;
    line-height: 1.5;
}

.action-panel button {
    background-color: #6f4e37;
    color: white;
    padding: 10px 15px;
    margin: 5px;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    font-size: 1em;
    transition: background-color 0.3s;
}

.action-panel button:hover:not(:disabled) {
    background-color: #4a3328;
}

.action-panel button:disabled {
    background-color: #aaa;
    cursor: not-allowed;
}

#resultMessage {
    margin-top: 15px;
    font-weight: bold;
    min-height: 20px;
}

// --- Variabel Status Game ---
let gameState = {
    money: 100,
    reputation: 10,
    coffeeBeans: 50, // dalam gram. 1 porsi = 10 gram.
    day: 1,
    equipmentLevel: 1 // Level 1: manual, Level 2: mesin semi-otomatis, dst.
};

// --- Elemen DOM ---
const $ = (id) => document.getElementById(id);

const moneyEl = $('money');
const reputationEl = $('reputation');
const coffeeBeansEl = $('coffeeBeans');
const dayEl = $('day');
const storyTextEl = $('storyText');
const actionPanelEl = $('actionPanel');
const resultMessageEl = $('resultMessage');

// --- Konfigurasi Game ---
const costs = {
    coffeeBeanBag: 40, // Harga 100 gram kopi
    coffeeBeanAmount: 100,
    upgradeCost: 200,
    serveYield: 15
};

// --- Fungsi Utama Update UI ---
function updateUI() {
    moneyEl.textContent = `$${gameState.money}`;
    reputationEl.textContent = gameState.reputation;
    coffeeBeansEl.textContent = `${gameState.coffeeBeans}`;
    dayEl.textContent = gameState.day;
}

// --- Logika Aksi Pemain ---

/**
 * Aksi: Melayani Pelanggan
 */
function serveCustomers() {
    const costPerServe = 10;

    if (gameState.coffeeBeans < costPerServe) {
        resultMessageEl.textContent = "❌ Gagal! Inventori kopi tidak cukup untuk membuat kopi!";
        return;
    }

    // Simulasi hasil pelayanan
    const isSuccess = Math.random() < (0.5 + gameState.reputation / 100); // Reputasi meningkatkan peluang sukses
    gameState.coffeeBeans -= costPerServe;

    if (isSuccess) {
        const earnings = costs.serveYield + Math.floor(Math.random() * 5); // Keuntungan
        gameState.money += earnings;
        gameState.reputation = Math.min(100, gameState.reputation + 2); // Reputasi max 100
        storyTextEl.textContent = `Seorang pelanggan sangat menikmati kopimu! Kamu mendapat $${earnings} dan reputasi bertambah.`;
    } else {
        gameState.money += costs.serveYield - 5; // Masih dapat uang tapi lebih sedikit
        gameState.reputation = Math.max(1, gameState.reputation - 1); // Reputasi min 1
        storyTextEl.textContent = "Ada keluhan kecil tentang rasa kopi, tapi kamu belajar dari kesalahan. Reputasi sedikit turun.";
    }

    resultMessageEl.textContent = `Melayani pelanggan. Sisa Kopi: ${gameState.coffeeBeans} gram.`;
    updateUI();
}

/**
 * Aksi: Beli Bahan Baku
 */
function buyBeans() {
    if (gameState.money < costs.coffeeBeanBag) {
        resultMessageEl.textContent = "❌ Uangmu tidak cukup untuk membeli bahan baku.";
        return;
    }

    gameState.money -= costs.coffeeBeanBag;
    gameState.coffeeBeans += costs.coffeeBeanAmount;
    storyTextEl.textContent = `Kamu membeli 100 gram biji kopi segar seharga $${costs.coffeeBeanBag}. Stok aman!`;
    resultMessageEl.textContent = `Inventori Kopi: ${gameState.coffeeBeans} gram.`;
    updateUI();
}

/**
 * Aksi: Upgrade Peralatan
 */
function upgradeEquipment() {
    if (gameState.equipmentLevel >= 3) {
        resultMessageEl.textContent = "✅ Peralatanmu sudah mencapai level maksimum saat ini.";
        return;
    }

    if (gameState.money < costs.upgradeCost * gameState.equipmentLevel) {
        resultMessageEl.textContent = `❌ Perlu $${costs.upgradeCost * gameState.equipmentLevel} untuk upgrade. Kumpulkan lagi!`;
        return;
    }

    gameState.money -= costs.upgradeCost * gameState.equipmentLevel;
    gameState.equipmentLevel++;
    costs.serveYield += 5; // Peningkatan keuntungan/efisiensi
    storyTextEl.textContent = `🎉 Peralatan berhasil di-upgrade ke Level ${gameState.equipmentLevel}! Operasi cafe kini lebih efisien.`;
    resultMessageEl.textContent = `Keuntungan per serve naik!`;
    updateUI();
}


/**
 * Aksi: Lanjut ke Hari Berikutnya
 */
function nextDay() {
    gameState.day++;
    
    // Rintangan (Event Acak)
    let eventMessage = "";
    if (gameState.day % 5 === 0) {
        // Setiap 5 hari ada tantangan
        const eventType = Math.random();
        if (eventType < 0.4) {
            // Biaya tak terduga
            const bill = Math.floor(Math.random() * 20) + 10;
            gameState.money = Math.max(0, gameState.money - bill);
            eventMessage = `Oh tidak! Tagihan listrik tak terduga datang senilai $${bill}. Kamu harus membayarnya.`;
        } else if (eventType < 0.8) {
            // Pelanggan setia
            const bonus = Math.floor(Math.random() * 10) + 5;
            gameState.money += bonus;
            gameState.reputation = Math.min(100, gameState.reputation + 3);
            eventMessage = `Seorang pelanggan setia memberikan tips besar! Kamu mendapat bonus $${bonus} dan reputasi naik!`;
        } else {
            // Hari sepi
            eventMessage = "Hari ini sangat sepi. Hanya beberapa pelanggan. Kamu harus lebih gencar promosi!";
        }
    } else {
        eventMessage = "Kamu menutup cafe. Besok adalah hari baru penuh peluang.";
    }

    storyTextEl.textContent = eventMessage;
    resultMessageEl.textContent = "Selamat datang di Hari " + gameState.day + "!";
    updateUI();
    checkGameOver();
}

// --- Logika Akhir Game ---
function checkGameOver() {
    if (gameState.money <= 0 && gameState.coffeeBeans < 10) {
        storyTextEl.textContent = "GAME OVER: Cafe-mu bangkrut karena kehabisan modal dan stok. Usaha kerasmu belum cukup.";
        disableActions();
    } else if (gameState.day >= 30 && gameState.reputation >= 80) {
        storyTextEl.textContent = "KEMENANGAN: Setelah 30 hari, cafe-mu sukses besar! Reputasi tinggi, dan kamu menjadi Barista terkenal!";
        disableActions();
    }
}

function disableActions() {
    Array.from(actionPanelEl.children).forEach(button => {
        button.disabled = true;
    });
}

// --- Inisialisasi Event Listener ---
function init() {
    actionPanelEl.addEventListener('click', (event) => {
        const action = event.target.dataset.action;
        if (!action) return;

        resultMessageEl.textContent = ''; // Kosongkan pesan hasil
        
        switch (action) {
            case 'serve':
                serveCustomers();
                break;
            case 'buy':
                buyBeans();
                break;
            case 'upgrade':
                upgradeEquipment();
                break;
            case 'nextDay':
                nextDay();
                break;
        }
    });

    updateUI();
}

// Mulai Game
document.addEventListener('DOMContentLoaded', init);
