# JAWABAN LATIHAN UTS MOBILE PROGRAMMING (GANJIL 2026)

Topik UTS: Android Software Architecture, Android OOP, Layout & Activity Lifecycle, Intents & Activity.

| No | Jenis | Bobot | Topik |
|---|---|---|---|
| 1 | Essay | 20 | MVVM dan lapisannya |
| 2 | Essay | 20 | Hubungan OOP dengan MVVM |
| 3 | Study Case | 20 | Event Listener untuk event Halloween |
| 4 | Study Case | 40 | 2 Activity + Intent (Kotlin + XML) |

---

## No. 1: MVVM di Android Development

**MVVM (Model-View-ViewModel)** adalah pola arsitektur yang memisahkan aplikasi menjadi tiga lapisan: **Model** (data), **View** (tampilan), dan **ViewModel** (penghubung yang menyimpan state dan logika tampilan). Tujuannya agar kode UI tidak bercampur dengan logika dan data, sehingga lebih mudah dirawat, dites, dan tidak kehilangan data saat layar diputar.

```
View (Activity/Fragment + XML)
   │  ▲
   │  │ observe LiveData / StateFlow
   ▼  │
ViewModel
   │  ▲
   ▼  │
Model (Repository → Room / API / SharedPreferences)
```

| Lapisan | Isi | Fungsi |
|---|---|---|
| **Model** | Data class, Repository, Room database, Retrofit API | Menyimpan dan menyediakan data, serta aturan bisnis. Tidak tahu apa-apa soal UI. |
| **ViewModel** | Class turunan `ViewModel`, berisi `LiveData`/`StateFlow` | Mengambil data dari Model, mengolahnya, dan menyimpan state UI. **Tetap hidup saat rotasi layar** (configuration change). Tidak menyimpan referensi ke View/Activity. |
| **View** | Activity, Fragment, layout XML | Menampilkan data dan meneruskan aksi pengguna (klik, input) ke ViewModel. Meng-*observe* data dari ViewModel dan otomatis ter-update saat data berubah. |

Aturan alur:
- **View → ViewModel:** View memanggil fungsi ViewModel, misalnya `viewModel.tambahKoin()`.
- **ViewModel → View:** ViewModel tidak memanggil View secara langsung. View mengamati (observe) `LiveData`.
- **ViewModel → Model:** ViewModel meminta data ke Repository.

Contoh singkat:

```kotlin
// Model
data class Player(val name: String, val coins: Int)

// ViewModel
class PlayerViewModel : ViewModel() {
    private val _coins = MutableLiveData(0)
    val coins: LiveData<Int> = _coins

    fun tambahKoin(jumlah: Int) {
        _coins.value = (_coins.value ?: 0) + jumlah
    }
}

// View
class MainActivity : AppCompatActivity() {
    private val vm: PlayerViewModel by viewModels()

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        val tv = findViewById<TextView>(R.id.tvCoins)
        vm.coins.observe(this) { tv.text = "Koin: $it" }
        findViewById<Button>(R.id.btnMisi).setOnClickListener { vm.tambahKoin(10) }
    }
}
```

Kelebihan MVVM: pemisahan tanggung jawab (*separation of concerns*), data tidak hilang saat rotasi, ViewModel bisa di-unit test tanpa emulator, dan UI otomatis sinkron dengan data.

---

## No. 2: Kaitan OOP dengan MVVM

MVVM dibangun di atas konsep OOP. Setiap lapisan adalah **class** dengan tanggung jawab sendiri, dan empat pilar OOP dipakai untuk memisahkan serta menghubungkan lapisan tersebut.

