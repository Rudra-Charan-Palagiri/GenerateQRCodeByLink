# QR Code Generator

This is a simple QR code generator that uses HTML, CSS, and JavaScript.

## How to Use

1. Open the file in a web browser.
2. Enter a website link.
3. Click "Generate QR Code."
4. Download the generated QR code.

## HTML, CSS, and JavaScript Code

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>QR Code Generator</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f0f0f0;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
        }

        .container {
            background-color: #fff;
            padding: 20px;
            border-radius: 8px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
            width: 300px;
            text-align: center;
        }

        h1 {
            margin-bottom: 20px;
            font-size: 24px;
            color: #333;
        }

        label {
            display: block;
            margin: 10px 0 5px;
            color: #555;
        }

        input {
            width: 100%;
            padding: 8px;
            margin-bottom: 20px;
            border: 1px solid #ccc;
            border-radius: 4px;
        }

        button {
            background-color: #4CAF50;
            color: white;
            padding: 10px 20px;
            border: none;
            border-radius: 4px;
            cursor: pointer;
        }

        button:hover {
            background-color: #45a049;
        }

        #qrcode {
            margin-top: 20px;
        }

        #downloadLink {
            margin-top: 10px;
            display: block;
            text-decoration: none;
            background-color: #4CAF50;
            color: white;
            padding: 10px 20px;
            border-radius: 4px;
            cursor: pointer;
        }
    </style>
    <script src="https://cdn.jsdelivr.net/npm/qrcode@1.4.4/build/qrcode.min.js"></script>
</head>
<body>
    <div class="container">
        <h1>QR Code Generator</h1>
        <form id="qrForm">
            <label for="link">Website Link:</label>
            <input type="text" id="link" name="link" required>

            <button type="submit">Generate QR Code</button>
        </form>

        <div id="qrcode"></div>
        <a id="downloadLink" style="display:none;">Download QR Code</a>
    </div>

    <script>
        document.getElementById('qrForm').addEventListener('submit', function(event) {
            event.preventDefault();

            // Clear any existing QR code
            document.getElementById('qrcode').innerHTML = '';
            document.getElementById('downloadLink').style.display = 'none';

            // Get the input link
            const link = document.getElementById('link').value;

            // Generate the QR code
            QRCode.toCanvas(document.getElementById('qrcode'), link, function(error, canvas) {
                if (error) {
                    console.error(error);
                    alert('Failed to generate QR code.');
                    return;
                }

                // Create a download link for the QR code
                const downloadLink = document.getElementById('downloadLink');
                downloadLink.href = canvas.toDataURL('image/png');
                downloadLink.download = 'qrcode.png';
                downloadLink.style.display = 'block';
                downloadLink.textContent = 'Download QR Code';
            });
        });
    </script>
</body>
</html>
