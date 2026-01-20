<!DOCTYPE html>
<html lang="tr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Eltilerin Mutfağı | Sipariş Hattı</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;600;700&display=swap');
        
        body {
            font-family: 'Poppins', sans-serif;
            background-color: #fcfcfc;
            -webkit-tap-highlight-color: transparent;
        }

        .product-card {
            background: white;
            border-radius: 16px;
            box-shadow: 0 4px 12px rgba(0,0,0,0.03);
            border: 1px solid #f0f0f0;
        }

        .cart-bar {
            position: fixed;
            bottom: 25px;
            left: 5%;
            width: 90%;
            background: #111827;
            color: white;
            padding: 12px 20px;
            border-radius: 20px;
            display: none;
            justify-content: space-between;
            align-items: center;
            z-index: 999;
            box-shadow: 0 10px 30px rgba(0,0,0,0.3);
            animation: slideUp 0.3s ease-out;
        }

        @keyframes slideUp {
            from { transform: translateY(100px); opacity: 0; }
            to { transform: translateY(0); opacity: 1; }
        }

        .category-header {
            font-weight: 800;
            font-size: 1.1rem;
            color: #1f2937;
            margin: 25px 0 15px 5px;
            display: flex;
            align-items: center;
            gap: 10px;
        }

        .category-header::before {
            content: '';
            width: 4px;
            height: 20px;
            background: #ef4444;
            border-radius: 2px;
        }

        #orderModal {
            display: none;
            position: fixed;
            inset: 0;
            background: rgba(0,0,0,0.7);
            z-index: 1000;
            align-items: flex-end; /* Mobil için aşağıdan açılan menü tipi */
        }

        .modal-content {
            background: white;
            width: 100%;
            border-radius: 30px 30px 0 0;
            padding: 24px;
            max-height: 85vh;
            overflow-y: auto;
            animation: slideModal 0.3s ease-out;
        }

        @keyframes slideModal {
            from { transform: translateY(100%); }
            to { transform: translateY(0); }
        }
    </style>