| Pilar OOP | Penerapan di MVVM | Contoh |
|---|---|---|
| **Class & Object** | Model, ViewModel, dan View masing-masing adalah class. Saat aplikasi berjalan, dibuat object-nya. | `data class Player`, `class PlayerViewModel`, `class MainActivity` |
| **Encapsulation** | ViewModel menyembunyikan data yang bisa diubah (`private MutableLiveData`) dan hanya membuka versi baca (`LiveData`). View tidak bisa mengubah data sembarangan, harus lewat fungsi ViewModel. | `private val _coins` + `val coins: LiveData<Int>` |
| **Inheritance** | ViewModel mewarisi class `ViewModel` (mendapat lifecycle yang tahan rotasi). View mewarisi `AppCompatActivity`. | `class PlayerViewModel : ViewModel()` |
| **Polymorphism** | Method bawaan di-override sesuai kebutuhan. Repository berupa interface bisa punya implementasi berbeda (data lokal, API, data palsu untuk testing). | `override fun onCreate()`, `override fun onCleared()` |
| **Abstraction** | ViewModel hanya tahu interface Repository, tidak peduli data berasal dari Room atau API. View hanya tahu "ada data koin", tidak tahu cara data diambil. | `interface PlayerRepository { fun getCoins(): Int }` |

```kotlin
interface PlayerRepository {                                   // Abstraction
    fun getCoins(): Int
}

class LocalPlayerRepository : PlayerRepository {               // Polymorphism
    override fun getCoins() = 100
}

class PlayerViewModel(private val repo: PlayerRepository) : ViewModel() {   // Inheritance
    private val _coins = MutableLiveData(repo.getCoins())     // Encapsulation
    val coins: LiveData<Int> = _coins
}
```

Kesimpulan: OOP memberi alatnya (class, enkapsulasi, pewarisan, polimorfisme, abstraksi), MVVM memberi aturan pembagiannya. Hasilnya kode yang modular (*loose coupling*), satu class satu tanggung jawab (*Single Responsibility*), mudah diganti bagiannya, dan mudah dites.

---

## No. 3: Event Listener untuk Event Halloween

Pemetaan fitur ke listener:

| Fitur di soal | Komponen Android | Event Listener | Respons aplikasi |
|---|---|---|---|
| Membuka peti misterius | `ImageButton` / `ImageView` peti | **View.OnClickListener** | Peti terbuka, tampilkan hadiah |
| Koin labu bertambah setelah misi selesai | `Button` "Selesaikan Misi" + `TextView` koin | **View.OnClickListener** | Jumlah koin di `TextView` diperbarui |
| Mengatur intensitas efek suara | `SeekBar` di menu pengaturan event | **SeekBar.OnSeekBarChangeListener** | Volume efek suara berubah sesuai posisi slider |
| Memilih kostum, tampilan karakter langsung berubah sebelum konfirmasi | `RadioGroup` berisi `RadioButton` kostum (atau `Spinner`) | **RadioGroup.OnCheckedChangeListener** (atau **AdapterView.OnItemSelectedListener** untuk Spinner) | Gambar karakter (`ImageView`) langsung berganti saat pilihan berubah |
| Mengetuk objek tersembunyi, muncul animasi hantu | `ImageView` objek tersembunyi / area permainan | **View.OnTouchListener** | Saat `ACTION_DOWN`, animasi hantu diputar di titik sentuh |

Soal meminta **minimal dua**. Empat pilihan di bawah sudah mencakup semua fitur.

### a. View.OnClickListener: buka peti dan selesaikan misi

**Alasan:** membuka peti dan menyelesaikan misi adalah aksi **sekali ketuk** pada tombol/gambar dengan hasil **diskrit** (peti terbuka, koin bertambah). OnClickListener adalah listener paling tepat untuk aksi tap tunggal pada `Button` atau `ImageButton`.

```kotlin
var koin = 0
btnSelesaiMisi.setOnClickListener {
    koin += 50
    tvKoin.text = "Koin Labu: $koin"
}

imgPeti.setOnClickListener {
    imgPeti.setImageResource(R.drawable.peti_terbuka)
}
```

### b. SeekBar.OnSeekBarChangeListener: intensitas efek suara

**Alasan:** intensitas suara adalah **nilai bertingkat/kontinu** (misalnya 0–100), bukan sekadar on/off. `SeekBar` cocok untuk memilih nilai dalam rentang, dan listener-nya memberi nilai `progress` setiap kali slider digeser, sehingga volume bisa langsung disesuaikan.

```kotlin
seekBarSuara.setOnSeekBarChangeListener(object : SeekBar.OnSeekBarChangeListener {
    override fun onProgressChanged(sb: SeekBar?, progress: Int, fromUser: Boolean) {
        val volume = progress / 100f
        mediaPlayer.setVolume(volume, volume)
        tvVolume.text = "Volume: $progress%"
    }
    override fun onStartTrackingTouch(sb: SeekBar?) {}
    override fun onStopTrackingTouch(sb: SeekBar?) {}
})
```

