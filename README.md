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

        .color-preview {
            display: inline-block;
            width: 20px;
            height: 20px;
            border-radius: 50%;
            margin-left: 10px;
            vertical-align: middle;
            border: 1px solid #000;
        }

        /* Center text for the menu preview */
        .center-text {
            text-align: center;
        }

        /* Category and Subheading styles */
        .category-heading {
            font-size: 18px;
            font-weight: bold;
            margin-top: 20px;
        }

        .subheading {
            font-size: 16px;
            font-weight: normal;
            margin-left: 10px;
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
            <input type="file" id="logo-upload" accept="image/*" required>
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

        <h3>First Page Settings</h3>
        <label for="firstPageBackgroundColor">Background Color:</label>
        <input type="color" id="firstPageBackgroundColor" onchange="updateColorPreview('firstPageBackgroundColor', 'firstPageBackgroundPreview')">
        <span class="color-preview" id="firstPageBackgroundPreview"></span>
        
        <label for="firstPageFontColor">Font Color:</label>
        <input type="color" id="firstPageFontColor" onchange="updateColorPreview('firstPageFontColor', 'firstPageFontColorPreview')">
        <span class="color-preview" id="firstPageFontColorPreview"></span>
        
        <label for="firstPageFontSize">Font Size (px):</label>
        <input type="number" id="firstPageFontSize" placeholder="Enter font size" required>

        <h3>Second Page Settings</h3>
        <label for="secondPageBackgroundColor">Background Color:</label>
        <input type="color" id="secondPageBackgroundColor" onchange="updateColorPreview('secondPageBackgroundColor', 'secondPageBackgroundPreview')">
        <span class="color-preview" id="secondPageBackgroundPreview"></span>

        <label for="secondPageFontColor">Font Color:</label>
        <input type="color" id="secondPageFontColor" onchange="updateColorPreview('secondPageFontColor', 'secondPageFontColorPreview')">
        <span class="color-preview" id="secondPageFontColorPreview"></span>

        <label for="secondPageFontSize">Font Size (px):</label>
        <input type="number" id="secondPageFontSize" placeholder="Enter font size" required>

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
                <div class="center-text">
                    <h3>${document.getElementById('restaurant-name').value}</h3>
                    <img src="${document.getElementById('logo-upload').files[0] ? URL.createObjectURL(document.getElementById('logo-upload').files[0]) : ''}" alt="Restaurant Logo" style="max-width:100px;">
                    <p>${document.getElementById('restaurant-address').value}</p>
                    <p>${document.getElementById('proprietor-name').value}</p>
                    <p>${document.getElementById('phone-number').value}</p>
                </div>
            `;

            secondPagePreview.innerHTML = menuItems.map(item => `
                <div>
                    <div class="category-heading">${item.category}</div>
                    <div class="subheading">${item.type}: ${item.name} - ${item.price}</div>
                </div>
            `).join('');
        }

        function saveMenu() {
            const { jsPDF } = window.jspdf;
            const doc = new jsPDF();

            // First Page Information
            doc.setFillColor(document.getElementById('firstPageBackgroundColor').value);
            doc.rect(0, 0, doc.internal.pageSize.getWidth(), doc.internal.pageSize.getHeight(), 'F');
            doc.setTextColor(document.getElementById('firstPageFontColor').value);
            doc.setFontSize(document.getElementById('firstPageFontSize').value);
            doc.text(`Welcome to ${document.getElementById('restaurant-name').value}`, 105, 20, { align: 'center' });
            doc.text(`Address: ${document.getElementById('restaurant-address').value}`, 105, 30, { align: 'center' });
            doc.text(`Proprietor: ${document.getElementById('proprietor-name').value}`, 105, 40, { align: 'center' });
            doc.text(`Phone: ${document.getElementById('phone-number').value}`, 105, 50, { align: 'center' });

            // Add Logo
            if (document.getElementById('logo-upload').files[0]) {
                const logoImage = new Image();
                logoImage.src = URL.createObjectURL(document.getElementById('logo-upload').files[0]);
                logoImage.onload = function () {
                    doc.addImage(logoImage, 'PNG', 10, 10, 50, 50); // Adjust logo position and size
                    doc.addPage();
                    addMenuItemsToPDF(doc); // Call the function to add menu items to the PDF after adding the logo
                    doc.save(`${document.getElementById('restaurant-name').value}_menu.pdf`);
                };
            } else {
                doc.addPage();
                addMenuItemsToPDF(doc);
                doc.save(`${document.getElementById('restaurant-name').value}_menu.pdf`);
            }
        }

        function addMenuItemsToPDF(doc) {
            doc.setFillColor(document.getElementById('secondPageBackgroundColor').value);
            doc.rect(0, 0, doc.internal.pageSize.getWidth(), doc.internal.pageSize.getHeight(), 'F');
            doc.setTextColor(document.getElementById('secondPageFontColor').value);
            doc.setFontSize(document.getElementById('secondPageFontSize').value);

            menuItems.forEach((item) => {
                doc.text(`${item.category}: ${item.name} - ${item.price}`, 10, 20 + (menuItems.indexOf(item) * 10));
            });
        }

        function updateColorPreview(colorInputId, previewId) {
            const color = document.getElementById(colorInputId).value;
            document.getElementById(previewId).style.backgroundColor = color;
        }
    </script>
</body>
</html>
