# Blumon Pay e105-API

SDK para integración con el dispositivo e105 de Verifone.
Para transaccionar con Blumon Pay se requiere de solicitar una alta de usuario al correo: helloworld@blumonpay.com

## Integración utilizando Android Studio (Java)

Para comenzar la integración se tiene que crear un proyecto nuevo en Android Studio, configuración recomendada:
- Versión de compilación: 23
- Versión mímina de SDK: 14

Para importar el .aar, se selecciona File->New->New Module
Seleccionar Import .JAR / .AAR Package
Y se selecciona el .aar de Blumon Pay

Cofiguración de Gradle:

```json
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
    compile project(':BlumonPayLibrary')
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

....
