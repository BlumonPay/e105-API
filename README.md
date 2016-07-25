# Blumon Pay e105-API

SDK para integración con el dispositivo e105 de Verifone y transacciones electrónicas con Blumon Pay.
Para transaccionar con Blumon Pay se requiere de solicitar una alta de usuario al correo: helloworld@blumonpay.com

## Integración utilizando Android Studio (Java)

Para comenzar la integración se tiene que crear un proyecto nuevo en Android Studio, configuración recomendada:
- Versión de compilación: 23
- Versión mímina de SDK: 14

Importar el .aar
    <br>&nbsp;&nbsp;Selecciona File->New->New Module
    <br>&nbsp;&nbsp;Import .JAR / .AAR Package
    <br>&nbsp;&nbsp;Selecciona el .aar de Blumon Pay

Importar librerías nativas de android:
 - armeabi
 - armeabi-v7a
 - native-libs.jar

###Cofiguración de Gradle (app.gradle):
```gradle
android {
    compileSdkVersion 23
    buildToolsVersion "23.0.2"
    defaultConfig {
        applicationId "appname.company.com"
        minSdkVersion 14
        targetSdkVersion 23
        versionCode 1
        versionName "1.0"
        multiDexEnabled true
        vectorDrawables.useSupportLibrary = true
    }
    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }
}

dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile fileTree(dir: "$buildDir/native-libs", include: 'native-libs.jar')
    compile project(':BlumonPayLibrary-release')
    compile 'com.android.support:appcompat-v7:23.4.0'
    compile 'com.android.support:design:23.4.0'
    compile 'com.android.support.constraint:constraint-layout:1.0.0-alpha4'
    compile 'com.github.rahatarmanahmed:circularprogressview:2.5.0'
    compile 'com.android.support:support-v4:23.4.0'
    compile 'com.android.support:support-vector-drawable:23.4.0'
    compile 'com.android.support:recyclerview-v7:24.1.0'
}

task nativeLibsToJar(type: Zip, description: 'create a jar archive of the native libs') {
    destinationDir file("$buildDir/native-libs")
    baseName 'native-libs'
    extension 'jar'
    from fileTree(dir: 'libs', include: '**/*.so')
    into 'lib/'
}

tasks.withType(JavaCompile) {
    compileTask -> compileTask.dependsOn(nativeLibsToJar)
}
```

###Permisos de aplicación (AndroidManifest.xml):
```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.RECORD_AUDIO" />
```

###Interfaces a utilizar:

####Login
Paquete: app.blumon.com.Login.LoginListener
<br>Clase: LoginListener
<br>Métodos: 
```java
void startLogin();
void endLogin(boolean status, int code, String message);
void processingMessage(int code, String message);
```

####Transacción
Paquete: app.blumon.com.Transaction
<br>Clase: Transaction.PreTransactionListener
<br>Métodos:
```java
void startPreTransaction(String message);
void endPreTransaction(boolean status, int code, String message);
void readingCard(String message);
void slideInsertCard(String message);
void slideCardMagnetic(String message);
void processingMessage(int code, String message);
void connectionStatus(boolean status);
```
####Cancelación
Paquete: app.blumon.com.Cancellation
<br>Clase: Cancellation.CancellationListener
<br>Métodos:
```java
void startCancellation(String message);
void endCancellation(boolean status, int code, String message);
void readingCard(String message);
void slideInsertCard(String message);
void slideCardMagnetic(String message);
void processingMessage(int code, String message);
void connectionStatus(boolean status);
```

####Información del dispositivo
Paquete: app.blumon.com.model.DeviceInformation
<br>Clase: Information.InformationListener
<br>Métodos:
```java
void startGettingInformation();
void endGettingInformation(boolean status, int code, String message, DeviceInformation deviceInformation);
void processingMessage(int code, String message);
```

####Base de datos
Paquete: app.blumon.com.model.db.BlumonpayDataSource
<br>Métodos:
```java
// Constructor
BlumonpayDataSource(Context context);

// Métodos de Lectura
ServerResponse readLogin();
FirstSession readFirstSession();

// Métodos de borrar
void deleteLogin();
void deleteFirstSession();
```
<br>Uso:
```java
BlumonpayDataSource dataSource = new BlumonpayDataSource(Activity);
// Información de Login
ServerResponse loginData = dataSource.readLogin();
// Información de primera sesión del dispositivo
FirstSession firstsession = dataSource.readFirstSession();
```

####Objeto Login
Parámetro | Descripción
--- | ---
nb_branch | Nombre de la sucursal.
id_branch | Identificador de la sucursal.
nb_company | Nombre de la compañía que realizó la transacción.
id_company | Identificador de la empresa.
nb_street | Domicilio del comercio.
ticket_foot | Pie de Ticket que se imprime.
ticket_header | Encabezado del ticket.
logo | Url del logotipo personalizado.
username | Nombre del usuario.
fingerprint | Huella de dispositivo.
menu | Opciones de menú.
submenu | Opciones del submenú.
mail | E-mail del comercio.
