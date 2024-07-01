# tugasUAS
# Nama Kelompok 5
| NO |        ANGGOTA        |   NIM         |  KELAS   | MATA KULIAH           |
|----|-----------------------|---------------|----------|-----------------------|
| 1  |Abdul Gofar            | 312210504     |TI.22.B.2 | Pemrograman Mobile 2  |
| 2  |Kholid Wahyudi         | 312210345     |TI.22.B.2 | Pemrograman Mobile 2  |
| 3  |Febri Tri Arie Sakti   | 312210542     |TI.22.B.2 | Pemrograman Mobile 2  |

# Deskripsi
## Penjelasan Aplikasi
KasAppAndroid adalah aplikasi Android untuk manajemen kas yang memungkinkan pengguna untuk mencatat pemasukan dan pengeluaran serta melihat laporan keuangan. <br>
Aplikasi ini dirancang untuk membantu individu atau bisnis kecil dalam mengelola keuangan mereka secara efektif.<br>
<br>
## Fitur Utama
Tambah Transaksi: Pengguna dapat menambahkan transaksi baru dengan memasukkan detail seperti jumlah, kategori, dan tanggal.<br>
Edit dan Hapus Transaksi: Pengguna dapat mengedit atau menghapus transaksi yang sudah ada.<br>
Laporan Keuangan: Pengguna dapat melihat laporan keuangan harian, mingguan, dan bulanan dalam bentuk grafik dan tabel.<br>
Pencarian Transaksi: Pengguna dapat mencari transaksi berdasarkan kata kunci atau filter tertentu.<br>
Grafik Visualisasi Keuangan: Menampilkan grafik untuk memvisualisasikan pemasukan dan pengeluaran.<br>
<br>
## Cara Menggunakan Aplikasi
Buka Aplikasi KasAppAndroid:

Setelah aplikasi berhasil diinstal, buka aplikasi KasAppAndroid di perangkat Anda.
Tambah Transaksi Baru:

Di halaman utama, klik tombol Tambah untuk menambahkan transaksi baru.
Isi detail transaksi seperti jumlah, kategori, dan tanggal, lalu klik Simpan.
Edit atau Hapus Transaksi:

Untuk mengedit atau menghapus transaksi, tekan lama pada transaksi yang ingin Anda ubah atau hapus, lalu pilih opsi Edit atau Hapus.
Lihat Laporan Keuangan:

Navigasikan ke tab Laporan untuk melihat laporan keuangan Anda.
Anda dapat melihat laporan harian, mingguan, atau bulanan dalam bentuk grafik dan tabel.
Cari Transaksi:

Gunakan fitur pencarian di halaman utama untuk mencari transaksi berdasarkan kata kunci atau filter tertentu.

# AndroidManifest.xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android">

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.VIBRATE" />

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <activity android:name=".FilterActivity" />
        <activity android:name=".EditActivity" />
        <activity android:name=".AddActivity" />
        <activity
            android:name=".SplashScreen"
            android:label="@string/app_name"
            android:theme="@style/AppTheme.NoActionBar">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <activity
            android:name=".MainActivity"
            android:theme="@style/AppTheme.NoActionBar" />
        <activity
            android:name=".IntroActivity"
            android:theme="@style/AppTheme.NoActionBar" />
    </application>

</manifest>

## Penjelasan AndroidManifest.xml
Permissions:

android.permission.INTERNET: Aplikasi membutuhkan akses internet.
android.permission.VIBRATE: Aplikasi membutuhkan akses untuk menggunakan fungsi getar pada perangkat.
Application:

android:allowBackup="true": Membolehkan backup aplikasi.
android:icon dan android:roundIcon: Menentukan ikon aplikasi.
android:label: Label aplikasi yang ditentukan di strings.xml.
android:supportsRtl="true": Mendukung tata letak RTL (right-to-left).
android:theme: Menentukan tema aplikasi.
Activities:

FilterActivity: Aktivitas untuk menyaring data.
EditActivity: Aktivitas untuk mengedit data.
AddActivity: Aktivitas untuk menambah data.
SplashScreen: Aktivitas yang tampil pertama kali saat aplikasi dijalankan (Launcher).
MainActivity: Aktivitas utama aplikasi.
IntroActivity: Aktivitas untuk menampilkan pengenalan aplikasi.
Selanjutnya, saya akan melihat lebih dalam pada beberapa file Java utama untuk memahami fungsionalitas aplikasi ini. Mari kita mulai dengan MainActivity.java. ​​

Berikut adalah analisis dari MainActivity.java dalam aplikasi ini:

# MainActivity.java
package KasAppAndroid;

import android.app.AlertDialog;
import android.app.Dialog;
import android.content.Context;
import android.content.DialogInterface;
import android.content.Intent;
import android.content.SharedPreferences;
import android.database.Cursor;
import android.os.Bundle;
import android.support.design.widget.FloatingActionButton;
import android.support.v4.widget.SwipeRefreshLayout;
import android.support.v7.app.AppCompatActivity;
import android.support.v7.widget.Toolbar;
import android.util.Log;
import android.view.View;
import android.view.Menu;
import android.view.MenuItem;
import android.view.WindowManager;
import android.widget.AdapterView;
import android.widget.ListView;
import android.widget.SimpleAdapter;
import android.widget.TextView;
import android.widget.Toast;

