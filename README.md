
<!DOCTYPE html>
<html lang="tr">
<head>
<meta charset="UTF-8">
<title>Etlilerin Mutfağı | Online Sipariş</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<style>
body{margin:0;font-family:Arial;background:#fff;color:#333}
header{background:#f3e8d8;text-align:center;padding:22px}
header img{max-width:150px}
h2{color:#8a6f4d}
.container{max-width:1200px;margin:auto;padding:20px}
.products{display:grid;grid-template-columns:repeat(auto-fit,minmax(240px,1fr));gap:18px}
.card{border:1px solid #e0d4c2;border-radius:12px;padding:14px;text-align:center}
.price{font-weight:bold;color:#8a6f4d;margin:6px 0}
.unit{font-size:13px;color:#777}
.qty{display:flex;justify-content:center;align-items:center;margin:8px 0}
.qty button{padding:6px 12px;font-size:18px}
.qty span{margin:0 10px;font-size:18px}
.add{background:#8a6f4d;color:#fff;border:none;padding:10px 14px;border-radius:6px;cursor:pointer}
#cart{margin-top:28px;border-top:2px solid #e0d4c2;padding-top:18px}
.complete{background:#25D366;color:#fff;padding:14px 24px;border:none;border-radius:30px;font-size:18px;cursor:pointer}
.footer{background:#f3e8d8;text-align:center;padding:16px;margin-top:30px}
</style>
</head>
<body>

<header>
  <img src="logo.jpg">
  <p><strong>Hemen Sipariş Ver</strong></p>
</header>

<div class="container">
<h2>Yemekler</h2>
<div class="products" id="products"></div>

<div id="cart">
<h2>Sepet</h2>
<ul id="cartList"></ul>
<h3 id="total">Toplam: 0 ₺</h3>
<button class="complete" onclick="sendWhatsApp()">Siparişi Tamamla</button>
</div>
</div>

<div class="footer">© 2026 Etlilerin Mutfağı</div>

<script>
const products = [
{name:"İçli Köfte",price:120,unit:"Adet"},
{name:"Kalem Böreği",price:12,unit:"Adet"},
{name:"Gül Böreği",price:25,unit:"Adet"},
{name:"Poğaça",price:15,unit:"Adet"},
{name:"Saçaklı Poğaça",price:20,unit:"Adet"},
{name:"Perde Pilavı",price:100,unit:"Adet"},
{name:"Cheesecake (Porsiyon)",price:100,unit:"Adet"},
{name:"Magnolya",price:85,unit:"Adet"},
{name:"Fırın Sütlaç",price:80,unit:"Adet"},
{name:"Brownie",price:250,unit:"Adet"},

{name:"Mantı",price:1200,unit:"KG"},
{name:"Fellah Köftesi",price:480,unit:"KG"},
{name:"Mercimek Köftesi",price:480,unit:"KG"},
{name:"Kısır",price:420,unit:"KG"},

{name:"Etli Sarma",price:750,unit:"KG"},
{name:"Zeytinyağlı Sarma",price:650,unit:"KG"},
{name:"Üzümlü Fıstıklı Sarma",price:1300,unit:"KG"},

{name:"El Açması Kıymalı Börek",price:1250,unit:"KG"},
{name:"El Açması Patatesli Börek",price:650,unit:"KG"},
{name:"El Açması Ispanaklı Börek",price:650,unit:"KG"},

{name:"Cheesecake (Büyük)",price:900,unit:"KG"},
{name:"Kıbrıs Tatlısı",price:800,unit:"KG"},
{name:"Gelin Pastası",price:700,unit:"KG"},
{name:"Islak Kek",price:600,unit:"KG"},
{name:"Kadayıflı Muhallebi",price:750,unit:"KG"},

{name:"Fıstıklı Baklava",price:3000,unit:"Tepsi"},
{name:"Cevizli Baklava",price:2250,unit:"Tepsi"},
{name:"Soğuk Baklava",price:3250,unit:"Tepsi"},

{name:"Peynirli Börek",price:1100,unit:"Borcam"},
{name:"Patatesli Börek",price:850,unit:"Borcam"},
{name:"Kıymalı Börek",price:1150,unit:"Borcam"},
{name:"Ispanaklı Börek",price:850,unit:"Borcam"},

{name:"Şekerpare",price:550,unit:"KG"},
{name:"Feselli",price:90,unit:"Adet"}
];

let cart = {};
let quantities = Array(products.length).fill(1);
const container = document.getElementById("products");

products.forEach((p,i)=>{
const card=document.createElement("div");
card.className="card";
card.innerHTML=`
<h3>${p.name}</h3>
<div class="unit">${p.unit}</div>
<div class="price">${p.price} ₺</div>
<div class="qty">
<button onclick="changeQty(${i},-1)">-</button>
<span id="q${i}">1</span>
<button onclick="changeQty(${i},1)">+</button>
</div>
<button class="add" onclick="addCart(${i})">Sepete Ekle</button>`;
container.appendChild(card);
});

function changeQty(i,v){
quantities[i]=Math.max(1,quantities[i]+v);
document.getElementById("q"+i).innerText=quantities[i];
}

function addCart(i){
const p=products[i];
cart[p.name]=(cart[p.name]||0)+quantities[i];
renderCart();
}

function renderCart(){
const list=document.getElementById("cartList");
list.innerHTML="";
let total=0;
for(let n in cart){
const p=products.find(x=>x.name===n);
const t=cart[n]*p.price;
total+=t;
list.innerHTML+=`<li>${n} (${cart[n]} ${p.unit}) = ${t} ₺</li>`;
}
document.getElementById("total").innerText="Toplam: "+total+" ₺";
}

function sendWhatsApp(){
let msg="Merhaba, sipariş vermek istiyorum.%0A";
let total=0;
for(let n in cart){
const p=products.find(x=>x.name===n);
const t=cart[n]*p.price;
total+=t;
msg+=`${n} (${cart[n]} ${p.unit}) = ${t} ₺%0A`;
}
msg+=`Toplam: ${total} ₺`;
window.open("https://wa.me/905305956681?text="+msg,"_blank");
}
</script>

</body>
</html>