</head>
<body class="pb-32">

    <!-- Header -->
    <header class="bg-white/80 backdrop-blur-md border-b sticky top-0 z-50 px-4 py-4">
        <div class="container mx-auto flex justify-between items-center">
            <h1 class="text-2xl font-bold text-red-600 tracking-tight">Eltilerin Mutfağı</h1>
            <a href="tel:05305956681" class="w-10 h-10 bg-red-50 text-red-600 rounded-full flex items-center justify-center">
                <i class="fas fa-phone-alt"></i>
            </a>
        </div>
    </header>

    <!-- Ana Menü -->
    <main class="container mx-auto px-4">
        <div id="menu-container"></div>
    </main>

    <!-- Sepet Çubuğu -->
    <div id="cartBar" class="cart-bar">
        <div class="flex flex-col">
            <span id="cartInfo" class="text-xs text-gray-400">0 ürün seçildi</span>
            <span id="totalPriceDisplay" class="font-bold text-lg text-white">0 ₺</span>
        </div>
        <button onclick="showSummary()" class="bg-red-600 px-6 py-3 rounded-xl font-bold text-sm">
            Siparişi Tamamla <i class="fas fa-arrow-right ml-1"></i>
        </button>
    </div>

    <!-- Sepet Modalı -->
    <div id="orderModal" onclick="if(event.target == this) closeModal()">
        <div class="modal-content">
            <div class="flex justify-between items-center mb-6">
                <h3 class="text-xl font-bold">Sipariş Özeti</h3>
                <button onclick="closeModal()" class="w-8 h-8 flex items-center justify-center bg-gray-100 rounded-full">&times;</button>
            </div>
            
            <div id="summaryList" class="space-y-4 mb-8"></div>

            <div class="space-y-3">
                <div class="flex justify-between text-lg">
                    <span class="text-gray-500">Toplam</span>
                    <span id="finalPrice" class="font-bold text-red-600">0 ₺</span>
                </div>
                <button onclick="sendToWhatsApp()" class="w-full bg-[#25D366] text-white py-4 rounded-2xl font-bold text-lg flex items-center justify-center gap-2 active:scale-95 transition-transform">
                    <i class="fab fa-whatsapp text-2xl"></i>
                    WhatsApp'tan Siparişi Ver
                </button>
            </div>
        </div>
    </div>

    <script>
        const WHATSAPP_NUMBER = "905305956681";
        
        const menu = [
            { cat: "Tane ile Satılanlar", items: [
                { n: "İçli Köfte", p: 120, u: "Tane" }, { n: "Kalem Böreği", p: 12, u: "Tane" },
                { n: "Gül Böreği", p: 25, u: "Tane" }, { n: "Poğaça", p: 15, u: "Tane" },
                { n: "Saçaklı Poğaça", p: 20, u: "Tane" }, { n: "Perde Pilavı", p: 100, u: "Tane" },
                { n: "Porsiyon Cheesecake", p: 100, u: "Tane" }, { n: "Magnolya", p: 85, u: "Tane" },
                { n: "Fırın Sütlaç", p: 80, u: "Tane" }, { n: "Brownie", p: 250, u: "Tane" },
                { n: "Feselli", p: 90, u: "Tane" }
            ]},
            { cat: "Kilo ile Satılanlar", items: [
                { n: "Mantı", p: 1200, u: "KG" }, { n: "Fellah Köftesi", p: 480, u: "KG" },
                { n: "Mercimek Köftesi", p: 480, u: "KG" }, { n: "Kısır", p: 420, u: "KG" },
                { n: "Etli Sarma", p: 750, u: "KG" }, { n: "Zeytinyağlı Sarma", p: 650, u: "KG" },
                { n: "Üzümlü Fıstıklı Sarma", p: 1300, u: "KG" }, { n: "Kıymalı Börek (Kilo)", p: 1250, u: "KG" },
                { n: "Patatesli Börek (Kilo)", p: 650, u: "KG" }, { n: "Ispanaklı Börek (Kilo)", p: 650, u: "KG" },
                { n: "Cheesecake Büyük", p: 900, u: "KG" }, { n: "Kıbrıs Tatlısı", p: 800, u: "KG" },
                { n: "Gelin Pastası", p: 700, u: "KG" }, { n: "Islak Kek", p: 600, u: "KG" },
                { n: "Kadayıflı Muhallebi", p: 750, u: "KG" }, { n: "Şekerpare", p: 550, u: "KG" }
            ]},
            { cat: "Tepsi & Borcam", items: [
                { n: "Fıstıklı Baklava (Tepsi)", p: 3000, u: "Tepsi" }, { n: "Cevizli Baklava (Tepsi)", p: 2250, u: "Tepsi" },
                { n: "Soğuk Baklava (Tepsi)", p: 3250, u: "Tepsi" }, { n: "Peynirli Börek (Borcam)", p: 1100, u: "Borcam" },
                { n: "Patatesli Börek (Borcam)", p: 850, u: "Borcam" }, { n: "Kıymalı Börek (Borcam)", p: 1150, u: "Borcam" },
                { n: "Ispanaklı Börek (Borcam)", p: 850, u: "Borcam" }
            ]}
        ];

        let cart = {};

        function initMenu() {
            const container = document.getElementById('menu-container');
            menu.forEach(group => {
                const head = document.createElement('h2');
                head.className = "category-header";
                head.innerText = group.cat;
                container.appendChild(head);

                const grid = document.createElement('div');
                grid.className = "grid grid-cols-1 gap-3";
                
                group.items.forEach(item => {
                    const el = document.createElement('div');
                    el.className = "product-card p-4 flex justify-between items-center";
                    el.innerHTML = `
                        <div class="flex-1">
                            <div class="font-bold text-gray-800 text-sm">${item.n}</div>
                            <div class="text-red-600 font-bold text-sm">${item.p} ₺ <span class="text-[10px] text-gray-400 font-normal">/ ${item.u}</span></div>
                        </div>
                        <div class="flex items-center gap-3">
                            <div id="btn-${item.n.replace(/\s/g,'')}" class="flex items-center bg-gray-100 rounded-full p-1 ${cart[item.n] ? '' : 'hidden'}">
                                <button onclick="addToCart('${item.n}', ${item.p}, -1)" class="w-8 h-8 flex items-center justify-center font-bold text-red-600">-</button>
                                <span class="px-2 font-bold text-sm">${cart[item.n]?.qty || 0}</span>
                                <button onclick="addToCart('${item.n}', ${item.p}, 1)" class="w-8 h-8 flex items-center justify-center font-bold text-red-600">+</button>
                            </div>
                            <button id="add-${item.n.replace(/\s/g,'')}" onclick="addToCart('${item.n}', ${item.p}, 1)" class="bg-red-600 text-white w-10 h-10 rounded-full ${cart[item.n] ? 'hidden' : ''}">
                                <i class="fas fa-plus"></i>
                            </button>
                        </div>
                    `;
                    grid.appendChild(el);
                });
                container.appendChild(grid);
            });
        }

        function addToCart(name, price, change) {
            if (!cart[name]) cart[name] = { qty: 0, price: price };
            cart[name].qty += change;
            if (cart[name].qty <= 0) delete cart[name];
            updateUI();
        }

        function updateUI() {
            let total = 0, count = 0;
            
            // Tüm butonları sıfırla/güncelle
            document.querySelectorAll('[id^="btn-"]').forEach(el => el.classList.add('hidden'));
            document.querySelectorAll('[id^="add-"]').forEach(el => el.classList.remove('hidden'));

            Object.keys(cart).forEach(n => {
                total += cart[n].qty * cart[n].price;
                count += cart[n].qty;
                const safeName = n.replace(/\s/g,'');
                document.getElementById('btn-'+safeName).classList.remove('hidden');
                document.getElementById('btn-'+safeName).querySelector('span').innerText = cart[n].qty;
                document.getElementById('add-'+safeName).classList.add('hidden');
            });

            const bar = document.getElementById('cartBar');
            if (count > 0) {
                bar.style.display = 'flex';
                document.getElementById('cartInfo').innerText = count + " ürün seçildi";
                document.getElementById('totalPriceDisplay').innerText = total + " ₺";
            } else {
                bar.style.display = 'none';
            }
        }

        function showSummary() {
            const list = document.getElementById('summaryList');
            list.innerHTML = '';
            let total = 0;
            Object.keys(cart).forEach(n => {
                const sub = cart[n].qty * cart[n].price;
                total += sub;
                list.innerHTML += `<div class="flex justify-between border-b pb-2"><div><p class="font-bold text-sm">${n}</p><p class="text-xs text-gray-500">${cart[n].qty} Adet x ${cart[n].price} ₺</p></div><span class="font-bold">${sub} ₺</span></div>`;
            });
            document.getElementById('finalPrice').innerText = total + " ₺";
            document.getElementById('orderModal').style.display = 'flex';
        }

        function closeModal() { document.getElementById('orderModal').style.display = 'none'; }

        function sendToWhatsApp() {
            let m = "*ELTİLERİN MUTFAĞI SİPARİŞİ*\n--------------------------\n";
            let t = 0;
            Object.keys(cart).forEach(n => {
                const s = cart[n].qty * cart[n].price;
                t += s;
                m += `✅ ${cart[n].qty} Adet/KG - ${n}\n`;
            });
            m += `--------------------------\n*TOPLAM: ${t} ₺*`;
            window.location.href = `https://wa.me/${WHATSAPP_NUMBER}?text=${encodeURIComponent(m)}`;
        }

        window.onload = initMenu;
    </script>
</body>
</html>


