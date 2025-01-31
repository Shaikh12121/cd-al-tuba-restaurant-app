<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Al-Tuba Restaurant Billing & Inventory</title>
    <style>
        body {
            font-family: 'Arial', sans-serif;
            background-color: #fff;
            margin: 0;
            padding: 0;
            color: #333;
        }

        header {
            background-color: #f39c12; /* Bright Orange */
            color: white;
            text-align: center;
            padding: 30px;
        }

        h1 {
            margin: 0;
        }

        .container {
            width: 90%;
            max-width: 1200px;
            margin: 0 auto;
            padding: 30px 0;
        }

        section {
            margin-bottom: 50px;
        }

        table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 20px;
            box-shadow: 0px 3px 6px rgba(0, 0, 0, 0.1);
        }

        th, td {
            padding: 12px;
            text-align: left;
            border: 1px solid #ddd;
        }

        th {
            background-color: #f39c12;
            color: white;
        }

        td input {
            width: 50px;
            padding: 5px;
            text-align: center;
        }

        button {
            padding: 12px 20px;
            background-color: #f39c12;
            border: none;
            color: white;
            font-size: 16px;
            cursor: pointer;
            transition: background-color 0.3s;
        }

        button:hover {
            background-color: #e67e22;
        }

        .btn-group {
            display: flex;
            justify-content: space-between;
            gap: 10px;
        }

        .summary-box {
            background-color: #f8f8f8;
            padding: 15px;
            border-radius: 8px;
            box-shadow: 0px 3px 6px rgba(0, 0, 0, 0.1);
        }

        .summary-box h3 {
            margin-top: 0;
        }

        .summary-box span {
            font-weight: bold;
            font-size: 18px;
        }

        @media (max-width: 768px) {
            .container {
                width: 100%;
                padding: 15px;
            }

            table {
                font-size: 14px;
            }
        }
    </style>
</head>
<body>

<header>
    <h1>Al-Tuba Restaurant Billing & Inventory</h1>
</header>

<div class="container">
    <!-- Inventory Section -->
    <section id="inventory-section">
        <h2>Inventory</h2>
        <div class="btn-group">
            <button onclick="addItemToInventory()">Add New Item</button>
        </div>
        <table id="inventory-table">
            <thead>
                <tr>
                    <th>Name</th>
                    <th>Price</th>
                    <th>Stock</th>
                    <th>COGS</th>
                    <th>Action</th>
                </tr>
            </thead>
            <tbody>
                <!-- Inventory items will be dynamically inserted here -->
            </tbody>
        </table>
    </section>

    <!-- Cart Section -->
    <section id="cart-section">
        <h2>Cart</h2>
        <table id="cart-table">
            <thead>
                <tr>
                    <th>Item</th>
                    <th>Price</th>
                    <th>Quantity</th>
                    <th>Total</th>
                    <th>Remove</th>
                </tr>
            </thead>
            <tbody id="cart-body">
                <!-- Cart items will be dynamically inserted here -->
            </tbody>
        </table>
        <div class="btn-group">
            <button onclick="checkout()">Checkout</button>
        </div>
    </section>

    <!-- Profit & Loss Section -->
    <section id="profit-loss-section">
        <h2>Profit & Loss</h2>
        <div class="summary-box">
            <h3>Sales Summary</h3>
            <p>Total Sales: $<span id="total-sales">0</span></p>
            <p>Total COGS: $<span id="total-cogs">0</span></p>
            <p>Profit/Loss: $<span id="profit-loss">0</span></p>
        </div>
    </section>
</div>

<script>
    // Inventory data with Cost of Goods Sold (COGS)
    let inventory = [
        { id: 1, name: 'Pizza', price: 10, stock: 100, cogs: 4 },
        { id: 2, name: 'Pasta', price: 8, stock: 50, cogs: 3 },
        { id: 3, name: 'Burger', price: 5, stock: 75, cogs: 2 }
    ];

    // Cart data
    let cart = [];

    // Render inventory table
    function renderInventory() {
        const inventoryBody = document.querySelector('#inventory-table tbody');
        inventoryBody.innerHTML = ''; // Clear table
        inventory.forEach(item => {
            const row = document.createElement('tr');
            row.innerHTML = `
                <td>${item.name}</td>
                <td>${item.price}</td>
                <td>${item.stock}</td>
                <td>${item.cogs}</td>
                <td><button onclick="addToCart(${item.id})">Add to Cart</button></td>
            `;
            inventoryBody.appendChild(row);
        });
    }

    // Render cart table
    function renderCart() {
        const cartBody = document.querySelector('#cart-body');
        cartBody.innerHTML = ''; // Clear cart table
        let total = 0;
        cart.forEach(item => {
            const row = document.createElement('tr');
            row.innerHTML = `
                <td>${item.name}</td>
                <td>${item.price}</td>
                <td><input type="number" value="${item.quantity}" onchange="updateCart(${item.id}, event)"></td>
                <td>${item.quantity * item.price}</td>
                <td><button onclick="removeFromCart(${item.id})">Remove</button></td>
            `;
            cartBody.appendChild(row);
            total += item.quantity * item.price;
        });
        updateSummary(total);
    }

    // Add item to cart
    function addToCart(id) {
        const item = inventory.find(i => i.id === id);
        const cartItem = cart.find(i => i.id === id);
        if (cartItem) {
            cartItem.quantity++;
        } else {
            cart.push({ ...item, quantity: 1 });
        }
        renderCart();
    }

    // Remove item from cart
    function removeFromCart(id) {
        cart = cart.filter(item => item.id !== id);
        renderCart();
    }

    // Update item quantity in cart
    function updateCart(id, event) {
        const item = cart.find(i => i.id === id);
        if (item) {
            item.quantity = parseInt(event.target.value);
        }
        renderCart();
    }

    // Checkout and calculate total sales
    function checkout() {
        const total = cart.reduce((sum, item) => sum + (item.quantity * item.price), 0);
        alert(`Checkout complete! Total: $${total}`);
        cart = [];
        renderCart();
    }

    // View Profit/Loss (Sales - COGS)
    function updateSummary(sales) {
        let totalSales = sales;
        let totalCOGS = cart.reduce((sum, item) => sum + (item.quantity * item.cogs), 0);
        let profitLoss = totalSales - totalCOGS;

        document.getElementById('total-sales').innerText = totalSales;
        document.getElementById('total-cogs').innerText = totalCOGS;
        document.getElementById('profit-loss').innerText = profitLoss;
    }

    // Add new item to inventory (Prompt for simplicity)
    function addItemToInventory() {
        const name = prompt('Enter item name');
        const price = parseFloat(prompt('Enter item price'));
        const stock = parseInt(prompt('Enter stock quantity'));
        const cogs = parseFloat(prompt('Enter cost of goods sold (COGS)'));
        const newItem = { id: inventory.length + 1, name, price, stock, cogs };
        inventory.push(newItem);
        renderInventory();
    }

    // Initial render of inventory
    renderInventory();

</script>

</body>
</html>
