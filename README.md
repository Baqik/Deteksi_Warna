import cv2
import numpy as np
from google.colab import files
import io
from matplotlib import pyplot as plt

# Fungsi untuk mendeteksi kematangan buah naga
def deteksi_kematangan_buah_naga(image_path):
    # Membaca gambar dari file
    img = cv2.imread(image_path)

    if img is None:
        print(f"Error: Gambar '{image_path}' tidak ditemukan atau tidak dapat dibaca.")
        return

    # Mengubah gambar dari BGR ke HSV (Hue, Saturation, Value)
    hsv_img = cv2.cvtColor(img, cv2.COLOR_BGR2HSV)

    # Menentukan rentang warna merah hingga merah keunguan pada HSV
    lower_red = np.array([140, 50, 50])   # Rentang bawah untuk warna merah keunguan
    upper_red = np.array([180, 255, 255]) # Rentang atas untuk warna merah keunguan
    mask = cv2.inRange(hsv_img, lower_red, upper_red)

    # Menghitung area yang terdeteksi sebagai merah keunguan
    red_area = np.sum(mask > 0)

    # Tentukan kematangan berdasarkan area warna merah keunguan
    total_area = img.shape[0] * img.shape[1]
    percentage_red = (red_area / total_area) * 100

    # Menentukan kematangan berdasarkan persentase area merah keunguan
    if percentage_red > 75:
        result = "Buah naga sangat matang"
    elif percentage_red > 50:
        result = "Buah naga matang"
    elif percentage_red > 25:
        result = "Buah naga hampir matang"
    else:
        result = "Buah naga belum matang"

    # Menampilkan hasil dan gambar yang diproses
    print(f"Hasil: {result} ({percentage_red:.2f}% area merah keunguan)")

    # Menampilkan gambar asli dan gambar hasil deteksi
    plt.figure(figsize=(10, 5))

    # Gambar Asli
    plt.subplot(1, 2, 1)
    plt.imshow(cv2.cvtColor(img, cv2.COLOR_BGR2RGB))
    plt.title("Gambar Asli")
    plt.axis('off')

    # Gambar Masker Merah Keunguan
    plt.subplot(1, 2, 2)
    plt.imshow(mask, cmap='gray')
    plt.title("Masker Merah Keunguan")
    plt.axis('off')

    plt.show()

# Fungsi untuk mengunggah file gambar
def upload_file():
    uploaded = files.upload()  # Memulai upload file
    for filename in uploaded.keys():
        print(f"File {filename} berhasil diunggah!")
        # Memproses gambar yang diunggah
        deteksi_kematangan_buah_naga(filename)

# Panggil fungsi untuk mengunggah file gambar
upload_file()
