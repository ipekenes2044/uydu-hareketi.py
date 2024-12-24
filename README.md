import numpy as np
import matplotlib.pyplot as plt

# Verilen Değerler
a = 10000 * 1000  # Yarı büyük eksen (metre)
b = 8000 * 1000   # Yarı küçük eksen (metre)
T = 6 * 3600      # Dönüş periyodu (saniye)
G = 6.674 * 10**-11  # Yer çekimi sabiti (N·m²/kg²)
M = 5.972 * 10**24   # Dünya'nın kütlesi (kg)

# Dış merkezlik ve odak uzaklığı hesaplama
e = np.sqrt(1 - (b**2 / a**2))  # Dış merkezlik
c = e * a  # Odak uzaklığı

# Yörünge Denklemi
theta = np.linspace(0, 2 * np.pi, 500)
x = a * np.cos(theta)
y = b * np.sin(theta)

# Parametrik Konum Hesaplama
def uydu_konumu(t):
    M_t = 2 * np.pi * t / T  # Ortalama anomali
    E_t = M_t  # Başlangıç için yaklaşım
    for _ in range(5):
        E_t = M_t + e * np.sin(E_t)  # Kepler denklemini çözme iterasyonu
    x_t = a * np.cos(E_t) - c
    y_t = b * np.sin(E_t)
    return x_t, y_t

# Hız Hesaplama
def uydu_hizi(t):
    x_t, y_t = uydu_konumu(t)
    r = np.sqrt(x_t**2 + y_t**2)
    hız = np.sqrt(G * M * (2 / r - 1 / a))
    return hız

# Grafik Çizim
plt.figure(figsize=(8, 6))
plt.plot(x, y, label="Yörünge")
plt.scatter([c, -c], [0, 0], color="red", label="Odak Noktaları")

# Her 30 dakikada bir uydu konumları
zaman_araliklari = np.arange(0, T, 1800)
konumlar = [uydu_konumu(t) for t in zaman_araliklari]
konum_x = [k[0] for k in konumlar]
konum_y = [k[1] for k in konumlar]
plt.scatter(konum_x, konum_y, color="blue", label="Uydu Konumları (30 dk aralık)")

# Grafik Ayarları
plt.axhline(0, color='black', linewidth=0.5, linestyle='--')
plt.axvline(0, color='black', linewidth=0.5, linestyle='--')
plt.title("Uydu Yörünge Modeli")
plt.xlabel("X (metre)")
plt.ylabel("Y (metre)")
plt.legend()
plt.grid()
plt.axis('equal')
plt.show()