### c. RadioGroup.OnCheckedChangeListener: pilih kostum

**Alasan:** pemain memilih **satu dari beberapa kostum** (pilihan tunggal). Listener ini terpanggil **setiap kali pilihan berubah**, sehingga gambar karakter bisa langsung diperbarui sebagai *preview* sebelum pemain menekan tombol konfirmasi. OnClickListener kurang tepat di sini karena yang penting adalah perubahan pilihan, bukan klik.

```kotlin
rgKostum.setOnCheckedChangeListener { _, checkedId ->
    when (checkedId) {
        R.id.rbVampir  -> imgKarakter.setImageResource(R.drawable.vampir)
        R.id.rbPenyihir -> imgKarakter.setImageResource(R.drawable.penyihir)
        R.id.rbZombie  -> imgKarakter.setImageResource(R.drawable.zombie)
    }
}
```

### d. View.OnTouchListener: ketuk objek tersembunyi, animasi hantu

**Alasan:** di dalam game, aplikasi perlu tahu **kapan dan di koordinat mana** layar disentuh agar animasi hantu muncul tepat di objek yang diketuk. OnTouchListener memberi `MotionEvent` berisi jenis sentuhan (`ACTION_DOWN`, `ACTION_UP`) dan posisi `x`, `y`, yang tidak tersedia di OnClickListener.

```kotlin
imgObjekRahasia.setOnTouchListener { v, event ->
    if (event.action == MotionEvent.ACTION_DOWN) {
        imgHantu.x = v.x + event.x
        imgHantu.y = v.y + event.y
        imgHantu.visibility = View.VISIBLE
        imgHantu.startAnimation(AnimationUtils.loadAnimation(this, R.anim.hantu_muncul))
    }
    true
}
```

> Catatan: efek suara khusus saat **memasuki area tertentu** biasanya dipicu oleh logika game (posisi karakter masuk ke area), bukan oleh listener komponen UI. Jika harus dijawab dengan listener dari daftar, gunakan **View.OnTouchListener** (atau **View.OnDragListener** jika karakter digeser ke area tersebut) lalu cek apakah koordinat berada di dalam area.

---

## No. 4: Rancangan Activity Event Halloween

### a. Activity yang dibutuhkan

| Activity | Fungsi |
|---|---|
| **HalloweenEventActivity** (halaman utama event) | Menampilkan informasi event (judul, periode), jumlah koin Halloween pemain, dan daftar misi. Pemain memilih misi dari daftar untuk melihat detailnya. |
| **MissionDetailActivity** (halaman detail misi) | Menampilkan detail misi yang dipilih (judul, deskripsi, hadiah koin) dan tombol untuk mengikuti/menyelesaikan misi. Setelah misi selesai, hadiah koin dikirim kembali ke halaman event. |

Alur:

```
HalloweenEventActivity --(pilih misi, Intent + putExtra)--> MissionDetailActivity
          ▲                                                        │
          └────────(setResult: REWARD, lalu finish())──────────────┘
```

### b. Data di setiap Activity

| Activity | Data | Tipe | Sumber |
|---|---|---|---|
| HalloweenEventActivity | Judul event | `String` | Hardcode / resource |
| | Periode event (2 minggu) | `String` | Hardcode |
| | Jumlah koin pemain | `Int` | Variabel, diperbarui dari hasil misi |
| | Daftar misi (judul, deskripsi, hadiah) | `List<Mission>` | Data class `Mission` |
| MissionDetailActivity | Judul misi | `String` | Intent extra `TITLE` |
| | Deskripsi misi | `String` | Intent extra `DESC` |
| | Hadiah koin | `Int` | Intent extra `REWARD` |
| | Hasil misi (koin yang didapat) | `Int` | Dikirim balik lewat `setResult()` |

### c. Implementasi Kotlin + XML

#### `Mission.kt`

```kotlin
data class Mission(val title: String, val desc: String, val reward: Int)
```

