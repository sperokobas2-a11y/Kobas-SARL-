# Kobas-SARL-
A line market 
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Boutique Kobas Pro</title>
    <style>
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            margin: 0;
            padding: 0;
            background-color: #f5f5f5;
        }
        header {
            background-color: #4CAF50;
            color: white;
            text-align: center;
            padding: 20px;
            position: sticky;
            top: 0;
            z-index: 10;
        }
        header h1 {
            margin: 0;
            font-size: 24px;
        }
        nav {
            display: flex;
            justify-content: center;
            flex-wrap: wrap;
            margin-top: 10px;
        }
        nav button {
            background-color: white;
            border: 1px solid #4CAF50;
            color: #4CAF50;
            padding: 8px 15px;
            margin: 5px;
            border-radius: 5px;
            cursor: pointer;
        }
        nav button.active {
            background-color: #4CAF50;
            color: white;
        }
        .products {
            display: flex;
            flex-wrap: wrap;
            justify-content: center;
            padding: 20px;
        }
        .product {
            background-color: white;
            border-radius: 10px;
            box-shadow: 0 2px 6px rgba(0,0,0,0.1);
            margin: 10px;
            width: 200px;
            text-align: center;
            padding: 15px;
            transition: transform 0.2s;
        }
        .product:hover {
            transform: translateY(-5px);
        }
        .product img {
            width: 100%;
            height: 150px;
            object-fit: cover;
            border-radius: 8px;
        }
        .product h3 {
            margin: 10px 0 5px 0;
        }
        .product p {
            margin: 5px 0;
            font-weight: bold;
            color: #333;
        }
        .product button {
            background-color: #4CAF50;
            color: white;
            border: none;
            padding: 8px 12px;
            margin-top: 10px;
            cursor: pointer;
            border-radius: 5px;
        }
        .product button:hover {
            background-color: #45a049;
        }
        #cart {
            position: fixed;
            top: 20px;
            right: 20px;
            background-color: #ff9800;
            color: white;
            padding: 10px 15px;
            border-radius: 50%;
            font-weight: bold;
            cursor: pointer;
            z-index: 20;
        }
        #cartModal {
            display: none;
            position: fixed;
            top: 0; left: 0; right:0; bottom:0;
            background: rgba(0,0,0,0.5);
            justify-content: center;
            align-items: center;
        }
        #cartContent {
            background: white;
            padding: 20px;
            border-radius: 10px;
            width: 90%;
            max-width: 400px;
            max-height: 80%;
            overflow-y: auto;
        }
        #cartContent h2 {
            text-align: center;
        }
        .cart-item {
            display: flex;
            justify-content: space-between;
            margin: 10px 0;
        }
        .cart-item button {
            background-color: #f44336;
            padding: 5px 10px;
        }
        .checkout-btn {
            background-color: #2196F3;
            width: 100%;
            padding: 10px;
            margin-top: 10px;
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
        }
        .checkout-btn:hover {
            background-color: #1976D2;
        }
    </style>
</head>
<body>
    <header>
        <h1>Boutique Kobas Pro</h1>
        <nav id="categoryNav">
            <button class="active" onclick="filterCategory('all', this)">Tous</button>
            <button onclick="filterCategory('vetements', this)">Vêtements</button>
            <button onclick="filterCategory('accessoires', this)">Accessoires</button>
            <button onclick="filterCategory('electronique', this)">Électronique</button>
        </nav>
    </header>

    <div id="cart">0 🛒</div>
    <div class="products" id="products"></div>

    <div id="cartModal">
        <div id="cartContent">
            <h2>Panier</h2>
            <div id="cartItems"></div>
            <p>Total: <span id="totalPrice">0</span> €</p>
            <button class="checkout-btn" onclick="checkout()">Payer</button>
        </div>
    </div>

    <script>
        const products = [
            {id: 1, name: "T-shirt Rouge", price: 12, img: "https://via.placeholder.com/200x150.png?text=T-shirt+Rouge", category: "vetements"},
            {id: 2, name: "Casquette Noire", price: 8, img: "https://via.placeholder.com/200x150.png?text=Casquette+Noire", category: "accessoires"},
            {id: 3, name: "Montre Digitale", price: 25, img: "https://via.placeholder.com/200x150.png?text=Montre+Digitale", category: "accessoires"},
            {id: 4, name: "Jeans Bleu", price: 20, img: "https://via.placeholder.com/200x150.png?text=Jeans+Bleu", category: "vetements"},
            {id: 5, name: "Écouteurs", price: 15, img: "https://via.placeholder.com/200x150.png?text=Écouteurs", category: "electronique"},
            {id: 6, name: "Smartphone", price: 120, img: "https://via.placeholder.com/200x150.png?text=Smartphone", category: "electronique"}
        ];

        let cart = [];

        function renderProducts(category = 'all') {
            const container = document.getElementById('products');
            container.innerHTML = '';
            let filtered = category === 'all' ? products : products.filter(p => p.category === category);
            filtered.forEach(product => {
                container.innerHTML += `
                    <div class="product">
                        <img src="${product.img}" alt="${product.name}">
                        <h3>${product.name}</h3>
                        <p>${product.price} €</p>
                        <button onclick="addToCart(${product.id})">Ajouter au panier</button>
                    </div>
                `;
            });
        }

        function filterCategory(cat, btn) {
            document.querySelectorAll('#categoryNav button').forEach(b => b.classList.remove('active'));
            btn.classList.add('active');
            renderProducts(cat);
        }

        function addToCart(id) {
            const product = products.find(p => p.id === id);
            cart.push(product);
            updateCart();
        }

        function removeFromCart(index) {
            cart.splice(index, 1);
            updateCart();
        }

        function updateCart() {
            document.getElementById('cart').innerText = `${cart.length} 🛒`;
            const cartItems = document.getElementById('cartItems');
            cartItems.innerHTML = '';
            cart.forEach((item, index) => {
                cartItems.innerHTML += `
                    <div class="cart-item">
                        <span>${item.name} - ${item.price}€</span>
                        <button onclick="removeFromCart(${index})">❌</button>
                    </div>
                `;
            });
            const total = cart.reduce((sum,item) => sum + item.price, 0);
            document.getElementById('totalPrice').innerText = total;
        }

        function toggleCart() {
            const modal = document.getElementById('cartModal');
            modal.style.display = modal.style.display === 'flex' ? 'none' : 'flex';
        }

        function checkout() {
            if(cart.length === 0){
                alert("Votre panier est vide !");
            } else {
                const total = cart.reduce((sum,item) => sum + item.price, 0);
                alert(`Merci pour votre achat ! Total: ${total} €\nVous pouvez intégrer un vrai paiement ici (PayPal, Flutterwave, etc.)`);
                cart = [];
                updateCart();
                toggleCart();
            }
        }

        document.getElementById('cartModal').addEventListener('click', function(e){
            if(e.target === this) toggleCart();
        });

        renderProducts();
    </script>
</body>
</html>
