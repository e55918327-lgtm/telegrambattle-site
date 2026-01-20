[index.html](https://github.com/user-attachments/files/24739120/index.html)
<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8">
<title>Gift Battle Style</title>

<style>
body {
  margin: 0;
  font-family: Arial, sans-serif;
  background: #0b0f1a;
  color: white;
  text-align: center;
}

header {
  background: #0f172a;
  padding: 15px;
  display: flex;
  justify-content: center;
  gap: 20px;
}

button {
  background: linear-gradient(45deg, #7c3aed, #2563eb);
  border: none;
  color: white;
  padding: 12px 20px;
  border-radius: 8px;
  cursor: pointer;
  font-size: 16px;
}

.section {
  display: none;
  padding: 30px;
}

.active {
  display: block;
}

/* КЕЙС */

.roller-wrapper {
  position: relative;
  width: 320px;
  margin: 0 auto;
}

.arrow {
  position: absolute;
  top: -22px;
  left: 50%;
  transform: translateX(-50%);
  font-size: 24px;
  color: gold;
  text-shadow: 0 0 10px gold;
  z-index: 10;
}

.roller {
  overflow: hidden;
  width: 320px;
  border-radius: 10px;
  background: #111827;
  border: 2px solid #1f2937;
}

.roll-items {
  display: flex;
  transition: transform 3s cubic-bezier(.08,.6,0,1);
}

.roll-item {
  width: 100px;
  margin: 5px;
  background: #1f2937;
  border-radius: 10px;
  padding: 10px;
  flex-shrink: 0;
}

.roll-item img {
  width: 60px;
  height: 60px;
}

.price {
  color: gold;
  margin-top: 5px;
  font-weight: bold;
}

/* ИНВЕНТАРЬ */

.inventory-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(120px, 1fr));
  gap: 15px;
}

.inventory-item {
  background: #1f2937;
  padding: 10px;
  border-radius: 10px;
}
</style>
</head>

<body>

<header>
  <button onclick="showSection('cases')">🎁 Кейсы</button>
  <button onclick="showSection('inventory')">🎒 Инвентарь</button>
</header>

<div id="cases" class="section active">
  <h1>Открыть кейс</h1>

  <div class="roller-wrapper">
    <div class="arrow">▼</div>
    <div class="roller">
      <div id="roll-items" class="roll-items"></div>
    </div>
  </div>

  <br><br>
  <button onclick="openCase()">Открыть кейс</button>

  <h2 id="result"></h2>
</div>

<div id="inventory" class="section">
  <h1>Инвентарь</h1>
  <div id="inventory-grid" class="inventory-grid"></div>
</div>

<script>
function createImage(text, color) {
  const canvas = document.createElement("canvas");
  canvas.width = 100;
  canvas.height = 100;
  const ctx = canvas.getContext("2d");

  ctx.fillStyle = color;
  ctx.fillRect(0, 0, 100, 100);

  ctx.fillStyle = "white";
  ctx.font = "bold 20px Arial";
  ctx.textAlign = "center";
  ctx.textBaseline = "middle";
  ctx.fillText(text, 50, 50);

  return canvas.toDataURL();
}

const items = [
  { name: "PEPE", img: createImage("PEPE", "#22c55e"), price: "88.789" },
  { name: "ROLEKS", img: createImage("ROLEKS", "#facc15"), price: "45.304" },
  { name: "Черепашка", img: createImage("🐢", "#14b8a6"), price: "1.234" },
  { name: "Часы", img: createImage("⌚", "#6366f1"), price: "2.988" },
  { name: "GIFTS", img: createImage("🎁", "#ec4899"), price: "6.850" }
];

const rollContainer = document.getElementById("roll-items");
const inventoryGrid = document.getElementById("inventory-grid");
let inventory = [];

function showSection(id) {
  document.querySelectorAll(".section").forEach(s => s.classList.remove("active"));
  document.getElementById(id).classList.add("active");
}

function generateRoll() {
  rollContainer.innerHTML = "";

  for (let i = 0; i < 25; i++) {
    const item = items[Math.floor(Math.random() * items.length)];
    const div = document.createElement("div");
    div.className = "roll-item";
    div.innerHTML = `
      <img src="${item.img}">
      <div class="price">⭐ ${item.price}</div>
      <span>${item.name}</span>
    `;
    rollContainer.appendChild(div);
  }
}

function openCase() {
  generateRoll();
  rollContainer.style.transform = "translateX(0px)";

  const roller = document.querySelector(".roller");
  const containerWidth = roller.offsetWidth;
  const item = rollContainer.children[0];
  const itemWidth = item.offsetWidth + 10;

  const winIndex = Math.floor(rollContainer.children.length / 2);

  setTimeout(() => {
    const offset = (winIndex * itemWidth) - (containerWidth / 2) + (itemWidth / 2);
    rollContainer.style.transform = `translateX(-${offset}px)`;
  }, 100);

  setTimeout(() => {
    const centerX = roller.getBoundingClientRect().left + roller.offsetWidth / 2;

    let closest = null;
    let min = Infinity;

    [...rollContainer.children].forEach(el => {
      const rect = el.getBoundingClientRect();
      const elCenter = rect.left + rect.width / 2;
      const dist = Math.abs(centerX - elCenter);

      if (dist < min) {
        min = dist;
        closest = el;
      }
    });

    const name = closest.querySelector("span").textContent;
    const img = closest.querySelector("img").src;
    const price = closest.querySelector(".price").textContent;

    document.getElementById("result").textContent = "Ты выиграл: " + name;

    inventory.push({ name, img, price });
    updateInventory();
  }, 3200);
}

function updateInventory() {
  inventoryGrid.innerHTML = "";

  inventory.forEach(item => {
    const div = document.createElement("div");
    div.className = "inventory-item";
    div.innerHTML = `
      <img src="${item.img}" width="60">
      <div>${item.name}</div>
      <div class="price">${item.price}</div>
    `;
    inventoryGrid.appendChild(div);
  });
}
</script>

</body>
</html>
