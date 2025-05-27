# WIC (Web Images Cryptography)



**WIC** (Web Images Cryptography) is a revolutionary new file format (`*.wic`) designed to ultra-compress and ultra-encrypt web images, providing faster load times, enhanced security, and seamless integration with modern web technologies. 

WIC files are compressed using advanced algorithms like Huffman coding, encrypted with OpenSSL, and can be decrypted/decompressed locally in the browser.

This project is part of the [Ascoos Framework](https://sourceforge.net/projects/ascoos-fw/) and is released as an **open standard** to benefit the global web community.

## 🌟 Features
- **Ultra-Compression**: Significantly reduces image file sizes using JSON-based data structures and Huffman coding, leading to faster page load times.
- **Ultra-Security**: Encrypts images with AES-256-CBC (via OpenSSL) during transfer, ensuring data privacy and protection against interception.
- **Local Decryption**: Images are decrypted and decompressed in the browser, keeping them secure during transit.
- **Path Preservation**: Stores the original image paths in the JSON header, preventing broken `img src` links.
- **SSL Support**: Seamlessly integrates with HTTPS for secure data transfer.
- **Open Standard**: Free for everyone to use, modify, and contribute to, fostering innovation in web image handling.

## 🚀 Why WIC?

The web is increasingly image-heavy, but traditional formats like JPEG and PNG are not optimized for both size and security. WIC addresses these challenges by:

- Reducing bandwidth usage with ultra-compressed files.
- Protecting images from unauthorized access with strong encryption.
- Enabling browsers to handle images natively (with future browser support).

WIC is designed to be the future of web images, and we invite the community to help make it a global standard!

## 📦 Installation

### For Developers (Using Ascoos Framework)

1. Clone the Ascoos Framework repository:

```bash
   git clone https://github.com/alexsoft-software/ascoos-framework.git
```

Include the TWICHandler class in your project:
```php
    require_once 'path/to/ascoos/TWICHandler.php';
```

Use the TWICHandler to create .wic files:
```php
    $wic = new TWICHandler(['wic' => ['encryptionKey' => 'your_secret_key']]);
    $images = [
        ['path' => '/images/photo1.jpg', 'file' => 'photo1.jpg'],
        ['path' => '/images/photo2.png', 'file' => 'photo2.png']
    ];
    $wic->createWIC($images, 'images.wic');
```

For Browsers (Using DevBro or JavaScript Polyfill)

DevBro Browser: WIC is natively supported in DevBro, a custom browser built for Ascoos Web Extended Studio. (Coming soon!)

JavaScript Polyfill: For other browsers, use the WIC JavaScript library:

```javascript
    import { WIC } from './wic.js';

    WIC.decode('https://example.com/images.wic', 'your_secret_key').then(images => {
        images.forEach(img => document.body.appendChild(img));
    });
```

## 📖 Usage Example

Creating a WIC File
```php
$wic = new TWICHandler(['wic' => ['encryptionKey' => 'my_secret_key']]);
$images = [
    ['path' => '/images/photo1.jpg', 'file' => 'photo1.jpg'],
    ['path' => '/images/photo2.png', 'file' => 'photo2.png']
];
$wic->createWIC($images, 'images.wic');
```

Displaying WIC Images in a Browser
```html

<script src="wic.js"></script>
<script>
    WIC.decode('https://example.com/images.wic', 'my_secret_key').then(images => {
        images.forEach(img => document.body.appendChild(img));
    });
</script>
```

## 🔧 Technical Details

#### File Structure

    - Header: JSON containing image metadata (path, type, etc.).
    - Data: Huffman-compressed and AES-256-CBC encrypted image data.

#### Compression

WIC uses JSON to structure image data, which is then compressed using the Huffman coding algorithm for maximum efficiency.

#### Encryption

Images are encrypted with AES-256-CBC via OpenSSL, ensuring secure transfer over the web. Decryption happens locally in the browser using the Web Crypto API.

#### Browser Integration

WIC is designed to be natively supported by browsers. We provide a JavaScript polyfill for current browsers and are working towards adoption by major browser vendors (Chrome, Firefox, Safari).

## 🌐 Browser Support Proposal

We invite browser vendors to adopt WIC as a native image format. Benefits include:

    - Faster page load times with ultra-compressed images.
    - Enhanced security with encrypted image transfer.
    - Seamless integration with HTTPS and modern web standards.

Interested vendors can review the [WIC Specification](docs/specification.md) and contact us for collaboration.

## 🤝 Contributing
We welcome contributions from the community! Here's how you can help:

#### Fork the repository:
```bash
    git clone https://github.com/alexsoft-software/wic.git
```
#### Create a new branch for your feature:
```bash
    git checkout -b feature-name
```
#### Commit your changes:
```bash
    git commit -m "Add new feature"
```
    
#### Push to your fork and create a pull request:
```bash
    git push origin feature-name
```

### Contribution Ideas

    - Improve the JavaScript polyfill for better performance.
    - Add support for additional compression algorithms.
    - Help draft proposals for browser vendors.

## 📜 License
WIC is released under the MIT License (LICENSE), making it free for everyone to use, modify, and distribute.

## 🙏 Acknowledgments
WIC is inspired by the need for faster and more secure web images. We thank the open-source community for their continuous inspiration and support.
Let's make WIC the future of web images together! 🚀
