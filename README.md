<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Restaurant Menu Card Maker</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f4f4f4;
            margin: 0;
            padding: 0;
        }

        .container {
            width: 80%;
            margin: auto;
            overflow: hidden;
        }

        h1, h2 {
            text-align: center;
        }

        form {
            background: #fff;
            padding: 20px;
            margin-bottom: 20px;
            border-radius: 5px;
        }

        label {
            display: block;
            margin-bottom: 5px;
        }

        input, select, button {
            width: 100%;
            padding: 10px;
            margin-bottom: 10px;
        }

        #menu-preview {
            background: #fff;
            padding: 20px;
            border-radius: 5px;
        }

        #first-page-preview, #second-page-preview {
            margin-bottom: 20px;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Restaurant Menu Card Maker</h1>
        <form id="restaurant-details-form">
            <h2>Restaurant Details</h2>
            <label for="restaurant-name">Name:</label>
            <input type="text" id="restaurant-name" required>
            <label for="restaurant-address">Address:</label>
            <input type="text" id="restaurant-address" required>
            <label for="proprietor-name">Proprietor's Name:</label>
            <input type="text" id="proprietor-name" required>
            <label for="phone-number">Phone Number:</label>
            <input type="text" id="phone-number" required>
            <label for="logo-upload">Logo:</label>
            <input type="file" id="logo-upload" accept="image/*">
        </form>

        <form id="menu-item-form">
            <h2>Add Menu Item</h2>
            <label for="item-name">Item Name:</label>
            <input type="text" id="item-name" required>
            <label for="item-price">Price (₹):</label>
            <input type="text" id="item-price" required>
            <label for="item-category">Category:</label>
            <select id="item-category">
                <option value="Starters">Starters</option>
                <option value="Main Course">Main Course</option>
                <option value="Rice Items">Rice Items</option>
                <option value="Drinks">Drinks</option>
            </select>
            <label for="item-type">Type:</label>
            <select id="item-type">
                <option value="Veg">Veg</option>
                <option value="Non-Veg">Non-Veg</option>
            </select>
            <button type="button" onclick="addMenuItem()">Add Item</button>
        </form>

        <div id="menu-preview">
            <h2>Menu Preview</h2>
            <div id="first-page-preview"></div>
            <div id="second-page-preview"></div>
        </div>

        <button onclick="saveMenu()">Save Menu</button>
    </div>

    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.3.1/jspdf.umd.min.js"></script>
    <script>
        let menuItems = [];

        function addMenuItem() {
            const itemName = document.getElementById('item-name').value;
            const itemPrice = document.getElementById('item-price').value;
            const itemCategory = document.getElementById('item-category').value;
            const itemType = document.getElementById('item-type').value;

            const menuItem = {
                name: itemName,
                price: `₹${itemPrice}`,
                category: itemCategory,
                type: itemType
            };

            menuItems.push(menuItem);
            updateMenuPreview();
        }

        function updateMenuPreview() {
            const firstPagePreview = document.getElementById('first-page-preview');
            const secondPagePreview = document.getElementById('second-page-preview');

            firstPagePreview.innerHTML = `
                <h3>${document.getElementById('restaurant-name').value}</h3>
                <p>${document.getElementById('restaurant-address').value}</p>
                <p>${document.getElementById('proprietor-name').value}</p>
                <p>${document.getElementById('phone-number').value}</p>
            `;

            secondPagePreview.innerHTML = menuItems.map(item => `
                <p>${item.name} - ${item.price} (${item.category}, ${item.type})</p>
            `).join('');
        }

        function saveMenu() {
            const { jsPDF } = window.jspdf;
            const doc = new jsPDF();
            doc.text(document.getElementById('restaurant-name').value, 10, 10);
            doc.text(document.getElementById('restaurant-address').value, 10, 20);
            doc.text(document.getElementById('proprietor-name').value, 10, 30);
            doc.text(document.getElementById('phone-number').value, 10, 40);

            menuItems.forEach((item, index) => {
                doc.text(`${item.name} - ${item.price} (${item.category}, ${item.type})`, 10, 50 + (index * 10));
            });

            doc.save(`${document.getElementById('restaurant-name').value}_menu.pdf`);
        }
    </script>
</body>
</html>
