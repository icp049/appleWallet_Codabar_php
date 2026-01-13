🍎 Apple PassKit (PHP) — CODABAR Workaround

Apple Wallet does NOT support CODABAR barcodes at the time of writing.
This project is a workaround for developers who still need CODABAR-compatible data inside Apple Wallet passes.

Instead of relying on Apple’s native barcode formats, this implementation:

- Generates CODABAR barcodes as images
- Embeds them into an Apple Wallet pass
- Uses php-pkpass to properly sign and generate .pkpass files

📥 Composer Installation

- composer require pkpass/pkpass picqer/php-barcode-generator


🛠️ Apple Developer Setup

1️⃣ Create Apple Developer Account
- Create an Apple Developer Account for your organization.

2️⃣ Go to Certificates
- Navigate to Certificates, IDs & Profiles

3️⃣ Create Pass Type ID
- Click ➕
- Create a Pass Type ID
- Identifier MUST start with pass.
- eg. pass.test01


🔐 Certificate Generation (macOS)

4️⃣ Generate Certificate Signing Request
- Open Keychain Access
- Certificate Assistant → Generate Certificate
- This creates a SignerCert file

5️⃣ Upload Certificate Signing Request to Apple
- Create a certificate for your Pass Type ID
- Upload the SignerCert in the apple developer account under the certificates
- Leave name blank when downloading

6️⃣ Download Certificate

- You will receive: pass.cer

7️⃣ Import into Keychain

- Drag pass.cer into Keychain Access and you should see the certificater and private key underneath it. There should be a down arrow to expand the private key.


8️⃣ Export .p12
- Select certificate + private key
- Export 2 items
- Save as: certificates.p12 or whatever you like

⚠️ Useful Links
https://developer.apple.com/help/account/create-certificates/create-a-certificate-signing-request/

🔧 Conversion Commands

- cd dirname
- $env:OPENSSL_MODULES="C:\Users\ipedeglorio\OpenSSL-Win64\lib\ossl-modules" 
- openssl pkcs12 -legacy -in Certificates.p12 -nodes -out key_decrypted.tmp
- Enter Import Password: (⚠️ Place a new password here, remember that as you will use that in your code later on)
- openssl pkcs12 -in key_decrypted.tmp -export -out key_new.p12
- Enter Export Password: (⚠️ Enter the password you indicated in import)
- Verifying - Enter Export Password:

✅ after the export password you should have the key_new.p12 in the same directory, you can then put the key_new.p12 in teh root folder of your project for testing or wherever.



🚨 OpenSSL Error with Certificates.p12 (Windows Fix)

Apple-generated PassKit certificates often use legacy encryption that newer versions of OpenSSL cannot read by default.
This commonly causes errors when php-pkpass tries to load Certificates.p12.

- Download legacy.dll
- Place it inside: OpenSSL-Win64\lib\ossl-modules
- Enable OpenSSL Legacy Provider by using this command: $env:OPENSSL_MODULES="C:\Users\ipedeglorio\OpenSSL-Win64\lib\ossl-modules"
⚠️ This step is critical — without it, OpenSSL will fail. You can do this with the conversion commands mentioned above


🧾 Pass JSON Example

```php
$passJson = [
    "formatVersion" => 1,
    "passTypeIdentifier" => "pass.yourpassid",
    "serialNumber" => $serialNumber,
    "teamIdentifier" => "YOUR_TEAM_ID",
    "organizationName" => "Your Organization",
    "description" => "Description for your pass",
    "backgroundColor" => "rgb(255,255,255)",
    "foregroundColor" => "rgb(0,80,136)",

    "storeCard" => [
        "secondaryFields" => [
            [
                "key" => "name",
                "label" => "Name",
                "value" => $name
            ],
            [
                "key" => "accountNumber",
                "label" => "Account Number",
                "value" => $accountNumber
            ]
        ]
    ]
];
```



🧩 PHP Pass Generation

```php

$pass = new PKPass('key_new.p12', 'key_new.p12 password'); ⚠️put the import/export password that you set here
$pass->setData(json_encode($passJson));

$pass->addFile("icon.png");
$pass->addFile("icon@2x.png");
$pass->addFile("logo.png");
$pass->addFile("strip.png");

$pass->create(true);

```

<br>
<br>


🧪 Included Examples in This Repository

- This repository includes two complete working examples for generating Apple Wallet passes using a CODABAR workaround, each demonstrating a different approach to barcode generation.
- Both examples include: A frontend form and a backend php script
- Full PassKit (.pkpass) generation using php-pkpass

1️⃣ Example 1: PHP-Only Barcode Generation (Backend)
The barcode is generated entirely on the server
Uses the PHP library:
-picqer/php-barcode-generator
-Generates a CODABAR barcode image (PNG) in PHP
-Saves the barcode as strip.png
-Embeds the image into the Apple Wallet pass

🔹 Frontend
Plain HTML form
User submits:
-Name
-Account number
-Membership flag
-No JavaScript required

🔹 Backend (PHP)
-Generates CODABAR using PHP
-Rescales and pads the barcode image for Apple Wallet compatibility
-Creates pass.json
-Signs and generates the .pkpass file

2️⃣ Example 2: JavaScript Barcode Generation (Frontend) + PHP Backend
- The barcode is generated in the browser using JavaScript

Uses:

JsBarcode (CODABAR)
- Barcode is rendered to a hidden <canvas>
- Converted to a Base64 PNG
- Sent to PHP via a hidden form field

🔹 Frontend (HTML + JavaScript)
- Uses JsBarcode
- Generates the CODABAR barcode client-side
- Converts the barcode to a Base64 image
- Submits it along with form data

🔹 Backend (PHP)

- Receives the Base64 barcode image
- Decodes and saves it as strip.png
- Creates pass.json
- Signs and generates the .pkpass file

