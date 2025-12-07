# Final Project Robotics ITB

Dokumentasi ini merangkum cara membangun, menjalankan, dan memperluas paket ROS 2 di repositori **Final-Project-Robotics-ITB**. Seluruh instruksi ditulis dalam bahasa Indonesia.

## Ringkasan Proyek
Repositori ini berisi empat paket utama berbasis ROS 2:

- **yolobot_description**: Menyediakan model URDF `robot/yolobot.urdf` serta launch file untuk memuat deskripsi robot dan memanggil `robot_state_publisher`.
- **yolobot_gazebo**: Menyediakan dunia simulasi Gazebo (`worlds/yolo_test.world`) dan launch file untuk menjalankan Gazebo sekaligus memanggil paket lain yang dibutuhkan.
- **yolobot_control**: Node pengontrol sederhana yang membaca joystick (`sensor_msgs/Joy`) dan menerbitkan ke `/yolobot/cmd_vel` melalui executor multithread.
- **yolobot_recognition**: Integrasi YOLOv5 untuk mengenali objek dari topik kamera (`rgb_cam/image_raw`) menggunakan model bawaan `yolov5s.pt`.

## Prasyarat
- ROS 2 (mis. Humble/Foxy) dengan dukungan `colcon` dan `ament_cmake`.
- Gazebo dan paket `gazebo_ros`.
- Python 3 beserta dependensi YOLOv5 (lihat `src/yolobot_recognition/scripts/requirements.txt`).
- Paket joystick ROS 2 (`joy`) bila ingin mengontrol robot dengan gamepad.

## Instalasi
1. **Kloning repositori** (bila belum):
   ```bash
   git clone <url-repo> Final-Project-Robotics-ITB
   cd Final-Project-Robotics-ITB
   ```
2. **Bangun workspace**:
   ```bash
   colcon build
   ```
3. **Muat environment hasil build**:
   ```bash
   source install/setup.bash
   ```

## Menjalankan Simulasi dan Kontrol
1. **Mulai Gazebo beserta robot dan kontrol dasar**:
   ```bash
   ros2 launch yolobot_gazebo yolobot_launch.py
   ```
   Launch ini akan:
   - Menjalankan dunia `worlds/yolo_test.world` dari paket `yolobot_gazebo`.
   - Memanggil `spawn_yolobot_launch.launch.py` untuk memunculkan robot dari URDF `yolobot_description` dan menjalankan `robot_state_publisher`.
   - Menjalankan node joystick `joy` serta node kontrol `yolobot_control` yang menerjemahkan input joystick menjadi perintah kecepatan.

2. **Menyesuaikan simulasi**: Ubah atau ganti file dunia di `src/yolobot_gazebo/worlds/` lalu sesuaikan argumen `world` pada launch file bila diperlukan.

## Penggunaan Pengenalan Objek (YOLOv5)
1. **Pasang dependensi** (sekali saja di dalam environment Python):
   ```bash
   pip install -r src/yolobot_recognition/scripts/requirements.txt
   ```
2. **Jalankan node deteksi** (setelah simulasi berjalan dan topik kamera tersedia):
   ```bash
   ros2 run yolobot_recognition ros_recognition_yolo.py
   ```
   Node ini akan:
   - Berlangganan citra dari `rgb_cam/image_raw` menggunakan `CvBridge`.
   - Memuat model `yolov5s.pt`, menjalankan inferensi, dan menampilkan jendela OpenCV berisi bounding box hasil deteksi.

3. **Pelatihan atau pengujian lanjutan**: Skrip tambahan seperti `train.py`, `detect.py`, dan `detect_video.py` tersedia di `src/yolobot_recognition/scripts/` untuk eksperimen di luar ROS 2.

## Struktur Direktori Singkat
- `src/yolobot_description/`: Model URDF dan launch untuk memunculkan robot.
- `src/yolobot_gazebo/`: Launch untuk Gazebo dan dunia simulasi.
- `src/yolobot_control/`: Script kontrol joystick dan launch terkait.
- `src/yolobot_recognition/`: Integrasi dan skrip YOLOv5.
- `build/` & `install/`: Hasil `colcon build` (abaikan saat membaca kode sumber).

## Tips Pengembangan
- Tambahkan paket baru di bawah `src/` dan daftarkan dependensi di `package.xml` serta `CMakeLists.txt` masing-masing.
- Gunakan `colcon build --packages-select <nama_paket>` untuk mempercepat waktu kompilasi saat mengembangkan paket tertentu saja.
- Periksa topik yang tersedia dengan `ros2 topic list` dan echo data dengan `ros2 topic echo <nama_topik>` ketika melakukan debugging.

