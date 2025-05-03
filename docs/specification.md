# WIC (Web Images Cryptography) Specification

## 1. Introduction

**WIC** (Web Images Cryptography) is a new file format (`*.wic`) designed to address the challenges of web image delivery by providing ultra-compression and ultra-security. WIC files are collections of images that are compressed using advanced algorithms (e.g., Huffman coding), encrypted with AES-256-CBC (via OpenSSL), and decrypted/decompressed locally in the browser. This specification outlines the structure, compression, encryption, and browser integration requirements for WIC.

WIC aims to:
- Reduce image file sizes for faster web page loading.
- Enhance security by encrypting images during transfer.
- Preserve compatibility with existing web standards (e.g., HTTPS, image paths).

This specification is part of the [Ascoos Framework](https://sourceforge.net/projects/ascoos-fw/) and is released as an open standard for the global web community.

## 2. File Structure

A WIC file (`*.wic`) consists of the following components:

### 2.1. Header
- **Format**: JSON
- **Purpose**: Stores metadata about the images in the collection.
- **Contents**:
  - `version`: The WIC format version (e.g., `1.0`).
  - `images`: An array of image metadata objects, each containing:
    - `path`: The original path of the image (e.g., `/images/photo.jpg`).
    - `type`: The MIME type of the image (e.g., `image/jpeg`).
    - `size`: The size of the compressed image data (in bytes).
    - `offset`: The byte offset where the image data starts in the file.

### 2.2. Data
- **Format**: Binary
- **Purpose**: Stores the compressed and encrypted image data.
- **Contents**:
  - A sequence of image data blocks, each compressed with Huffman coding and encrypted with AES-256-CBC.

### Example File Structure
```json
[WIC Header (JSON)]
{
  "version": "1.0",
  "images": [
    {
      "path": "/images/photo1.jpg",
      "type": "image/jpeg",
      "size": 1024,
      "offset": 512
    },
    {
      "path": "/images/photo2.png",
      "type": "image/png",
      "size": 2048,
      "offset": 1536
    }
  ]
}
[WIC Data (Binary)]
[Image 1: Huffman-compressed and AES-256-CBC encrypted data]
[Image 2: Huffman-compressed and AES-256-CBC encrypted data]
```

## 3. Compression

### 3.1. JSON Header
- The header is first encoded as a JSON string.
- The JSON string is compressed using gzip to reduce its size.

### 3.2. Image Data
- Each image is processed as follows:
  1. **Conversion to Base64**: The raw image data is encoded as a Base64 string.
  2. **Huffman Coding**: The Base64 string is compressed using the Huffman coding algorithm for lossless compression.
  3. **Storage**: The compressed data is stored in the WIC file at the specified offset.

## 4. Encryption

### 4.1. Algorithm
- **Cipher**: AES-256-CBC
- **Library**: OpenSSL
- **Key**: A 256-bit encryption key provided by the application.
- **IV**: A 128-bit initialization vector (IV) generated randomly for each WIC file.

### 4.2. Process
- The compressed image data (Huffman-coded) is encrypted using AES-256-CBC.
- The IV is prepended to the encrypted data.
- The combined IV + encrypted data is Base64-encoded for storage in the WIC file.

### 4.3. Security Considerations
- WIC relies on HTTPS for secure transfer between the server and the browser.
- The encryption key must be securely shared between the server and the browser (e.g., via a secure channel or public-key cryptography).

## 5. Browser Integration

### 5.1. MIME Type
- WIC files should be served with the MIME type: `image/wic`.

### 5.2. Decryption and Decompression
Browsers supporting WIC should implement the following steps:

1. **Fetch the WIC File**:
   - Use HTTPS to securely download the `.wic` file.
   - Example: `fetch('https://example.com/images.wic')`

2. **Parse the Header**:
   - Read the JSON header (decompressed with gzip).
   - Extract the `images` array to determine the paths, types, sizes, and offsets of the images.

3. **Decrypt the Data**:
   - Use the Web Crypto API to decrypt the image data:
     - Algorithm: AES-CBC
     - Key: The 256-bit encryption key (securely provided to the browser).
     - IV: Extracted from the first 16 bytes of the encrypted data.
   - Example:
     ```javascript
     const cryptoKey = await crypto.subtle.importKey(
         'raw',
         new TextEncoder().encode('your_secret_key'),
         { name: 'AES-CBC' },
         false,
         ['decrypt']
     );
     const decrypted = await crypto.subtle.decrypt(
         { name: 'AES-CBC', iv: ivBuffer },
         cryptoKey,
         encryptedBuffer
     );
     ```

4. **Decompress the Data**:
   - Decompress the decrypted data using Huffman decoding.
   - Convert the decompressed Base64 string back to binary image data.

5. **Render the Images**:
   - Create `Image` objects with the decrypted/decompressed data.
   - Set the `src` attribute to the `path` from the header to preserve the original URL.
   - Example:
     ```javascript
     const img = new Image();
     img.src = imageData.path; // e.g., '/images/photo.jpg'
     img.src = `data:${imageData.type};base64,${decompressedData}`;
     document.body.appendChild(img);
     ```

### 5.3. Fallback for Non-Supported Browsers
- For browsers that do not natively support WIC, a JavaScript polyfill can be used (see the [WIC JavaScript library](https://github.com/alexsoft-software/wic/blob/main/wic.js)).

## 6. Security Considerations

- **HTTPS Requirement**: WIC files must be served over HTTPS to ensure secure transfer.
- **Key Management**: The encryption key should be securely managed and shared between the server and the browser.
- **Tamper Protection**: The JSON header can include a digital signature (e.g., HMAC-SHA256) to verify the integrity of the WIC file.

## 7. Benefits of WIC

- **Performance**: Ultra-compressed images reduce bandwidth usage and improve page load times.
- **Security**: Encrypted images protect sensitive content during transfer.
- **Compatibility**: Preserves original image paths, ensuring seamless integration with existing websites.
- **Scalability**: Suitable for large collections of images, as the JSON header allows efficient indexing.

## 8. Implementation Notes

### 8.1. Server-Side Generation
- Use the `TWICHandler` class in the Ascoos Framework to create WIC files (see [README.md](../README.md) for examples).

### 8.2. Client-Side Rendering
- Browsers should implement native support for the `image/wic` MIME type.
- Until native support is available, the [WIC JavaScript polyfill](https://github.com/alexsoft-software/wic/blob/main/wic.js) can be used.

## 9. Future Directions

- **Lossy Compression**: Add support for lossy compression (e.g., WebP conversion) to further reduce file sizes.
- **Asymmetric Encryption**: Use RSA for secure key exchange between the server and the browser.
- **Standardization**: Work with the W3C and browser vendors to make WIC a recognized web standard.

## 10. Contact

For questions, feedback, or collaboration opportunities, please contact:

- **Author**: Drogidis Christos
- **Website**: [ascoos.com](https://ascoos.com)
- **GitHub Issues**: [Open an issue](https://github.com/alexsoft-software/wic/issues)

---

This specification is a living document and will be updated as WIC evolves. Contributions are welcome!
