---
title: Migración de una aplicación Android | Microsoft Docs
description: Un tutorial sobre cómo migrar de una aplicación Android de Google Maps a Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 60d8fcc9879e89276aad80bbaf3a0edf244a45b8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/25/2019
ms.locfileid: "75475422"
---
# <a name="migrate-an-android-app-from-google-maps"></a>Migración de una aplicación Android desde Google Maps

Android SDK de Azure Maps tiene una interfaz API muy similar al SDK web. Si ha desarrollado con uno de estos SDK, se aplican muchos de los mismos conceptos, procedimientos recomendados y arquitecturas, y debería poder transferir con facilidad su conocimiento de uno a otro.

Android SDK de Azure Maps admite una versión mínima para Android de la API 21: Android 5.0.0 (Lollipop).

Todos los ejemplos se proporcionan en Java, pero también se puede usar Kotlin con Android SDK de Azure Maps.

Vea también las [guías paso a paso de Android SDK para Azure Maps](how-to-use-android-map-control-library.md) para obtener más información sobre el desarrollo con este SDK.

## <a name="load-a-map"></a>Carga de un mapa

La carga de un mapa en una aplicación Android con Google Maps o Azure Maps consta de muchos de los mismos pasos. Al usar cualquier SDK debe hacer lo siguiente:

- Obtener una API o clave de suscripción para acceder a cualquiera de las plataformas.
- Agregar código XML a una actividad para especificar dónde se debe representar el mapa y cómo se debe diseñar.
- Reenviar todos los métodos de ciclo de vida de la actividad que contienen la vista del mapa a los correspondientes en la clase de mapa. En concreto, debe reenviar los métodos siguientes:
    - `onCreate(Bundle)`
    - `onStart()`
    - `onResume()`
    - `onPause()`
    - `onStop()`
    - `onDestroy()`
    - `onSaveInstanceState(Bundle)`
    - `onLowMemory()`
- Espere a que el mapa esté listo antes de intentar acceder a él mediante programación.

**Antes: Google Maps**

Para mostrar un mapa mediante el SDK de Google Maps para Android, debe seguir estos pasos:

1.  Asegúrese de que Servicios de Google Play está instalado.
2.  Agregue una dependencia del servicio Google Maps al archivo **gradle.build** del módulo: 

    `implementation 'com.google.android.gms:play-services-maps:17.0.0'`

1.  Agregue una clave de API de Google Maps dentro de la sección de la aplicación del archivo **google\_maps\_api.xml**:
    
    ```xml
    <meta-data android:name="com.google.android.geo.API_KEY" android:value="YOUR_GOOGLE_MAPS_KEY"/>
    ```

1.  Agregue un fragmento de mapa a la actividad principal:

    ```xml
    <com.google.android.gms.maps.MapView
            android:id="@+id/myMap"
            android:layout_width="match_parent"
            android:layout_height="match_parent"/>
    ```

1.  En el archivo **MainActivity.java** tendrá que agregar importaciones para el SDK de Google Maps. Reenvíe todos los métodos de ciclo de vida de la actividad que contienen la vista del mapa a los correspondientes en la clase de mapa. Se puede recuperar una instancia de `MapView` desde el fragmento de mapa mediante el método `getMapAsync(OnMapReadyCallback)`. `MapView` inicializa de forma automática el sistema de mapas y la vista. Edite el **MainActivity.java** como sigue:

    ```java
    import com.google.android.gms.maps.GoogleMap;
    import com.google.android.gms.maps.MapView;
    import com.google.android.gms.maps.OnMapReadyCallback;
    
    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    
    public class MainActivity extends AppCompatActivity implements OnMapReadyCallback {
    
        MapView mapView;
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mapView = findViewById(R.id.myMap);
    
            mapView.onCreate(savedInstanceState);
            mapView.getMapAsync(this);
        }
    
        @Override
    
        public void onMapReady(GoogleMap map) {
            //Add your post map load code here.
        }
    
        @Override
        public void onResume() {
            super.onResume();
            mapView.onResume();
        }
    
        @Override
        protected void onStart(){
            super.onStart();
            mapView.onStart();
        }
    
        @Override
        public void onPause() {
            super.onPause();
            mapView.onPause();
        }
    
        @Override
        public void onStop() {
            super.onStop();
            mapView.onStop();
        }
    
        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapView.onLowMemory();
        }
    
        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapView.onDestroy();
        }
    
        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapView.onSaveInstanceState(outState);
        }
    }
    ```