#### `res/layout/activity_halloween_event.xml`

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="16dp"
    android:background="#1A1A1A">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Halloween: Berburu Labu Ajaib"
        android:textSize="22sp"
        android:textStyle="bold"
        android:textColor="#FF8C00" />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Periode: 20 Okt - 3 Nov 2026"
        android:textColor="#FFFFFF" />

    <TextView
        android:id="@+id/tvCoins"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="12dp"
        android:text="Koin Halloween: 0"
        android:textSize="18sp"
        android:textColor="#FFD700" />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="16dp"
        android:text="Daftar Misi"
        android:textStyle="bold"
        android:textColor="#FFFFFF" />

    <ListView
        android:id="@+id/lvMissions"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:background="#FFFFFF" />
</LinearLayout>
```

#### `HalloweenEventActivity.kt`

```kotlin
class HalloweenEventActivity : AppCompatActivity() {

    private var coins = 100
    private lateinit var tvCoins: TextView

    private val missions = listOf(
        Mission("Berburu Labu di Taman", "Kumpulkan 5 labu ajaib di taman kota.", 50),
        Mission("Rumah Berhantu", "Temukan 3 peti misterius di rumah tua.", 80),
        Mission("Pemakaman Tengah Malam", "Kalahkan 10 zombie sebelum jam 12.", 120)
    )

    // Menerima hasil (koin) dari MissionDetailActivity
    private val missionLauncher =
        registerForActivityResult(ActivityResultContracts.StartActivityForResult()) { result ->
            if (result.resultCode == RESULT_OK) {
                val reward = result.data?.getIntExtra("REWARD", 0) ?: 0
                coins += reward
                tvCoins.text = "Koin Halloween: $coins"
            }
        }

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_halloween_event)

        // Kembalikan koin jika Activity dibuat ulang (misalnya rotasi layar)
        coins = savedInstanceState?.getInt("COINS") ?: coins

        tvCoins = findViewById(R.id.tvCoins)
        tvCoins.text = "Koin Halloween: $coins"

        val lvMissions = findViewById<ListView>(R.id.lvMissions)
        lvMissions.adapter = ArrayAdapter(
            this, android.R.layout.simple_list_item_1, missions.map { it.title }
        )

        // Pindah halaman saat misi dipilih
        lvMissions.setOnItemClickListener { _, _, position, _ ->
            val mission = missions[position]
            val intent = Intent(this, MissionDetailActivity::class.java)
            intent.putExtra("TITLE", mission.title)
            intent.putExtra("DESC", mission.desc)
            intent.putExtra("REWARD", mission.reward)
            missionLauncher.launch(intent)
        }
    }

    override fun onSaveInstanceState(outState: Bundle) {
        super.onSaveInstanceState(outState)
        outState.putInt("COINS", coins)
    }
}
```

#### `res/layout/activity_mission_detail.xml`

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="16dp"
    android:background="#1A1A1A">

    <TextView
        android:id="@+id/tvTitle"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textSize="22sp"
        android:textStyle="bold"
        android:textColor="#FF8C00" />

    <TextView
        android:id="@+id/tvDesc"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="8dp"
        android:textColor="#FFFFFF" />

    <TextView
        android:id="@+id/tvReward"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="8dp"
        android:textColor="#FFD700" />

    <Button
        android:id="@+id/btnComplete"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="24dp"
        android:text="Selesaikan Misi" />

    <Button
        android:id="@+id/btnBack"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Kembali" />
</LinearLayout>
```

#### `MissionDetailActivity.kt`

```kotlin
class MissionDetailActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_mission_detail)

        // Ambil data dari Intent
        val title = intent.getStringExtra("TITLE")
        val desc = intent.getStringExtra("DESC")
        val reward = intent.getIntExtra("REWARD", 0)

        findViewById<TextView>(R.id.tvTitle).text = title
        findViewById<TextView>(R.id.tvDesc).text = desc
        findViewById<TextView>(R.id.tvReward).text = "Hadiah: $reward koin"

        // Selesaikan misi: kirim koin kembali ke halaman event
        findViewById<Button>(R.id.btnComplete).setOnClickListener {
            val result = Intent()
            result.putExtra("REWARD", reward)
            setResult(RESULT_OK, result)
            Toast.makeText(this, "Misi selesai! +$reward koin", Toast.LENGTH_SHORT).show()
            finish()
        }

        // Kembali tanpa hadiah
        findViewById<Button>(R.id.btnBack).setOnClickListener {
            setResult(RESULT_CANCELED)
            finish()
        }
    }
}
```

