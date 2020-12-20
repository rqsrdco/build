# 1. helloworld_flutter

# 2. build flutter android app

## 2.1. Thêm biểu tượng trình khởi chạy

    thêm gói : flutter_launcher_icons: ^0.8.1 vào pubspec.yaml

    và cấu hình :
    flutter_icons:
      image_path: "lib/assets/appicon/icon.png"
      android: true
      ios: true


    sau đó chạy các lệnh :
    flutter pub get
    flutter pub pub run flutter_launcher_icons:main

## 2.2 Ký ứng dụng
    Để xuất bản trên Cửa hàng Play, bạn cần cung cấp cho ứng dụng của mình chữ ký điện tử.
### 2.2.1 Tạo kho khóa
     trên windows sử dụng lệnh:
     keytool -genkey -v -keystore c:\Users\USER_NAME\key.jks -storetype JKS -keyalg RSA -keysize 2048 -validity 10000 -alias key
    và điền các thông tin được yêu cầu
### 2.2.2 Tham khảo kho khóa từ ứng dụng
    Tạo tệp có tên <app dir>/android/key.properties chứa tham chiếu đến kho khóa của bạn
    storePassword=<password from previous step>
    keyPassword=<password from previous step>
    keyAlias=key
    storeFile=<location of the key store file, such as /Users/<user name>/key.jks>
### 2.2.3 Định cấu hình đăng nhập trong gradle
    Định cấu hình ký cho ứng dụng của bạn bằng cách chỉnh sửa <app dir>/android/app/build.gradletệp.

#### 2.2.3.1 Thêm mã trước khi android khối:
    def keystoreProperties = new Properties()
   def keystorePropertiesFile = rootProject.file('key.properties')
   if (keystorePropertiesFile.exists()) {
       keystoreProperties.load(new FileInputStream(keystorePropertiesFile))
   }
    ...........................
   android {
         ...
   }

#### 2.2.3.2 Thêm mã trước khi buildTypes khối:
   signingConfigs {
       release {
           keyAlias keystoreProperties['keyAlias']
           keyPassword keystoreProperties['keyPassword']
           storeFile keystoreProperties['storeFile'] ? file(keystoreProperties['storeFile']) : null
           storePassword keystoreProperties['storePassword']
       }
   }
   .......
   buildTypes {
       release {
           signingConfig signingConfigs.release
       }
   }

### 2.2.4 Thu hẹp mã của bạn với R8
    R8 là trình thu nhỏ mã mới của Google và nó được bật theo mặc định khi bạn tạo APK hoặc AAB phát hành. Để tắt R8, chuyển --no-shrink cờ đến flutter build apkhoặc flutter build appbundle.
### 2.2.5 Xem lại tệp kê khai ứng dụng
    Xem lại tệp Tệp kê khai ứng dụng mặc định AndroidManifest.xml, nằm trong đó <app dir>/android/app/src/mainvà xác minh rằng các giá trị là chính xác, đặc biệt là các giá trị sau:

    application
    Chỉnh sửa thẻ android:labeltrong applicationthẻ để phản ánh tên cuối cùng của ứng dụng.
    uses-permission
    Thêm android.permission.INTERNET quyền nếu mã ứng dụng của bạn cần truy cập Internet. Mẫu chuẩn không bao gồm thẻ này nhưng cho phép truy cập Internet trong quá trình phát triển để cho phép giao tiếp giữa các công cụ Flutter và một ứng dụng đang chạy.
### 2.2.6 Building the app for release
    You have two possible release formats when publishing to the Play Store.

    App bundle (preferred)
    APK

#### 2.2.6.1 Build an APK
    Although app bundles are preferred over APKs, there are stores that don’t yet support app bundles. In this case, build a release APK for each target ABI (Application Binary Interface).

    If you completed the signing steps, the APK will be signed. At this point, you might consider obfuscating your Dart code to make it more difficult to reverse engineer. Obfuscating your code involves adding a couple flags to your build command.

    From the command line:

    Enter cd <app dir>
    (Replace <app dir> with your application’s directory.)
    Run flutter build apk --split-per-abi
    (The flutter build command defaults to --release.)
    This command results in three APK files:

    <app dir>/build/app/outputs/apk/release/app-armeabi-v7a-release.apk
    <app dir>/build/app/outputs/apk/release/app-arm64-v8a-release.apk
    <app dir>/build/app/outputs/apk/release/app-x86_64-release.apk
    Removing the --split-per-abi flag results in a fat APK that contains your code compiled for all the target ABIs. Such APKs are larger in size than their split counterparts, causing the user to download native binaries that are not applicable to their device’s architecture.

#### 2.2.6.2 Install an APK on a device
    Follow these steps to install the APK on a connected Android device.

    From the command line:

    Connect your Android device to your computer with a USB cable.
    Enter cd <app dir> where <app dir> is your application directory.
    Run flutter install.

### 2.2.7 Publishing to the Google Play Store
    For detailed instructions on publishing your app to the Google Play Store, see the Google Play launch documentation.