Cuando se ejecuta en una aplicación, el control de mapa se cargará como se indica a continuación.

<center>

![Google Maps simple](media/migrate-google-maps-android-app/simple-google-maps.png)</center>

**Después: Azure Maps**

Para mostrar un mapa mediante el SDK de Azure Maps para Android, debe seguir estos pasos:

1. Abra el archivo**build.gradle** de nivel superior y agregue el siguiente código a la sección de bloques **all projects**, **repositories** (todos los proyectos, repositorios):

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Actualice **app/build.gradle** y agréguele el siguiente código:
    
    1. Asegúrese de que el valor de **minSdkVersion** del proyecto es API 21 o superior.

    2. Agregue el siguiente código a la sección Android:

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. Actualice el bloque de dependencias y agregue una nueva línea de dependencia de implementación para el Android SDK de Azure Maps más reciente:

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```

        > [!Note]
        > El Android SDK de Azure Maps se actualiza y mejora periódicamente. Puede ver la documentación de [introducción al control de mapa de Android](how-to-use-android-map-control-library.md) para obtener el número de versión de implementación más reciente de Azure Maps. Además, puede establecer el número de versión "0.2" en "0+" para que apunte siempre a la versión más reciente.
    
    4. Vaya a **Archivo** en la barra de herramientas y haga clic en **Sincronizar proyecto con archivos de Gradle**.
3. Agregue un fragmento de mapa a la actividad principal (res \> layout \> activity\_main.xml):
    
    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <FrameLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        >

        <com.microsoft.azure.maps.mapcontrol.MapControl
            android:id="@+id/mapcontrol"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            />
    </FrameLayout>
    ```

4. En el archivo **MainActivity.java** tendrá que hacer lo siguiente:
    
    * agregar las importaciones del SDK de Azure Maps
    * establecer la información de autenticación de Azure Maps
    * obtener la instancia del control de mapa en el método **onCreate**

    La configuración de la información de autenticación en la clase `AzureMaps` de forma global mediante los métodos `setSubscriptionKey` o `setAadProperties` hace que no tenga que agregar la información de autenticación en cada vista. 

    El control de mapa contiene sus propios métodos de ciclo de vida para administrar el ciclo de vida de OpenGL de Android, al que debe llamarse directamente desde la actividad que lo contiene. Para que la aplicación funcione correctamente al llamar a los métodos de ciclo de vida del control de mapa, debe anular los siguientes métodos de ciclo de vida en la actividad que contiene el control de mapa y llamar al método de control de mapa correspondiente. 

    * `onCreate(Bundle)` 
    * `onStart()` 
    * `onResume()` 
    * `onPause()` 
    * `onStop()` 
    * `onDestroy()` 
    * `onSaveInstanceState(Bundle)` 
    * `onLowMemory()`

    Edite el **MainActivity.java** como sigue:
    
    ```java
    package com.example.myapplication;

    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.options.MapStyle;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;

    public class MainActivity extends AppCompatActivity {
        
        static {
            AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
        }

        MapControl mapControl;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            mapControl = findViewById(R.id.mapcontrol);

            mapControl.onCreate(savedInstanceState);
    
            //Wait until the map resources are ready.
            mapControl.onReady(map -> {
                //Add your post map load code here.
    
            });
        }

        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
        }

        @Override
        protected void onStart(){
            super.onStart();
            mapControl.onStart();
        }

        @Override
        public void onPause() {
            super.onPause();
            mapControl.onPause();
        }

        @Override
        public void onStop() {
            super.onStop();
            mapControl.onStop();
        }

        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapControl.onLowMemory();
        }

        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapControl.onDestroy();
        }

        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapControl.onSaveInstanceState(outState);
        }
    }
    ```

Si ejecuta la aplicación, el control de mapa se cargará como se indica a continuación.

<center>

![Azure Maps simple](media/migrate-google-maps-android-app/simple-azure-maps.png)</center>

Tenga en cuenta que el control de Azure Maps permite alejar más y proporciona una mayor vista mundial.

> [!TIP]
> Si usa un emulador de Android en Windows, es posible que el mapa no se represente debido a conflictos con OpenGL y la representación de gráficos acelerada mediante hardware. Algunos usuarios han utilizado lo siguiente para resolver este problema. Abra el administrador de AVD y seleccione el dispositivo virtual que se va a editar. En la sección **Emulated Performance** (Rendimiento emulado,) establezca la opción **Graphics** (Gráficos) en **Hardware**.

