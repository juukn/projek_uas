1. Import Library
python
Copy code
from kivy.app import App
from kivy.uix.boxlayout import BoxLayout
from kivy.uix.textinput import TextInput
from kivy.uix.button import Button
from kivy.uix.label import Label
import requests
kivy.app.App: Digunakan untuk membuat aplikasi utama dengan App sebagai kelas dasar. Aplikasi ini mengontrol bagaimana tampilan dan interaksi aplikasi akan bekerja.
kivy.uix.boxlayout.BoxLayout: Merupakan widget untuk layout yang menyusun widget lain dalam orientasi vertikal atau horizontal. Di sini, digunakan untuk menyusun elemen-elemen GUI dalam tampilan vertikal.
kivy.uix.textinput.TextInput: Widget untuk menerima input teks dari pengguna. Dalam hal ini, digunakan untuk memasukkan nama kota.
kivy.uix.button.Button: Widget untuk tombol. Digunakan untuk memicu aksi saat diklik, dalam hal ini untuk memeriksa cuaca.
kivy.uix.label.Label: Widget untuk menampilkan teks, digunakan untuk menampilkan hasil dari pengecekan cuaca.
requests: Library eksternal untuk mengirim HTTP request dan menerima respon. Digunakan untuk mengambil data cuaca dari API OpenWeatherMap.
2. Kelas WeatherApp
python
Copy code
class WeatherApp(App):
    def build(self):
        self.layout = BoxLayout(orientation="vertical", padding=10, spacing=10)
WeatherApp adalah subclass dari kelas App yang mengatur struktur aplikasi.
build() adalah metode utama yang dipanggil ketika aplikasi dimulai. Di dalamnya, kita mendefinisikan elemen-elemen GUI yang akan ditampilkan.
3. Membuat Input Kota
python
Copy code
        self.city_input = TextInput(hint_text="Masukkan nama kota", font_size=20, size_hint=(1, 0.2))
        self.layout.add_widget(self.city_input)
TextInput digunakan untuk membuat kotak input teks tempat pengguna dapat mengetik nama kota.
hint_text="Masukkan nama kota": Menampilkan teks petunjuk di dalam kotak input ketika tidak ada teks.
font_size=20: Mengatur ukuran teks pada input.
size_hint=(1, 0.2): Menentukan ukuran relatif widget. 1 berarti lebar widget akan mengisi seluruh lebar kontainer (BoxLayout), sedangkan 0.2 berarti tinggi widget adalah 20% dari tinggi layout.
self.layout.add_widget(self.city_input): Menambahkan widget TextInput ke layout.
4. Membuat Tombol Cek Cuaca
python
Copy code
        self.check_button = Button(text="Cek Cuaca", font_size=20, size_hint=(1, 0.2))
        self.check_button.bind(on_press=self.get_weather)
        self.layout.add_widget(self.check_button)
Button: Membuat tombol dengan teks "Cek Cuaca".
font_size=20: Mengatur ukuran teks pada tombol.
size_hint=(1, 0.2): Menentukan ukuran tombol, 100% lebar dan 20% tinggi layout.
self.check_button.bind(on_press=self.get_weather): Mengikat (bind) event on_press tombol ke metode get_weather. Artinya, ketika tombol diklik, metode get_weather akan dipanggil.
self.layout.add_widget(self.check_button): Menambahkan tombol ke layout.
5. Menambahkan Label untuk Hasil Cuaca
python
Copy code
        self.result_label = Label(text="", font_size=18, size_hint=(1, 0.6))
        self.layout.add_widget(self.result_label)
Label: Digunakan untuk menampilkan hasil cuaca.
text="": Pada awalnya label ini kosong.
font_size=18: Mengatur ukuran font label.
size_hint=(1, 0.6): Menentukan ukuran label, 100% lebar dan 60% tinggi layout.
self.layout.add_widget(self.result_label): Menambahkan label ke layout.
6. Fungsi get_weather
python
Copy code
    def get_weather(self, instance):
        city = self.city_input.text
        if not city:
            self.result_label.text = "Masukkan nama kota terlebih dahulu!"
            return
get_weather(self, instance): Fungsi yang dipanggil ketika tombol "Cek Cuaca" ditekan.
city = self.city_input.text: Mengambil teks yang dimasukkan oleh pengguna pada TextInput dan menyimpannya dalam variabel city.
if not city: Memeriksa apakah input pengguna kosong. Jika kosong, akan menampilkan pesan untuk memasukkan nama kota.
self.result_label.text = "Masukkan nama kota terlebih dahulu!": Mengubah teks pada label untuk memberikan feedback kepada pengguna.
7. Mengambil Data Cuaca dari API
python
Copy code
        api_key = "5fcecfa19065b9593b87735917c5c2ea"
        url = f"https://api.openweathermap.org/data/2.5/weather?q={city}&appid={api_key}&units=metric"
api_key: Kunci API yang digunakan untuk autentikasi ke OpenWeatherMap.
url: URL untuk mengakses data cuaca berdasarkan kota. Menggunakan parameter:
q={city}: Nama kota yang dimasukkan oleh pengguna.
appid={api_key}: API key untuk autentikasi.
units=metric: Mengambil data suhu dalam satuan Celsius.
8. Menangani Respon API
python
Copy code
        try:
            response = requests.get(url)
            data = response.json()
response = requests.get(url): Mengirimkan request GET ke URL dan mendapatkan respons dari server.
data = response.json(): Mengonversi respons JSON menjadi objek Python (dictionary).
python
Copy code
            if data["cod"] == "404":
                self.result_label.text = "Kota tidak ditemukan!"
Mengecek kode respons dari API. Jika kode adalah 404, artinya kota tidak ditemukan, dan menampilkan pesan kesalahan.
python
Copy code
            else:
                temp = data["main"]["temp"]
                weather = data["weather"][0]["description"]
                humidity = data["main"]["humidity"]
                self.result_label.text = f"Cuaca: {weather}\nSuhu: {temp}°C\nKelembapan: {humidity}%"
Jika data ditemukan, kita mengakses informasi cuaca:
temp: Suhu kota dalam Celsius.
weather: Deskripsi cuaca (misalnya, "cerah", "hujan").
humidity: Kelembapan udara dalam persen.
Menampilkan hasil cuaca pada label.
9. Menangani Error
python
Copy code
        except Exception as e:
            self.result_label.text = f"Terjadi kesalahan: {e}"
Jika terjadi kesalahan (misalnya, masalah jaringan atau API tidak tersedia), error akan ditangkap dan pesan error akan ditampilkan di label.
10. Menjalankan Aplikasi
python
Copy code
if __name__ == "__main__":
    WeatherApp().run()
Jika script dijalankan langsung, maka aplikasi WeatherApp akan dimulai dengan memanggil metode run() dari App untuk menjalankan aplikasi GUI.
Penjelasan Umum
Aplikasi ini adalah aplikasi desktop berbasis Kivy yang mengambil data cuaca dari API OpenWeatherMap. Pengguna memasukkan nama kota pada kolom input, dan ketika tombol "Cek Cuaca" ditekan, aplikasi akan menampilkan informasi cuaca seperti suhu, deskripsi cuaca, dan kelembapan di kota yang dimaksud. Jika input tidak valid atau API gagal, aplikasi akan memberikan feedback yang sesuai kepada pengguna.
