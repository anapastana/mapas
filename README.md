# mapas
Tutorial: Uso de Mapas no Android com MapBox e Google Maps API.
Introdução: Esse tutorial vai ensinar como utilizar o MapBox e a Google Maps API em projetos Android usando Kotlin. Isso inclui a configuração das dependências, permissões necessárias, simulação de localização no emulador e exemplos de código para adicionar marcações baseadas na localização do GPS!

Parte 1: Utilizando o MapBox no Android.
Configuração do Projeto com MapBox.
Adicionando Dependências e Configurações:
1)	Adicionar o repositório do MapBox e dependência no build.gradle do projeto:

Exemplo:
// build.gradle (Project level)
allprojects {
    repositories {
        google()
        mavenCentral()
        maven { url 'https://api.mapbox.com/downloads/v2/releases/maven' }
    }
}

// build.gradle (Module level)
dependencies {
    implementation 'com.mapbox.maps:android:10.2.0'
}
2)	Adicionar a chave do MapBox no arquivo AndroidManifest.xml:

Exemplo:
<application>
    <!-- Other configurations -->
    <meta-data
        android:name="com.mapbox.token"
        android:value="YOUR_MAPBOX_ACCESS_TOKEN"/>
</application>
Adicionando Permissões:
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />


    3) Adicionando Permissões para Obter Localização pelo GPS:
No arquivo AndroidManifest.xml:
Exemplo:
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
4)	Simulando a Localização no Emulador Android:
1.	Abra o Android Emulator.
2.	Vá em "More" (ícone de três pontos).
3.	Selecione "Location".
4.	Insira a latitude e longitude desejadas e clique em "Send".
    5) Adicionando Marcação Baseada na Localização do GPS:
Código Kotlin para adicionar um mapa e uma marcação:
Exemplo:

// MainActivity.kt

import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity
import com.mapbox.maps.MapView
import com.mapbox.maps.Style
import com.mapbox.maps.plugin.locationcomponent.LocationComponentActivationOptions
import com.mapbox.maps.plugin.locationcomponent.location
import com.mapbox.maps.plugin.locationcomponent.location2
import com.mapbox.maps.plugin.locationcomponent.utils.LocationComponentUtils
import com.mapbox.maps.plugin.locationcomponent.utils.LocationsComponentUtils

class MainActivity : AppCompatActivity() {

    private lateinit var mapView: MapView

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        mapView = findViewById(R.id.mapView)

        mapView.getMapboxMap().loadStyleUri(Style.MAPBOX_STREETS) {
            enableLocationComponent()
        }
    }

    private fun enableLocationComponent() {
        val locationComponentPlugin = mapView.location

        val locationComponentActivationOptions = LocationComponentActivationOptions.builder(this, mapView.getMapboxMap().style!!)
            .useDefaultLocationEngine(true)
            .build()

        locationComponentPlugin.updateSettings {
            enabled = true
            pulsingEnabled = true
        }

        locationComponentPlugin.activateLocationComponent(locationComponentActivationOptions)
    }
}
Layout XML (activity_main.xml):
<com.mapbox.maps.MapView
    android:id="@+id/mapView"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />

Parte 2: Utilizando a Google Maps API no Android.
   7) Configuração do Projeto com Google Maps API:
Adicionando Dependências e Configurações:
1)	Adicionar o repositório e dependência no build.gradle do projeto:

Exemplo:
// build.gradle (Project level)
allprojects {
    repositories {
        google()
        mavenCentral()
    }
}

// build.gradle (Module level)
dependencies {
    implementation 'com.google.android.gms:play-services-maps:18.0.0'
    implementation 'com.google.android.gms:play-services-location:18.0.0'
}

2 ) Adicionar a chave da API do Google Maps no arquivo: AndroidManifest.xml:
Exemplo:
<application>
    <!-- Other configurations -->
    <meta-data
        android:name="com.google.android.geo.API_KEY"
        android:value="YOUR_GOOGLE_MAPS_API_KEY"/>
</application>

Adicionando Permissões:
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
8) Adicionando Permissões para Obter Localização pelo GPS:
No arquivo AndroidManifest.xml:
Exemplo:
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
9) Simulando a Localização no Emulador Android:
1.	Abra o Android Emulator.
2.	Vá em "More" (ícone de três pontos).
3.	Selecione "Location".
4.	Insira a latitude e longitude desejadas e clique em "Send".
10) Adicionando Marcação Baseada na Localização do GPS.
Código Kotlin para adicionar um mapa e uma marcação:
Exemplo:
// MainActivity.kt

import android.Manifest
import android.content.pm.PackageManager
import android.os.Bundle
import androidx.core.app.ActivityCompat
import androidx.fragment.app.FragmentActivity
import com.google.android.gms.maps.CameraUpdateFactory
import com.google.android.gms.maps.GoogleMap
import com.google.android.gms.maps.OnMapReadyCallback
import com.google.android.gms.maps.SupportMapFragment
import com.google.android.gms.maps.model.LatLng
import com.google.android.gms.maps.model.MarkerOptions
import com.google.android.gms.location.FusedLocationProviderClient
import com.google.android.gms.location.LocationServices
import com.google.android.gms.tasks.Task

class MainActivity : FragmentActivity(), OnMapReadyCallback {

    private lateinit var mMap: GoogleMap
    private lateinit var fusedLocationClient: FusedLocationProviderClient

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        // Obtain the SupportMapFragment and get notified when the map is ready to be used.
        val mapFragment = supportFragmentManager
            .findFragmentById(R.id.map) as SupportMapFragment
        mapFragment.getMapAsync(this)
        fusedLocationClient = LocationServices.getFusedLocationProviderClient(this)
    }

    override fun onMapReady(googleMap: GoogleMap) {
        mMap = googleMap

        // Add a marker and move the camera
        if (ActivityCompat.checkSelfPermission(this,
                Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED && ActivityCompat.checkSelfPermission(
                this, Manifest.permission.ACCESS_COARSE_LOCATION) != PackageManager.PERMISSION_GRANTED) {
            ActivityCompat.requestPermissions(this, arrayOf(Manifest.permission.ACCESS_FINE_LOCATION), 1)
            return
        }
        mMap.isMyLocationEnabled = true

        fusedLocationClient.lastLocation.addOnSuccessListener { location: Location? ->
            location?.let {
                val currentLatLng = LatLng(it.latitude, it.longitude)
                mMap.addMarker(MarkerOptions().position(currentLatLng).title("Current Location"))
                mMap.moveCamera(CameraUpdateFactory.newLatLngZoom(currentLatLng, 15f))
            }
        }
    }
}
Layout XML (activity_main.xml):
<fragment
    android:id="@+id/map"
    android:name="com.google.android.gms.maps.SupportMapFragment"
    android:layout_width="match_parent"
    android:layout_height="match_parent"/>