## <a name="localizing-the-map"></a>Localización del mapa

Si el público está distribuido entre varios países o habla distintos idiomas, la localización es importante.

**Antes: Google Maps**

El idioma del mapa se puede establecer en el método `onCreate` de la actividad principal si se agrega el código siguiente antes de establecer la vista de contexto del mapa. Lo siguiente limita el idioma al francés mediante el código de idioma "fr".

```java
String languageToLoad = "fr";
Locale locale = new Locale(languageToLoad);
Locale.setDefault(locale);
Configuration config = new Configuration();
config.locale = locale;
getBaseContext().getResources().updateConfiguration(config,
        getBaseContext().getResources().getDisplayMetrics());
```

Este es un ejemplo de Google Maps con el idioma establecido en "fr".

<center>

![Localización de Google Maps](media/migrate-google-maps-android-app/google-maps-localization.png)</center>

**Después: Azure Maps**

Azure Maps proporciona tres formas diferentes de establecer el idioma y la vista regional del mapa. La primera opción consiste en pasar el idioma y la información de la vista regional en la clase `AzureMaps` mediante los métodos `setLanguage` y `setView` estáticos globalmente. Esto establecerá el idioma y la vista regional predeterminados en todos los controles de Azure Maps cargados en la aplicación. Lo siguiente limita el idioma al francés mediante el código de idioma "fr-FR".

```java
static {
    //Set your Azure Maps Key.
    AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

    //Set the language to be used by Azure Maps.
    AzureMaps.setLanguage("fr-FR");

    //Set the regional view to be used by Azure Maps.
    AzureMaps.setView("auto");
}
```

La segunda opción es usar la información de idioma y de la vista en el XML del control de mapa.

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

La tercera opción consiste en establecer mediante programación el idioma y la vista regional del mapa mediante el método `setStyle` de Maps. Esta opción puede realizarse en cualquier momento para cambiar el idioma y la vista regional del mapa.

```java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

Este es un ejemplo de Azure Maps con el idioma establecido en "fr-FR".

<center>

![Localización de Azure Maps](media/migrate-google-maps-android-app/azure-maps-localization.png)</center>

[Aquí](supported-languages.md) encontrará una lista completa de los idiomas y vistas regionales admitidos.

## <a name="setting-the-map-view"></a>Establecimiento de la vista del mapa

Los mapas dinámicos en Azure Maps y Google Maps se pueden mover mediante programación a nuevas ubicaciones geográficas mediante la llamada a los métodos adecuados. En los ejemplos siguientes se muestra cómo hacer que el mapa muestre las imágenes aéreas de satélite, se centre sobre una ubicación con coordenadas (latitud: 35,0272, longitud: -111,0225) y cambie el nivel de zoom a 15 en Google Maps.

> [!NOTE]
> En Google Maps se usan iconos de 256 píxeles de tamaño mientras que en Azure Maps se usa un icono mayor de 512 píxeles. Esto reduce el número de solicitudes de red que Azure Maps necesita para cargar la misma área del mapa que Google Maps. Pero debido al funcionamiento de las pirámides de iconos en los controles de mapa, que los iconos de Azure Maps sean mayores significa que, para conseguir esa misma área visible como mapa en Google Maps, debe restar uno al nivel de zoom que se usa en Google Maps cuando utilice Azure Maps.

**Antes: Google Maps**

La cámara del control de mapa de Google Maps se puede mover mediante programación con el método `moveCamera`, que permite especificar el centro del mapa y un nivel de zoom. Se puede usar el método `setMapType` para cambiar el tipo de mapa que se muestra.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.moveCamera(CameraUpdateFactory.newLatLngZoom(new LatLng(35.0272, -111.0225), 15));
    mapView.setMapType(GoogleMap.MAP_TYPE_SATELLITE);
}
```

<center>

![Vista establecida de Google Maps](media/migrate-google-maps-android-app/google-maps-set-view.png)</center>

**Después: Azure Maps**

Como se ha indicado antes, para lograr la misma área visible en Azure Maps, reste 1 al nivel de zoom que se usa en Google Maps, en este caso, use un nivel de zoom de 14.

La vista de mapa inicial se puede establecer en atributos XML en el control de mapa.

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_cameraLat="35.0272"
    app:mapcontrol_cameraLng="-111.0225"
    app:mapcontrol_zoom="14"
    app:mapcontrol_style="satellite"
    />
