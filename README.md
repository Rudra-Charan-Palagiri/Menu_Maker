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

    #qrcode {
      margin-top: 20px;
    }

    .menu {
      padding: 20px;
      background-size: cover;
      background-position: center;
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
        <label for="backgroundColor">Menu Background Color:</label>
        <input type="color" id="backgroundColor" required>
      </div>
      <div>
        <label for="borderColor">Menu Border Color:</label>
        <input type="color" id="borderColor" required>
      </div>

      <!-- Online Image -->
      <div>
        <label for="menuImage">Background Image URL:</label>
        <input type="url" id="menuImage" placeholder="Optional: Paste image URL">
      </div>

      <!-- Font Color and Size for Items -->
      <div>
        <label for="fontColor">Font Color:</label>
        <input type="color" id="fontColor" required>
      </div>
      <div>
        <label for="fontSize">Font Size (px):</label>
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
      <button type="button" onclick="generateMenu()">Generate Menu</button>
    </form>

    <!-- Menu Display -->
    <h2>Menu Preview</h2>
    <div id="menuPreview" class="menu"></div>

    <!-- Generate PDF and QR Code -->
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

      const backgroundColor = document.getElementById('backgroundColor').value;
      const borderColor = document.getElementById('borderColor').value;
      const backgroundImage = document.getElementById('menuImage').value;
      const fontColor = document.getElementById('fontColor').value;
      const fontSize = document.getElementById('fontSize').value + 'px';

      // Apply styles
      menuPreview.style.backgroundColor = backgroundColor;
      menuPreview.style.borderColor = borderColor;
      menuPreview.style.color = fontColor;
      menuPreview.style.fontSize = fontSize;
      if (backgroundImage) {
        menuPreview.style.backgroundImage = `url(${backgroundImage})`;
      }

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
    }

    function generateMenu() {
      const restaurantName = document.getElementById('restaurantName').value;
      const restaurantAddress = document.getElementById('restaurantAddress').value;
      const restaurantFontColor = document.getElementById('restaurantFontColor').value;
      const restaurantFontSize = document.getElementById('restaurantFontSize').value + 'px';

      const doc = new jsPDF();
      
      // Restaurant Name
      doc.setFontSize(parseInt(restaurantFontSize));
      doc.setTextColor(restaurantFontColor);
      doc.text(restaurantName, 20, 20);
      
      // Restaurant Address
      doc.setFontSize(12);
      doc.setTextColor('#000000');
      doc.text(restaurantAddress, 20, 30);

      let y = 40;

      const categories = ['starters', 'main-course', 'rice-items', 'drinks'];
      categories.forEach(cat => {
        const items = menuItems.filter(item => item.category === cat);
        if (items.length > 0) {
          doc.setFontSize(16);
          doc.setTextColor('#000000');
          doc.text(cat.replace('-', ' ').toUpperCase(), 20, y);
          y += 10;

          const vegItems = items.filter(item => item.itemType === 'Veg');
          const nonVegItems = items.filter(item => item.itemType === 'Non-Veg');

          if (vegItems.length > 0) {
            doc.setFontSize(14);
            doc.text('Veg', 20, y);
            y += 10;
            vegItems.forEach(item => {
              doc.setFontSize(12);
              doc.text(`${item.itemName} - ₹${item.itemPrice}`, 30, y);
              y += 10;
            });
          }

          if (nonVegItems.length > 0) {
            doc.setFontSize(14);
            doc.text('Non-Veg', 20, y);
            y += 10;
            nonVegItems.forEach(item => {
              doc.setFontSize(12);
              doc.text(`${item.itemName} - ₹${item.itemPrice}`, 30, y);
              y += 10;
            });
          }
        }
      });

      doc.save(`${restaurantName}_Menu.pdf`);
    }
  </script>
</body>
</html>