import com.androidnetworking.AndroidNetworking;
import com.androidnetworking.common.Priority;
import com.androidnetworking.error.ANError;
import com.androidnetworking.interfaces.JSONObjectRequestListener;
import com.leavjenn.smoothdaterangepicker.date.SmoothDateRangePickerFragment;

import org.json.JSONArray;
import org.json.JSONException;
import org.json.JSONObject;

import java.text.NumberFormat;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.Locale;

import KasAppAndroid.github.uangkas.helper.Config;

public class MainActivity extends AppCompatActivity {

    TextView text_masuk, text_keluar, text_total;
    ListView list_kas;
    SwipeRefreshLayout swipe_refresh;
    ArrayList<HashMap<String, String>> aruskas = new ArrayList<HashMap<String, String>>();

    public static TextView text_filter;
    public static String LINK, transaksi_id, status, jumlah, keterangan, tanggal, tanggal2,
            tgl_dari, tgl_ke;
    public static boolean filter;

    String query_kas, query_total;

    // session-intro
    int sess_intro;
    SharedPreferences sharedPreferences;
    SharedPreferences.Editor editor;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // Initializations
        text_masuk = findViewById(R.id.text_masuk);
        text_keluar = findViewById(R.id.text_keluar);
        text_total = findViewById(R.id.text_total);
        list_kas = findViewById(R.id.list_kas);
        swipe_refresh = findViewById(R.id.swipe_refresh);
        text_filter = findViewById(R.id.text_filter);

        sharedPreferences = getSharedPreferences("uangkas", Context.MODE_PRIVATE);
        editor = sharedPreferences.edit();

        // Floating Action Button to add new transaction
        FloatingActionButton fab = findViewById(R.id.fab);
        fab.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                Intent intent = new Intent(MainActivity.this, AddActivity.class);
                startActivity(intent);
            }
        });

        // Other initializations and method calls
        AndroidNetworking.initialize(getApplicationContext());
        KasAdapter();
        getTotalKas();
        checkIntro();
        
        // Swipe to refresh functionality
        swipe_refresh.setOnRefreshListener(new SwipeRefreshLayout.OnRefreshListener() {
            @Override
            public void onRefresh() {
                KasAdapter();
            }
        });

        // List item click listener
        list_kas.setOnItemClickListener(new AdapterView.OnItemClickListener() {
            @Override
            public void onItemClick(AdapterView<?> parent, View view, int position, long id) {
                // Handle item click
            }
        });
    }

    // Method to fetch and display data
    private void KasAdapter() {
        // Implementation to fetch and display data from server
    }

    // Method to calculate total cash flow
    private void getTotalKas() {
        // Implementation to calculate total cash flow
    }

    // Method to check if intro has been shown
    private void checkIntro() {
        sess_intro = sharedPreferences.getInt("sess_intro", 0);
        if (sess_intro == 0) {
            Intent intent = new Intent(MainActivity.this, IntroActivity.class);
            startActivity(intent);
            editor.putInt("sess_intro", 1);
            editor.commit();
        }
    }

    // Method to filter data
    private void _filterMysql() {
        SmoothDateRangePickerFragment smoothDateRangePickerFragment =
                SmoothDateRangePickerFragment
                        .newInstance(new SmoothDateRangePickerFragment.OnDateRangeSetListener() {
                            @Override
                            public void onDateRangeSet(SmoothDateRangePickerFragment view,
                                                       int yearStart, int monthStart,
                                                       int dayStart, int yearEnd,
                                                       int monthEnd, int dayEnd) {
                                String date = "You picked the following date range: \n"
                                        + "From " + dayStart + "/" + (++monthStart)
                                        + "/" + yearStart + " To " + dayEnd + "/"
                                        + (++monthEnd) + "/" + yearEnd;
                                Log.d("daterange_result", date);

                                tgl_dari = yearStart + "-" + monthStart + "-" + dayStart ;
                                tgl_ke = yearEnd + "-" + monthEnd + "-" + dayEnd ;
                                LINK = Config.host + "filter.php?from=" + tgl_dari + "&to=" + tgl_ke;

                                text_filter.setVisibility(View.VISIBLE);
                                text_filter.setText(
                                        dayStart + "/" + monthStart + "/" + yearStart + " - " +
                                                dayStart + "/" + monthStart + "/" + yearStart
                                );
                                KasAdapter();
                            }
                        });
        smoothDateRangePickerFragment.show(getFragmentManager(), "Datepickerdialog");
    }
}

## Penjelasan MainActivity.java
Inisialisasi Komponen UI:

TextView untuk menampilkan jumlah pemasukan (text_masuk), pengeluaran (text_keluar), dan total (text_total).
ListView untuk menampilkan daftar kas.
SwipeRefreshLayout untuk fitur tarik-untuk-menyegarkan (swipe_refresh).
Floating Action Button:

Tombol untuk menambah transaksi baru yang memulai AddActivity.
Android Networking:

Inisialisasi pustaka Android Networking untuk melakukan permintaan HTTP.
KasAdapter:

Metode untuk mengambil dan menampilkan data dari server.
getTotalKas:

Metode untuk menghitung total arus kas.
checkIntro:

Memeriksa apakah intro sudah ditampilkan menggunakan SharedPreferences.
_filterMysql:

Metode untuk memfilter data menggunakan SmoothDateRangePickerFragment.