```

La vista del mapa se puede actualizar mediante programación con los métodos `setCamera` y `setStyle` del mapa.

```java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(35.0272, -111.0225), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![Vista establecida de Azure Maps](media/migrate-google-maps-android-app/azure-maps-set-view.png)</center>

**Recursos adicionales:**

- [Estilos de mapa admitidos](supported-map-styles.md)

## <a name="adding-a-marker"></a>Adición de un marcador

Los datos de punto se suelen representar en el mapa mediante una imagen en el mapa. Estas imágenes se suelen denominar marcadores, chinchetas, señales o símbolos. En los ejemplos siguientes se representan los datos de punto como marcadores en el mapa en (latitud: 51,5, longitud: -0,2).

**Antes: Google Maps**

Con Google Maps, los marcadores se agregan mediante el método `addMarker` de los mapas.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33)));
}
```

<center>

![Marcador de Google Maps](media/migrate-google-maps-android-app/google-maps-marker.png)</center>

**Después: Azure Maps**

En Azure Maps, los datos de punto se pueden representar en el mapa si primero se agregan los datos a un origen de datos y, después, se conecta ese origen de datos a una capa de símbolos. El origen de datos optimiza la administración de datos espaciales en el control de mapa y la capa de símbolos especifica cómo representar los datos de punto mediante una imagen o texto.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create a point feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Create a symbol layer and add it to the map.
    map.layers.add(new SymbolLayer(dataSource));
});
```

<center>

![Marcador de Azure Maps](media/migrate-google-maps-android-app/azure-maps-marker.png)</center>

## <a name="adding-a-custom-marker"></a>Adición de un marcador personalizado

Se pueden usar imágenes personalizadas para representar puntos en un mapa. En los ejemplos siguientes se usa una imagen personalizada para mostrar un punto en el mapa en (latitud: 51,5, longitud: -0,2) y desplaza la posición del marcador de modo que el punto del icono de marcador se alinee con la posición correcta en el mapa.

<center>

![imagen de marcador amarillo](media/migrate-google-maps-web-app/ylw_pushpin.png)<br/>
ylw\_pushpin.png</center>

En ambos ejemplos, la imagen anterior se agrega a la carpeta drawable de los recursos de aplicaciones.

**Antes: Google Maps**

Con Google Maps, se pueden usar imágenes personalizadas para los marcadores si se cargan a través de la opción `icon` del marcador. Para alinear el punto de la imagen con la coordenada, se puede usar la opción `anchor`. El delimitador es relativo a las dimensiones de la imagen, en este caso 0,2 unidades de ancho y 1 unidad de alto.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33))
    .icon(BitmapDescriptorFactory.fromResource(R.drawable.ylw_pushpin))
    .anchor(0.2f, 1f));
}
```

<center>

![Marcador personalizado de Google Maps](media/migrate-google-maps-android-app/google-maps-custom-marker.png)</center>

**Después: Azure Maps**

Las capas de símbolos de Azure Maps también admiten imágenes personalizadas, pero primero se debe cargar la imagen en los recursos del mapa y debe tener asignado un identificador único. Después, la capa de símbolos puede hacer referencia a este identificador. El símbolo se puede desplazar para alinearse con el punto correcto de la imagen mediante la opción `iconOffset`. Tenga en cuenta que el desplazamiento del icono se realiza en píxeles. De forma predeterminada, el desplazamiento es relativo al centro inferior de la imagen, pero se puede ajustar mediante la opción `iconAnchor`. En este ejemplo se establece la opción `iconAnchor` en `"center"` y se usa un desplazamiento de icono para mover la imagen 5 píxeles a la derecha y 15 píxeles hacia arriba hasta alinearla con el punto de la imagen del marcador.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create a point feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Load the custom image icon into the map resources.
    map.images.add("my-yellow-pin", R.drawable.ylw_pushpin);

    //Create a symbol that uses the custom image icon and add it to the map.
    map.layers.add(new SymbolLayer(dataSource,
        iconImage("my-yellow-pin"),
        iconAnchor(AnchorType.CENTER
        iconOffset(new Float[]{5f, -15f})));
});
```

<center>

![Marcador personalizado de Azure Maps](media/migrate-google-maps-android-app/azure-maps-custom-marker.png)</center>

## <a name="adding-a-polyline"></a>Adición de una polilínea

Las polilíneas se usan para representar una línea o una ruta en el mapa. En los ejemplos siguientes se muestra cómo crear una polilínea con guiones en el mapa.

