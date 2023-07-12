# Tugas_UAS_PengolahanCitraDigital
<br>
Nama             : Afra Nesya Apriyanthi<br>
Nim              : 312110614<br>
Kelas            : TI.21.C1<br>
Mata Kuliah      : Pengolahan Citra<br>
Dosen Pengampu   : Muhammad Najamuddin Dwi Miharja, S.Kom, M.Kom<br>

## Penjelasan Program
1. Impor pustaka dan modul yang diperlukan:
    - from PIL import Image, ImageTk: Digunakan untuk mengolah gambar menggunakan library PIL        (Python Imaging Library) dan menampilkan gambar di antarmuka GUI menggunakan ImageTk.
    - import numpy as np: Digunakan untuk operasi array dan manipulasi gambar menggunakan            array numpy.
    - import matplotlib.pyplot as plt: Digunakan untuk membuat histogram gambar.
    - import tkinter as tk: Digunakan untuk membuat antarmuka GUI.
    - from tkinter import filedialog: Digunakan untuk membuka jendela dialog pemilihan file          gambar.

2. Mendefinisikan fungsi median_filter(image, kernel_size): Fungsi ini menerima input berupa      objek gambar dan ukuran kernel filter median. Fungsi ini akan menerapkan filter median pada    gambar dengan memindai setiap piksel dalam gambar dan menggantinya dengan nilai median dari    piksel-piksel di sekitarnya. Fungsi ini mengembalikan gambar hasil filter median.
   ```py
    def median_filter(image, kernel_size):
    image_array = np.array(image)
    filtered_image = image_array.copy()

    for i in range(image.height):
        for j in range(image.width):
            r_vals = []
            g_vals = []
            b_vals = []

            for x in range(max(0, i - kernel_size), min(i + kernel_size + 1, image.height)):
                for y in range(max(0, j - kernel_size), min(j + kernel_size + 1, image.width)):
                    r, g, b = image_array[x, y]
                    r_vals.append(r)
                    g_vals.append(g)
                    b_vals.append(b)

            r_median = int(np.median(r_vals))
            g_median = int(np.median(g_vals))
            b_median = int(np.median(b_vals))

            filtered_image[i, j] = [r_median, g_median, b_median]

    return Image.fromarray(filtered_image.astype('uint8'))
   ```
3. Mendefinisikan fungsi mse(original_image, filtered_image): Fungsi ini menerima dua objek gambar, yaitu gambar asli dan gambar hasil filter median. Fungsi ini menghitung nilai MSE antara dua gambar dengan membandingkan piksel-piksel pada posisi yang sama. Fungsi ini mengembalikan nilai MSE.
   ```py
    def mse(original_image, filtered_image):
    original_pixels = np.array(original_image)
    filtered_pixels = np.array(filtered_image)
    error = np.mean((original_pixels - filtered_pixels) ** 2)
    return error
   ```
4. Mendefinisikan fungsi psnr(original_image, filtered_image): Fungsi ini menerima dua objek gambar, yaitu gambar asli dan gambar hasil filter median. Fungsi ini menghitung nilai PSNR berdasarkan nilai MSE antara dua gambar. Fungsi ini mengembalikan nilai PSNR.
    ```py
    def psnr(original_image, filtered_image):
    max_intensity = 255
    mse_val = mse(original_image, filtered_image)
    psnr_val = 10 * np.log10((max_intensity ** 2) / mse_val)
    return psnr_val
   ```
5. Mendefinisikan fungsi select_image() dan load_image(file_path): Fungsi ini dipanggil saat tombol "Select Image" di klik dan menerima path yang dipilih. Fungsi ini melakukan langkah-langkah berikut:
    ```py
    def select_image():
    file_path = filedialog.askopenfilename(filetypes=[("Image files", "*.jpg;*.jpeg;*.png")])
    if file_path:
        load_image(file_path)

    def load_image(file_path):
    original_image = Image.open(file_path)
    filtered_image = median_filter(original_image, 3)
    mse_val = mse(original_image, filtered_image)
    psnr_val = psnr(original_image, filtered_image)

    # Clear previous images, texts, and histograms
    canvas.delete("all")

    # Get image sizes
    original_width, original_height = original_image.size

    # Set canvas size to accommodate elements
    canvas.config(width=original_width * 3 + 30, height=original_height * 2 + 60, bg="turquoise")

    # Display original image
    original_photo = ImageTk.PhotoImage(original_image)
    canvas.create_image(70, 80, anchor="nw", image=original_photo)
    canvas.image = original_photo  # Store reference

    # Display filtered image
    filtered_photo = ImageTk.PhotoImage(filtered_image)
    canvas.create_image(original_width + 130, 80, anchor="nw", image=filtered_photo)
    canvas.image2 = filtered_photo  # Store reference
    
     # Display Text
    canvas.create_text(35, 30, anchor="sw", text="MENCARI NILAI MSE DAN PSNR CITRA 300X300 DENGAN MENGGUNAKAN METODE MEDIAN FILTER", fill="black", font=("tahoma", 14))
    canvas.create_text(70, 70, anchor="sw", text="Original Image =", fill="black", font=("tahoma", 12))
    canvas.create_text(original_width + 130, 70, anchor="sw", text="Filtered Image = Median Filter", fill="black", font=("tahoma", 12))
    
    # Making Box
    box_width = 150
    box_height = 70
    box_x = original_width + 130
    box_y = original_height + 115
    canvas.create_rectangle(box_x, box_y, box_x + box_width, box_y + box_height, fill="turquoise")

    #  Display text in the box
    mse_text = f"MSE  = {mse_val:.6f}"
    psnr_text = f"PSNR = {psnr_val:.6f}"
    canvas.create_text(box_x + 10, box_y + 15, anchor="nw", text=mse_text, fill="black")
    canvas.create_text(box_x + 10, box_y + 40, anchor="nw", text=psnr_text, fill="black")

    # Display histogram for original image
    original_hist = get_histogram(original_image)
    plot_histogram(original_hist, 2, 70, original_height + 100)
    
    # Update the canvas view
    canvas.update()
     ```
