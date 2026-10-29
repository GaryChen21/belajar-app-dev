# HAFALAN UTS MOBILE PROGRAMMING

## No. 1 (20 pt)
**Soal:** Jelaskan apa yang dimaksud dengan MVVM di dalam Android Development dan sebutkan setiap lapisan strukturnya lengkap dengan fungsi masing-masing!

**Jawaban:**
MVVM (Model-View-ViewModel) adalah pola arsitektur yang memisahkan aplikasi menjadi 3 lapisan supaya kode UI, logika, dan data tidak bercampur.

- **Model:** mengelola data (database, API, repository).
- **View:** menampilkan data ke pengguna dan menerima input (Activity, Fragment, XML).
- **ViewModel:** penghubung View dan Model. Menyimpan data untuk tampilan dan tidak hilang saat layar diputar.

Alur: **View → ViewModel → Model**, lalu data kembali ke View lewat **LiveData** (View meng-observe).

---

## No. 2 (20 pt)
**Soal:** Jelaskan apa kaitan antara konsep Object Oriented Programming dengan penerapan konsep MVVM di dalam Android Development!

**Jawaban:**
MVVM dibangun dengan OOP. Setiap lapisan (Model, View, ViewModel) adalah **class** dengan tugas masing-masing.

- **Encapsulation:** data di ViewModel dibuat `private`, View hanya bisa membaca.
- **Inheritance:** `class MyViewModel : ViewModel()` dan `class MainActivity : AppCompatActivity()`.
- **Polymorphism:** meng-`override` method, misalnya `override fun onCreate()`.
- **Abstraction:** ViewModel cukup memanggil Repository tanpa perlu tahu data diambil dari mana.

Kesimpulan: OOP membuat MVVM rapi, terpisah, dan mudah dirawat.

---

## No. 3 (20 pt)
**Soal (inti):** Event Halloween: buka peti, koin labu bertambah saat misi selesai, pilih kostum (tampilan karakter langsung berubah), atur intensitas suara, ketuk objek tersembunyi untuk memunculkan animasi hantu. Tentukan **minimal 2 Event Listener** beserta alasannya.

**Jawaban:**

| Listener | Komponen | Fitur | Alasan |
|---|---|---|---|
| **OnClickListener** | Button / ImageButton | Buka peti, selesaikan misi (koin bertambah) | Aksi sekali ketuk |
| **OnSeekBarChangeListener** | SeekBar | Atur intensitas suara | Nilainya bertingkat (0–100) dan langsung berubah saat digeser |
| **OnCheckedChangeListener** | RadioGroup | Pilih kostum | Tampilan karakter langsung berubah saat pilihan diganti |
| **OnTouchListener** | ImageView | Ketuk objek, animasi hantu muncul | Mengetahui posisi sentuhan |

```kotlin
btnMisi.setOnClickListener { koin += 10 }
```

---

## No. 4 (40 pt)
**Soal (inti):** Fitur event Halloween menampilkan info event, jumlah koin, dan daftar misi. Pemain bisa memilih misi.
a. Tentukan minimal 2 Activity dan fungsinya.
b. Tentukan data di setiap Activity.
c. Buat kode Kotlin + XML untuk 2 Activity beserta perpindahan halamannya.

**a. Activity**
- **MainActivity:** halaman event. Menampilkan info event, koin, dan tombol misi.
- **MisiActivity:** halaman detail misi yang dipilih.

**b. Data**
- MainActivity: nama event, jumlah koin, daftar misi.
- MisiActivity: nama misi (diterima dari Intent).

**c. Kode**

`activity_main.xml`
```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Event Halloween" />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Koin: 100" />

    <Button
        android:id="@+id/btnMisi"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Berburu Labu" />
</LinearLayout>
```

`MainActivity.kt`
```kotlin
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        findViewById<Button>(R.id.btnMisi).setOnClickListener {
            val i = Intent(this, MisiActivity::class.java)
            i.putExtra("misi", "Berburu Labu")
            startActivity(i)
        }
    }
}
```

`activity_misi.xml`
```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <TextView
        android:id="@+id/tvMisi"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" />
</LinearLayout>
```

`MisiActivity.kt`
```kotlin
class MisiActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_misi)

        val misi = intent.getStringExtra("misi")
        findViewById<TextView>(R.id.tvMisi).text = misi
    }
}
```

**Cara pindah halaman:** `Intent` → `putExtra()` → `startActivity()`. Di Activity tujuan, data dibaca dengan `getStringExtra()`.

---

## Kunci Hafalan
- **MVVM:** Model = data, View = tampilan, ViewModel = penghubung.
- **OOP:** Encapsulation, Inheritance, Polymorphism, Abstraction.
- **Listener:** Click, SeekBar, CheckedChange, Touch.
- **Pindah Activity:** Intent, putExtra, startActivity, getStringExtra.
- **Lifecycle:** onCreate → onStart → onResume → onPause → onStop → onDestroy.
