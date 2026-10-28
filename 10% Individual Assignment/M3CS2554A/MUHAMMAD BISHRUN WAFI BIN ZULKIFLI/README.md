# ITT440: Parallel and Distributed Computing Implementation using Distributed Image Processing

**Course:** ITT440 – Network Programming  
**Assessment:** Assessment 1 (10%) – Individual Assignment  
**Topic:** Parallel and Distributed Computing  
**Author:** MUHAMMAD BISHRUN WAFI BIN ZULKIFLI
**Number Matrics:** 2025309593
**UiTM Branch:** UiTM Cawangan Melaka, Kampus Jasin  

---

## 📌 1. Introduction

Parallel and Distributed Computing are essential paradigms in modern network programming designed to handle resource-intensive computational workloads efficiently across multiple systems or network nodes. 

* **Parallel Computing:** Focuses on concurrent execution of sub-tasks utilizing multi-core processors within a single system.
* **Distributed Computing:** Extends computational processing across distinct, physically separated devices connected via a network (LAN or wireless access point). Nodes coordinate and exchange data payloads using low-level communication protocols.

In this project, a **Distributed Image Processing System** is designed and implemented using a **Master-Worker Architecture**. The **Linux (Master Node)** offloads raw image data over TCP sockets to a **Windows (Worker Node)**, which performs real-time pixel-level transformations (Grayscale and Color Inversion) and returns the processed image.

---

## 🏗️ 2. System Architecture & Topology

The system topology consists of two distinct cross-platform nodes connected via a network environment:

1. **Master Node (Linux Environment):**
   * Acts as the coordinator and client.
   * Reads a raw input image (`input.jpg`) and transmits its byte stream across the network.
   * Receives, reconstructs, and saves the final processed image (`output_processed.jpg`).

2. **Worker Node (Windows Environment):**
   * Acts as the edge processing node running a TCP socket server on port `65432`.
   * Unpacks incoming image bytes, inspects resolution parameters, and iterates through pixel matrices row-by-row to apply image filters.
   * Transmits the processed image binary data back to the Master Node.


---

## 💻 3. Code Implementation

### A. Worker Node Code (`worker.py` - Windows)
This script listens for incoming TCP socket connections, parses image properties using Python's Pillow library, executes pixel iterations, and returns the result.

```python
import socket
import json
import time
from PIL import Image
import io

HOST = '0.0.0.0'
PORT = 65432

def process_image_with_logs(image_bytes):
    print(f"\n[INFO] Raw image data received from Master Node.")
    image = Image.open(io.BytesIO(image_bytes))
    width, height = image.size
    print(f"[INFO] Image Resolution: {width}x{height} pixels")
    print("-" * 50)
    print(" Executing pixel-level transformation (Grayscale & Inversion)...")
    
    image = image.convert("RGB")
    pixels = image.load()
    start_time = time.time()
    
    for y in range(height):
        if y % max(1, height // 10) == 0:
            print(f"  [Process] Processing row {y} of {height}...")
            
        for x in range(width):
            r, g, b = pixels[x, y]
            gray = int(0.299 * r + 0.587 * g + 0.114 * b)
            pixels[x, y] = (255 - r, 255 - g, 255 - b)

    elapsed_time = time.time() - start_time
    print("-" * 50)
    print(f"[INFO] Image processing completed in {round(elapsed_time, 4)} seconds.")
    
    output_io = io.BytesIO()
    image.save(output_io, format="JPEG")
    return output_io.getvalue(), round(elapsed_time, 4)

def start_worker():
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
        s.bind((HOST, PORT))
        s.listen()
        print(f"[*] Worker Image Processor (Windows) listening on port {PORT}...")
        
        while True:
            conn, addr = s.accept()
            with conn:
                print(f"\n[+] Connection accepted from Master: {addr}")
                size_data = conn.recv(1024)
                if not size_data: break
                image_size = int(size_data.decode('utf-8'))
                conn.sendall(b"READY_FOR_IMAGE")
                
                image_bytes = bytearray()
                while len(image_bytes) < image_size:
                    packet = conn.recv(4096)
                    if not packet: break
                    image_bytes.extend(packet)
                
                processed_img, time_taken = process_image_with_logs(bytes(image_bytes))
                
                conn.sendall(str(len(processed_img)).encode('utf-8'))
                conn.recv(1024)
                conn.sendall(processed_img)
                print(f"[*] Processed image successfully returned to Master.")

if __name__ == "__main__":
    start_worker()
```
### B. Master Node Code (`master.py` - Linux)
This script initiates the socket connection, transmits the image payload, and saves the processed output.

```python
import socket

WORKER_IP = '192.168.x.x'  # Replace with Windows Worker IP address
PORT = 65432
INPUT_IMAGE = 'input.jpg'
OUTPUT_IMAGE = 'output_processed.jpg'

def send_image_task():
    print(f"[*] Reading image file '{INPUT_IMAGE}'...")
    try:
        with open(INPUT_IMAGE, 'rb') as f:
            image_data = f.read()
    except FileNotFoundError:
        print(f"[Error] File '{INPUT_IMAGE}' not found!")
        return

    print(f"[*] Connecting to Worker Node at {WORKER_IP}:{PORT}...")
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
        s.connect((WORKER_IP, PORT))
        
        s.sendall(str(len(image_data)).encode('utf-8'))
        s.recv(1024)
        
        print(f"[*] Transmitting image payload to Worker...")
        s.sendall(image_data)
        
        result_size_data = s.recv(1024)
        result_size = int(result_size_data.decode('utf-8'))
        s.sendall(b"ACK")
        
        print(f"[*] Receiving processed image from Worker...")
        processed_bytes = bytearray()
        while len(processed_bytes) < result_size:
            packet = s.recv(4096)
            if not packet: break
            processed_bytes.extend(packet)
            
        with open(OUTPUT_IMAGE, 'wb') as f:
            f.write(processed_bytes)
            
        print(f"\n[✓] Success! Processed image saved as '{OUTPUT_IMAGE}'.")

if __name__ == "__main__":
    send_image_task()
```
## 🖼️ 4. Before & After Demonstration
Here is the visual comparison of the image processing results transmitted by the Master Node (Linux), processed concurrently at the Worker Node (Windows), and returned back:


### Input Image (Original - input.jpg)

<img width="716" height="428" alt="images (2)" src="https://github.com/user-attachments/assets/b2607c1d-bb66-4bff-824b-36331234e75c" />

Original image before transmission via TCP socket.

### Output Image (Processed - output_processed.jpg)

<img width="716" height="428" alt="output_processed" src="https://github.com/user-attachments/assets/d31baae5-2f7c-492e-ae14-dac7fc603252" />

Image after pixel transformation (Grayscale & Inversion) by Worker.


## 📹 5. Video Demonstration
A full demonstration video illustrating the cross-platform execution (Linux Master and Windows Worker) and live terminal logs is published on YouTube [cite: 1].

🎬 YouTube Video Link: https://youtu.be/u2lrT3oF_dY [cite: 1]

## 📚 6. References
Coulouris, G., Dollimore, J., & Kindberg, T. (2012). Distributed Systems: Concepts and Design (5th ed.). Addison-Wesley.

Python Software Foundation. (2026). socket — Low-level networking interface. Python Documentation. https://docs.python.org/3/library/socket.html

Python Imaging Library (Pillow) Documentation. https://pillow.readthedocs.io/