**Antes: Google Maps**

Con Google Maps, se puede crear una polilínea mediante la clase `PolylineOptions` y se puede agregar al mapa con el método `addPolyline`. El color del trazo se puede establecer mediante la opción `color`, el ancho del trazo se establece mediante la opción width y se puede establecer una matriz de guiones de trazo con la opción `pattern`.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polyline.
    PolylineOptions lineOptions = new PolylineOptions()
            .add(new LatLng(46, -123))
            .add(new LatLng(49, -122))
            .add(new LatLng(46, -121))
            .color(Color.RED)
            .width(10f)
            .pattern(Arrays.<PatternItem>asList(
                    new Dash(30f), new Gap(30f)));

    //Add the polyline to the map.
    Polyline polyline = mapView.addPolyline(lineOptions);
}
```

<center>

![Polilínea de Google Maps](media/migrate-google-maps-android-app/google-maps-polyline.png)</center>

**Después: Azure Maps**

En Azure Maps, las polilíneas se llaman objetos LineString o MultiLineString. Estos objetos se pueden agregar a un origen de datos y se representan mediante una capa de línea. Tenga en cuenta que las unidades de "píxel" de ancho de trazo y de matriz de guiones se alinean con el SDK web de Azure Maps y en los dos SDK se usan los mismos valores para producir los mismos resultados.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create an array of points.
    List<Point> points = Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46));

    //Create a LineString feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(LineString.fromLngLats(points)));

    //Create a line layer and add it to the map.
    map.layers.add(new LineLayer(dataSource,
        strokeColor("red"),
        strokeWidth(4f),
        strokeDashArray(new Float[]{3f, 3f})));
});
```

<center>

![Polilínea de Azure Maps](media/migrate-google-maps-android-app/azure-maps-polyline.png)</center>

## <a name="adding-a-polygon"></a>Adición de un polígono

Los polígonos se usan para representar un área en el mapa. En los ejemplos siguientes se muestra cómo crear un polígono que forme un triángulo basado en la coordenada del centro del mapa.

**Antes: Google Maps**

Con Google Maps, se puede crear un polígono mediante la clase `PolygonOptions` y se puede agregar al mapa con el método `addPolygon`. Los colores de relleno y trazo se pueden establecer mediante las opciones `fillColor` y `strokeColor`, el ancho del trazo se establece con la opción `strokeWidth`.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polygon.
    PolygonOptions polygonOptions = new PolygonOptions()
            .add(new LatLng(46, -123))
            .add(new LatLng(49, -122))
            .add(new LatLng(46, -121))
            .add(new LatLng(46, -123))  //Close the polygon.
            .fillColor(Color.argb(128, 0, 128, 0))
            .strokeColor(Color.RED)
            .strokeWidth(5f);

    //Add the polygon to the map.
    Polygon polygon = mapView.addPolygon(polygonOptions);
}
```

<center>

![Polígono de Google Maps](media/migrate-google-maps-android-app/google-maps-polygon.png)</center>

**Después: Azure Maps**

En Azure Maps, se pueden agregar los objetos Polygon y MultiPolygon a un origen de datos y se representan en el mapa mediante capas. El área de un polígono se puede representar en una capa de polígono. El contorno de un polígono se puede representar con una capa de línea. Tenga en cuenta que las unidades de "píxel" de ancho de trazo y de matriz de guiones se alinean con el SDK web de Azure Maps y en los dos SDK se usan los mismos valores para producir los mismos resultados.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create an array of point arrays to create polygon rings.
    List<List<Point>> rings = Arrays.asList(Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46),
        Point.fromLngLat(-123, 46)));

    //Create a Polygon feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Polygon.fromLngLats(rings)));

    //Add a polygon layer for rendering the polygon area.
    map.layers.add(new PolygonLayer(dataSource,
        fillColor("green"),
        fillOpacity(0.5f)));

    //Add a line layer for rendering the polygon outline.
    map.layers.add(new LineLayer(dataSource,
        strokeColor("red"),
        strokeWidth(2f)));
});
```

<center>

![Polígono de Azure Maps](media/migrate-google-maps-android-app/azure-maps-polygon.png)</center>

## <a name="overlay-a-tile-layer"></a>Superposición de una capa de icono

