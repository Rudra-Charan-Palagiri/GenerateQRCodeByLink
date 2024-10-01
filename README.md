<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Restaurant Menu Maker</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      padding: 20px;
    }

    .container {
      max-width: 600px;
      margin: 0 auto;
    }

    label {
      display: block;
      margin-bottom: 5px;
    }

    input, select, button {
      width: 100%;
      margin-bottom: 10px;
      padding: 10px;
      font-size: 16px;
    }

    button {
      cursor: pointer;
      background-color: #4CAF50;
      color: white;
      border: none;
    }

    #menuPreview {
      margin-top: 20px;
      padding: 10px;
      border: 5px solid #ddd;
    }

    #firstPagePreview {
      margin-top: 20px;
      border: 5px solid #ddd;
      padding: 20px;
      text-align: center;
    }

    img {
      max-width: 100px;
      margin-bottom: 10px;
    }

    #qrcode {
      margin-top: 20px;
    }
  </style>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/qrcodejs/1.0.0/qrcode.min.js"></script>
</head>
<body>
  <div class="container">
    <h1>Create Your Restaurant Menu</h1>
    <form id="menuForm">
      <!-- Restaurant Info -->
      <div>
        <label for="restaurantName">Restaurant Name:</label>
        <input type="text" id="restaurantName" placeholder="Enter restaurant name" required>
      </div>
      <div>
        <label for="restaurantAddress">Address:</label>
        <input type="text" id="restaurantAddress" placeholder="Enter restaurant address" required>
      </div>
      <div>
        <label for="restaurantProp">Proprietor Name:</label>
        <input type="text" id="restaurantProp" placeholder="Enter proprietor name" required>
      </div>
      <div>
        <label for="restaurantPhone">Phone Number:</label>
        <input type="tel" id="restaurantPhone" placeholder="Enter phone number" required>
      </div>
      <div>
        <label for="restaurantLogo">Logo:</label>
        <input type="file" id="restaurantLogo" accept="image/*" required>
      </div>

      <!-- Restaurant Name Font Settings -->
      <div>
        <label for="restaurantFontColor">Restaurant Name Font Color:</label>
        <input type="color" id="restaurantFontColor" required>
      </div>
      <div>
        <label for="restaurantFontSize">Restaurant Name Font Size (px):</label>
        <input type="number" id="restaurantFontSize" placeholder="Enter font size" required>
      </div>

      <!-- Background and Border Color -->
      <div>
        <label for="backgroundColor">First Page Background Color:</label>
        <input type="color" id="backgroundColor" required>
      </div>
      <div>
        <label for="borderColor">First Page Border Color:</label>
        <input type="color" id="borderColor" required>
      </div>

      <!-- Font Color and Size for Items -->
      <div>
        <label for="fontColor">Menu Font Color:</label>
        <input type="color" id="fontColor" required>
      </div>
      <div>
        <label for="fontSize">Menu Font Size (px):</label>
        <input type="number" id="fontSize" placeholder="Enter font size" required>
      </div>

      <!-- Category -->
      <div>
        <label for="category">Food Category:</label>
        <select id="category" required>
          <option value="starters">Starters</option>
          <option value="main-course">Main Course</option>
          <option value="rice-items">Rice Items</option>
          <option value="drinks">Drinks</option>
        </select>
      </div>

      <!-- Veg/Non-Veg -->
      <div>
        <label>Type:</label>
        <input type="radio" name="type" value="Veg" required> Veg
        <input type="radio" name="type" value="Non-Veg" required> Non-Veg
      </div>

      <!-- Item Info -->
      <div>
        <label for="itemName">Item Name:</label>
        <input type="text" id="itemName" placeholder="Enter item name" required>
      </div>
      <div>
        <label for="itemPrice">Item Price:</label>
        <input type="number" id="itemPrice" placeholder="Enter item price" required>
      </div>

      <!-- Submit -->
      <button type="button" onclick="addItem()">Add to Menu</button>
      <button type="button" onclick="saveMenu()">Save Menu</button>
    </form>

    <!-- First Page Preview -->
    <h2>First Page Preview</h2>
    <div id="firstPagePreview"></div>
    <h2>Menu Preview</h2>
    <div id="menuPreview" class="menu"></div>

    <!-- QR Code -->
    <div id="qrcode"></div>
  </div>

  <script>
    let menuItems = [];

    function addItem() {
      const category = document.getElementById('category').value;
      const itemName = document.getElementById('itemName').value;
      const itemPrice = document.getElementById('itemPrice').value;
      const itemType = document.querySelector('input[name="type"]:checked').value;

      const newItem = {
        category,
        itemType,
        itemName,
        itemPrice
      };

      menuItems.push(newItem);
      displayMenu();
    }

    function displayMenu() {
      const menuPreview = document.getElementById('menuPreview');
      menuPreview.innerHTML = '';

      const fontColor = document.getElementById('fontColor').value;
      const fontSize = document.getElementById('fontSize').value + 'px';

      // Apply styles
      menuPreview.style.color = fontColor;
      menuPreview.style.fontSize = fontSize;

      const categories = ['starters', 'main-course', 'rice-items', 'drinks'];
      categories.forEach(cat => {
        const items = menuItems.filter(item => item.category === cat);
        if (items.length > 0) {
          const categoryTitle = document.createElement('h3');
          categoryTitle.textContent = cat.replace('-', ' ').toUpperCase();
          menuPreview.appendChild(categoryTitle);

          const vegItems = items.filter(item => item.itemType === 'Veg');
          const nonVegItems = items.filter(item => item.itemType === 'Non-Veg');

          if (vegItems.length > 0) {
            const vegTitle = document.createElement('h4');
            vegTitle.textContent = 'Veg';
            menuPreview.appendChild(vegTitle);
            vegItems.forEach(item => {
              const itemElement = document.createElement('div');
              itemElement.textContent = `${item.itemName} - ₹${item.itemPrice}`;
              menuPreview.appendChild(itemElement);
            });
          }

          if (nonVegItems.length > 0) {
            const nonVegTitle = document.createElement('h4');
            nonVegTitle.textContent = 'Non-Veg';
            menuPreview.appendChild(nonVegTitle);
            nonVegItems.forEach(item => {
              const itemElement = document.createElement('div');
              itemElement.textContent = `${item.itemName} - ₹${item.itemPrice}`;
              menuPreview.appendChild(itemElement);
            });
          }
        }
      });

      displayFirstPage();
    }

    function displayFirstPage() {
      const firstPagePreview = document.getElementById('firstPagePreview');
      const restaurantName = document.getElementById('restaurantName').value;
      const restaurantAddress = document.getElementById('restaurantAddress').value;
      const restaurantProp = document.getElementById('restaurantProp').value;
      const restaurantPhone = document.getElementById('restaurantPhone').value;
      const restaurantLogo = document.getElementById('restaurantLogo').files[0];

      const backgroundColor = document.getElementById('backgroundColor').value;
      const borderColor = document.getElementById('borderColor').value;

      firstPagePreview.style.backgroundColor = backgroundColor;
      firstPagePreview.style.borderColor = borderColor;

      firstPagePreview.innerHTML = `
        <h2>Welcome to ${restaurantName}</h2>
        <div>
          <img src="${restaurantLogo ? URL.createObjectURL(restaurantLogo) : ''}" alt="Restaurant Logo">
        </div>
        <h3>${restaurantAddress}</h3>
        <h4>Proprietor: ${restaurantProp}</h4>
        <h4>Phone: ${restaurantPhone}</h4>
        <h4>Scan This QR For Online Menu Card</h4>
      `;
      generateQRCode(`${restaurantName}_Menu.pdf`); // Generate QR code with the PDF file name
    }

    function generateQRCode(pdfFileName) {
      const qrcode = new QRCode(document.getElementById("qrcode"), {
        text: pdfFileName,  // QR code contains the file name of the PDF
        width: 128,
        height: 128,
      });
    }

    async function saveMenu() {
      const { jsPDF } = window.jspdf;
      const doc = new jsPDF();

      // First Page Information
      const restaurantName = document.getElementById('restaurantName').value;
      const restaurantAddress = document.getElementById('restaurantAddress').value;
      const restaurantProp = document.getElementById('restaurantProp').value;
      const restaurantPhone = document.getElementById('restaurantPhone').value;
      const restaurantLogo = document.getElementById('restaurantLogo').files[0];

      // Add Logo
      if (restaurantLogo) {
        const logoImage = await loadImage(restaurantLogo);
        doc.addImage(logoImage, 'PNG', 10, 10, 50, 50); // logo dimensions
      }

      doc.setFontSize(20);
      doc.text(`Welcome to ${restaurantName}`, 10, 70);
      doc.setFontSize(14);
      doc.text(`Address: ${restaurantAddress}`, 10, 80);
      doc.text(`Proprietor: ${restaurantProp}`, 10, 90);
      doc.text(`Phone: ${restaurantPhone}`, 10, 100);

      // Add QR Code
      const qrCodeCanvas = document.getElementById('qrcode').children[0];
      const qrCodeImage = await loadImage(qrCodeCanvas.toDataURL());
      doc.addImage(qrCodeImage, 'PNG', 10, 110, 50, 50); // QR code dimensions

      // Add a page break
      doc.addPage();

      // Menu Items
      const fontColor = document.getElementById('fontColor').value;
      const fontSize = document.getElementById('fontSize').value;

      doc.setTextColor(fontColor);
      doc.setFontSize(fontSize);

      const categories = ['starters', 'main-course', 'rice-items', 'drinks'];
      categories.forEach(cat => {
        const items = menuItems.filter(item => item.category === cat);
        if (items.length > 0) {
          doc.setFontSize(16);
          doc.text(cat.replace('-', ' ').toUpperCase(), 10, doc.autoTable.previous.finalY + 10);
          doc.setFontSize(14);

          const vegItems = items.filter(item => item.itemType === 'Veg');
          const nonVegItems = items.filter(item => item.itemType === 'Non-Veg');

          if (vegItems.length > 0) {
            doc.text('Veg', 10, doc.autoTable.previous.finalY + 10);
            vegItems.forEach(item => {
              doc.text(`${item.itemName} - ₹${item.itemPrice}`, 20, doc.autoTable.previous.finalY + 10);
            });
          }

          if (nonVegItems.length > 0) {
            doc.text('Non-Veg', 10, doc.autoTable.previous.finalY + 10);
            nonVegItems.forEach(item => {
              doc.text(`${item.itemName} - ₹${item.itemPrice}`, 20, doc.autoTable.previous.finalY + 10);
            });
          }
        }
      });

      // Save the PDF with the name of the restaurant
      doc.save(`${restaurantName}_Menu.pdf`);
    }

    function loadImage(file) {
      return new Promise((resolve, reject) => {
        const reader = new FileReader();
        reader.onload = () => {
          const img = new Image();
          img.onload = () => resolve(img);
          img.src = reader.result;
        };
        reader.onerror = reject;
        reader.readAsDataURL(file);
      });
    }
  </script>
</body>
</html>