6. Mendefinisikan fungsi get_histogram(image) : Fungsi ini menerima objek gambar dan menghitung histogram gambar menggunakan numpy. Fungsi ini mengembalikan array histogram.
    ```py
    def get_histogram(image):
    image_array = np.array(image)
    hist, _ = np.histogram(image_array.flatten(), bins=256, range=[0, 256])
    return hist
    ```
7. Mendefinisikan Fungsi plot_histogram(hist, channel, x, y): Fungsi ini menerima array histogram, channel (saluran) gambar, dan koordinat x dan y untuk menampilkan histogram pada antarmuka GUI. Fungsi ini menggunakan matplotlib untuk membuat histogram, menyimpannya sebagai file sementara, dan menampilkan gambar histogram pada antarmuka GUI menggunakan tkinter.
    ```py
    def plot_histogram(hist, channel, x, y):
    fig, ax = plt.subplots(figsize=(4, 3), dpi=80)
    ax.bar(range(256), hist, color='black')
    ax.set_xlabel('Pixel Value')
    ax.set_ylabel('Frequency')
    ax.set_title('Histogram Original Photo')
    ax.set_xlim([0, 256])
    ax.set_ylim([0, np.max(hist) * 1.1])

    # Save the histogram plot to a temporary image file without transparency
    plt.savefig('histogram.png', transparent=True, bbox_inches='tight', pad_inches=0)
    plt.close(fig)

    # Load the temporary image file and display it on the canvas
    histogram_image = Image.open('histogram.png')
    histogram_photo = ImageTk.PhotoImage(histogram_image)
    canvas.create_image(x, y, anchor="nw", image=histogram_photo)
    canvas.image3 = histogram_photo  # Store reference
    ```
8. Membuat jendela utama dengan judul "UAS AFRA NESYA 312110614".
    ```py
    root = tk.Tk()
    root.title("UAS AFRA NESYA 312110614")
    root.geometry("900x700")
    root.configure(bg="ghost white")
    ```
9. Membuat tombol "Select Image" yang memanggil fungsi select_image().
    ```py
    select_button = tk.Button(root, text="Select Image", command=select_image, bg="ghost            white")
    select_button.pack()
    ```
10. Mengatur ukuran jendela menggunakan window.geometry.
    ```py
      root.geometry("900x700")
    ```
11. Membuat area canvas untuk menampilkan gambar, teks, dan histogram.
    ```py
    canvas = tk.Canvas(root, width=900, height=700, bg="turquoise")
    canvas.pack()
    canvas.create_text(450, 20, anchor="n", text="MENCARI NILAI MSE DAN PSNR CITRA 300X300             DENGAN MENGGUNAKAN METODE MEDIAN FILTER",fill="black", font=("tahoma", 14))
    canvas.create_text(70, 80, anchor="w", text="Original Image =", fill="black", font=                ("tahoma", 12))
    canvas.create_text(450, 80, anchor="w", text="Filtered Image = Median Filter",
        fill="black", font=("tahoma", 12))
    original_box = canvas.create_rectangle(70, 100, 370, 400, width=2, outline="black")
    filtered_box = canvas.create_rectangle(450, 100, 750, 400, width=2, outline="black")
    mse_psnr_box = canvas.create_rectangle(450, 425, 675, 500, width=2, outline="black")
        canvas.create_text(465, 460, anchor="w", text= "Hasil MSE dan PSNR", fill="black",
        font=("tahoma", 12))
    ```
12. Menjalankan event loop Tkinter menggunakan window.mainloop(). Event loop ini akan menjaga jendela GUI tetap aktif dan menangani interaksi pengguna seperti menekan tombol.
    ```py
      root.mainloop()
    ```

### Berikut adalah hasil dari program diatas
1. Pertama kita akan disuguhi GUI untuk pilih Gambar
   ![hasill](screenshot/hasil.png)
2. Kemudian Setelah Memilih Gambar, kita akan melihat GUI tentang Perbandingan Original Image dan Median filter serta mse dan psnr nya, seperti berikut
   ![hasill](screenshot/hasil1.png)