#### `AndroidManifest.xml` (di dalam tag `<application>`)

```xml
<activity
    android:name=".HalloweenEventActivity"
    android:exported="true">
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
</activity>

<activity android:name=".MissionDetailActivity" />
```

### Penjelasan mekanisme perpindahan halaman

1. Pemain mengetuk misi di `ListView`. `OnItemClickListener` membuat **explicit Intent** ke `MissionDetailActivity` dan mengisi data misi dengan `putExtra()`.
2. Intent dijalankan dengan `missionLauncher.launch(intent)` (Activity Result API), karena halaman event butuh **hasil balik** berupa koin.
3. `MissionDetailActivity` membaca data dengan `getStringExtra()` dan `getIntExtra()`.
4. Saat tombol **Selesaikan Misi** ditekan, hadiah dikirim balik dengan `setResult(RESULT_OK, intent)`, lalu `finish()` menutup halaman.
5. Callback `registerForActivityResult` di halaman event menerima hadiah dan memperbarui jumlah koin di `TextView`.
6. Jumlah koin disimpan di `onSaveInstanceState()` supaya tidak hilang saat Activity dibuat ulang (misalnya layar diputar).

---

## Materi Pendukung: Activity Lifecycle

```
onCreate() → onStart() → onResume()  [Activity berjalan]
                              ↓
                          onPause()  ← Activity lain muncul di depan
                              ↓
                          onStop()   ← Activity tidak terlihat
                              ↓
          onRestart() ←───────┤ (kembali ke Activity)
                              ↓
                          onDestroy() ← finish() atau sistem menghapus
```

| Method | Kapan dipanggil | Biasa dipakai untuk |
|---|---|---|
| `onCreate()` | Activity pertama kali dibuat | `setContentView()`, `findViewById()`, pasang listener |
| `onStart()` | Activity mulai terlihat | Menyiapkan tampilan |
| `onResume()` | Activity di depan dan bisa diinteraksi | Melanjutkan musik/animasi game |
| `onPause()` | Activity lain menutupi sebagian | Menjeda musik/game |
| `onStop()` | Activity tidak terlihat | Menyimpan data, melepas resource berat |
| `onRestart()` | Kembali dari keadaan stop | Memuat ulang data |
| `onDestroy()` | Activity dihancurkan | Membersihkan resource |

Contoh di skenario soal: saat pindah ke `MissionDetailActivity`, halaman event menjalankan `onPause()` lalu `onStop()`. Saat kembali, halaman event menjalankan `onRestart()`, `onStart()`, `onResume()`, sedangkan `MissionDetailActivity` menjalankan `onPause()`, `onStop()`, `onDestroy()`.

**Intent:**
- **Explicit Intent:** menyebut Activity tujuan secara langsung, misalnya `Intent(this, MissionDetailActivity::class.java)`. Dipakai untuk pindah halaman di dalam aplikasi sendiri.
- **Implicit Intent:** hanya menyebut aksi, sistem memilih aplikasi yang cocok. Contoh membuka browser: `Intent(Intent.ACTION_VIEW, Uri.parse("https://..."))`.

---

## Ringkasan untuk Dihafal

- **MVVM:** Model = data, View = tampilan, ViewModel = penghubung dan penyimpan state (tahan rotasi). View meng-observe ViewModel.
- **OOP di MVVM:** encapsulation (`private MutableLiveData`), inheritance (`: ViewModel()`), polymorphism (`override`), abstraction (interface Repository).
- **Listener:** klik → `OnClickListener`; slider → `OnSeekBarChangeListener`; pilihan tunggal → `RadioGroup.OnCheckedChangeListener`; sentuhan + koordinat → `OnTouchListener`; item list → `OnItemClickListener`.
- **Pindah Activity:** `Intent(this, Tujuan::class.java)` + `putExtra()`. Untuk hasil balik: `registerForActivityResult` + `setResult()` + `finish()`.
- **Lifecycle:** onCreate → onStart → onResume → onPause → onStop → onDestroy.