Las capas de icono, también conocidas como superposiciones de imagen en Google Maps, permiten superponer imágenes de capas que se han dividido en imágenes de icono más pequeñas que se alinean con el sistema de iconos de los mapas. Se trata de una manera común de superponer imágenes de capas o conjuntos de datos muy grandes.

En los ejemplos siguientes se superpone una capa de icono de radar meteorológico de Iowa Environmental Mesonet of Iowa State University. Los iconos tienen un tamaño de 256 píxeles.

**Antes: Google Maps**

Con Google Maps, se puede superponer una capa de icono en la parte superior del mapa mediante la clase `TileOverlayOptions` y agregarla al mapa con el método `addTileLauer`. Para hacer que los iconos sean semitransparentes, la opción `transparency` se establece en 0,2, o en un 20 % de transparencia.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the tile layer.
    TileOverlayOptions tileLayer = new TileOverlayOptions()
        .tileProvider(new UrlTileProvider(256, 256) {
            @Override
            public URL getTileUrl(int x, int y, int zoom) {

                try {
                    //Define the URL pattern for the tile images.
                    return new URL(String.format("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/%d/%d/%d.png", zoom, x, y));
                }catch (MalformedURLException e) {
                    throw new AssertionError(e);
                }
            }
        }).transparency(0.2f);

    //Add the tile layer to the map.
    mapView.addTileOverlay(tileLayer);
}
```

<center>

![Capa de icono de Google Maps](media/migrate-google-maps-android-app/google-maps-tile-layer.png)</center>

**Después: Azure Maps**

En Azure Maps, se puede agregar una capa de icono al mapa de la misma manera que cualquier otra. Una dirección URL con formato que tiene marcadores de posición X, Y y zoom (`{x}`, `{y}`, `{z}` respectivamente) se usa para indicar a la capa dónde acceder a los iconos. Las capas de icono en Azure Maps también admiten los marcadores de posición `{quadkey}`, `{bbox-epsg-3857}` y `{subdomain}`. Para que la capa de icono sea semitransparente, se usa un valor de opacidad de 0,8. Tenga en cuenta que la opacidad y la transparencia, aunque son similares, usan valores invertidos. Para realizar la conversión entre ellos simplemente reste su valor del número uno.

> [!TIP]
> En Azure Maps las capas se pueden representar fácilmente bajo otras capas, incluidas las capas base del mapa. A menudo es conveniente representar las capas de icono debajo de las etiquetas de mapa para que resulten fáciles de leer. El método `map.layers.add` toma un segundo parámetro que es el identificador de la capa en la que se va a insertar la nueva capa siguiente. Para insertar una capa de icono debajo de las etiquetas de mapa se puede usar el código siguiente: `map.layers.add(myTileLayer, "labels");`

```java
mapControl.onReady(map -> {
    //Add a tile layer to the map, below the map labels.
    map.layers.add(new TileLayer(
        tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
        opacity(0.8f),
        tileSize(256)
    ), "labels");
});
```

<center>

![Capa de icono de Azure Maps](media/migrate-google-maps-android-app/azure-maps-tile-layer.png)</center>

## <a name="show-traffic"></a>Visualización del tráfico

Los datos de tráfico se pueden superponer en Azure Maps y Google Maps.

**Antes: Google Maps**

Con Google Maps, los datos de flujo de tráfico se pueden superponer encima del mapa si se pasa true al método `setTrafficEnabled` del mapa.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.setTrafficEnabled(true);
}
```

<center>

![Tráfico de Google Maps](media/migrate-google-maps-android-app/google-maps-traffic.png)</center>

**Después: Azure Maps**

Azure Maps proporciona varias opciones diferentes para mostrar el tráfico. Los incidentes de tráfico, como los cierres de carreteras y los accidentes, se pueden mostrar como iconos en el mapa. El flujo de tráfico o las carreteras codificadas mediante colores se pueden superponer en el mapa y los colores se pueden modificar para que se basen en el límite de velocidad registrado, el retraso normal esperado o el retraso absoluto. Los datos de incidentes en Azure Maps se actualizan cada minuto y el flujo de datos se produce cada dos minutos.

```java
mapControl.onReady(map -> {
    map.setTraffic(
        incidents(true),
        flow(TrafficFlow.RELATIVE));
});
```

<center>

![Tráfico de Azure Maps](media/migrate-google-maps-android-app/azure-maps-traffic.png)</center>

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre Android SDK de Azure Maps.

> [!div class="nextstepaction"]
> [Procedimientos para usar el control de mapa de Android](how-to-use-android-map-control-library.md)
