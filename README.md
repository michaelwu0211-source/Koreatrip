<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Seoul Trip 2026</title>
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <style>
        :root {
            --primary: #ff4757;
            --bg: #f1f2f6;
            --card: #ffffff;
            --text: #2f3542;
        }
        body { font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif; background: var(--bg); color: var(--text); margin: 0; padding-bottom: 50px; }
        header { background: var(--primary); color: white; padding: 20px; text-align: center; font-weight: bold; font-size: 1.2rem; position: sticky; top: 0; z-index: 1000; box-shadow: 0 2px 10px rgba(0,0,0,0.1); }
        #map { height: 300px; width: 100%; position: sticky; top: 60px; z-index: 999; border-bottom: 2px solid #ddd; }
        .container { padding: 15px; }
        .day-section { background: var(--card); border-radius: 15px; padding: 15px; margin-bottom: 20px; box-shadow: 0 4px 6px rgba(0,0,0,0.05); }
        .day-title { font-size: 1.1rem; font-weight: 800; color: var(--primary); border-bottom: 2px solid #ffeaea; padding-bottom: 5px; margin-bottom: 10px; display: flex; justify-content: space-between; }
        .item-list { list-style: none; padding: 0; }
        .item { display: flex; align-items: center; justify-content: space-between; padding: 10px 0; border-bottom: 1px solid #eee; }
        .item:last-child { border-bottom: none; }
        .btn-map { background: #e1f5fe; color: #039be5; border: none; padding: 5px 10px; border-radius: 5px; font-size: 0.8rem; cursor: pointer; }
        .btn-del { color: #ff4757; font-weight: bold; margin-left: 10px; cursor: pointer; padding: 5px; }
        .add-box { display: flex; margin-top: 10px; gap: 5px; }
        input { flex: 1; padding: 8px; border: 1px solid #ddd; border-radius: 5px; }
        .btn-add { background: var(--primary); color: white; border: none; padding: 8px 15px; border-radius: 5px; }
    </style>
</head>
<body>

<header>🇰🇷 我的韓國首爾之旅 (4/30 - 5/4)</header>
<div id="map"></div>

<div class="container" id="app">
    </div>

<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
<script>
    // 初始化地圖 (首爾中心)
    const map = L.map('map').setView([37.5665, 126.9780], 12);
    L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png').addTo(map);

    // 初始行程資料
    let tripData = [
        { day: "Day 1 (4/30)", items: ["Check in", "汝矣島現代百貨", "漢江公園吃泡麵", "逛 Olive Young", "民宿吃韓式炸雞"], coords: [37.527, 126.927] },
        { day: "Day 2 (5/1)", items: ["乙支餃子(午餐)", "景福宮穿韓服", "北村韓屋村", "Onion Cafe", "嚴龍百豬肉湯飯"], coords: [37.579, 126.977] },
        { day: "Day 3 (5/2)", items: ["Free Day 自由活動", "弘大 烤肉給的男人(19:00)"], coords: [37.556, 126.922] },
        { day: "Day 4 (5/3)", items: ["孝淡鐘路雞湯(弘大)", "首爾塔夜景", "烏達里家醬蟹", "明洞散策"], coords: [37.559, 126.985] },
        { day: "Day 5 (5/4)", items: ["望遠市場巡禮", "準備返家"], coords: [37.556, 126.906] }
    ];

    function render() {
        const container = document.getElementById('app');
        container.innerHTML = '';
        
        tripData.forEach((data, dayIdx) => {
            const section = document.createElement('div');
            section.className = 'day-section';
            
            let itemsHtml = data.items.map((item, itemIdx) => `
                <li class="item">
                    <span>${item}</span>
                    <div>
                        <button class="btn-map" onclick="focusMap(${data.coords[0]}, ${data.coords[1]}, '${item}')">📍</button>
                        <span class="btn-del" onclick="deleteItem(${dayIdx}, ${itemIdx})">✕</span>
                    </div>
                </li>
            `).join('');

            section.innerHTML = `
                <div class="day-title">${data.day}</div>
                <ul class="item-list">${itemsHtml}</ul>
                <div class="add-box">
                    <input type="text" placeholder="新增景點..." id="input-${dayIdx}">
                    <button class="btn-add" onclick="addItem(${dayIdx})">新增</button>
                </div>
            `;
            container.appendChild(section);
            
            // 在地圖上標記
            L.marker(data.coords).addTo(map).bindPopup(data.day);
        });
    }

    function addItem(dayIdx) {
        const input = document.getElementById(`input-${dayIdx}`);
        if (input.value) {
            tripData[dayIdx].items.push(input.value);
            input.value = '';
            render();
        }
    }

    function deleteItem(dayIdx, itemIdx) {
        tripData[dayIdx].items.splice(itemIdx, 1);
        render();
    }

    function focusMap(lat, lng, name) {
        map.setView([lat, lng], 15);
        L.popup().setLatLng([lat, lng]).setContent(name).openOn(map);
        window.scrollTo({ top: 0, behavior: 'smooth' });
    }

    render();
</script>

</body>
</html>
