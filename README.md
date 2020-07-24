# firebase_flutter

Firebase for Flutter app.

## 1. 전제조건(Prerequisites)

- 객체 지향 프로그래밍이 익숙하다면, 이번 codelab을 완수할 수 있을 것입니다. 
- Dart언어로 모바일 프로그래밍이나 Firebase 경험이 필요없습니다; [introductory Flutter codelab](https://codelabs.developers.google.com/codelabs/first-flutter-app-pt1/#0) 을 해보는 것이 도움이 더 될것입니다.



## 2. Introduction

Flutter와 Firebase는 record time안에 모바일 앱을 빌드할 수 있도록 도와줄 것입니다.

- [Flutter](https://flutter.dev/)는 iOS와 android를 위한 모바일 앱 빌드를 위한 Google의 SDK입니다.
- [Firebase](https://firebase.google.com/)는 authentication, 스토리지, db와 호스팅을 포함한 모바일 앱을 위한 백엔드 서비스를 제공합니다.

이번 codelab에선, Firebase를 사용하는 Flutter 앱을 어떻게 만드는지 배울 것입니다. 부모가 아기의 이름을 친구들과 가족들이 투표를 통해 정하는 앱을 만들 것입니다. 특히, 앱은 [Cloud Firestore](https://firebase.google.com/products/firestore/) Database에 접근하고, 유저가 앱에서 액션을 취하면 db에 자동으로 업데이트합니다. 완성 앱화면이 아래사진과 같이 iOS와 Android가 있습니다. 앱을 빌드할때 Flutter로, iOS와 Android 모두 같은 코드를 사용할 수 있습니다.



## 3. Set up your Flutter environment

이번 codelab을 완성하기 위해 필요한 software 입니다.

- Flutter SDK
- An Editor
- Xcode
- Android Studio

이번 codelab을 실행할 수 있는 device입니다.

- 실제기기 (Android, iOS)
- iOS Simulator
- Android emulator



## 4. Create a new Flutter app & update dependencies

1. [Get Started: Test Drive](https://flutter.dev/docs/get-started/test-drive) 에 따라서 새로운 플러터 앱을 생성하십시오. 앱 이름은 `flutter_app`대신  `baby_names`으로 지으십시오. editor에 따라 플러터 앱을 새로 생성하는 방식이 다릅니다. IDE를 사용하고 있다면, 새로운 앱은 대게 **project**라고 불릴 것입니다.

   > 👨🏻‍💻 만약 IDE에서 옵션으로 "New Flutter Project" 가 보이지 않는다면, Flutter와 Dart 플러그인이 설치되어 있는지 확인하십시오.

2. emulator나 device에서 실행중이라면, 계속하기 전에 닫아주십시오.

3. IDE나 Editor에서 `pubspec.yaml` 파일을 열어주십시오. `cloud_firestore` 를 추가해 주시고, 파일을 저장해주세요.

   ```yaml
   dependencies:
     flutter:
       sdk: flutter
     cloud_firestore: ^0.13.7    # new
   ```

   > 👨🏻‍💻 [Pub.dev](https://pub.dev/packages/cloud_firestore) 에서 Cloud Firestore의 현재 버전을 적어주세요. 더 자세한 정보를 보고싶으시다면 dart.dev에서 [package dependencies](https://dart.dev/tools/pub/dependencies) 를 참고해주세요.

4. IDE에서 `flutter packages get` 명령어를 실행시켜주세요. 또는, 프로젝트의 맨 위에 command line에서 `flutter packages get` 을 실행해서 Flutter Packages를 추가해주세요.

에러가 난다면, `dependencies` 블럭에 들여쓰기를 확인해주세요.(tab이 아닌) 2칸 스페이스를 써야합니다.



## 5. Add the user interface

1. [Get Started: Test Drive](https://flutter.dev/docs/get-started/test-drive) 페이지를 참고해서, device나 emulator 에서 기본앱을 실행하십시오.

Flutter는 앱을 빌드하기 위해 시간이 몇분정도 소요됩니다. 좋은 소식은 이번 codelab에서 컴파일을 기다리는 마지막 시간인 것입니다. 나머지 변경 사항은 *hot-reloaded* 됩니다. 앱 빌딩이 다 되었다면, 다음과 같은 화면을 보게 될 것입니다.

![image](https://user-images.githubusercontent.com/43080040/87846458-fa097080-c90a-11ea-96ff-34954909f502.png)

2. IDE나 editor를 사용해서, `lib/main.dart` 파일을 열어주세요. 이 파일은 현재 기본 Flutter app을 위한 전체 코드가 포함되어 있습니다.
3. `main.dart` 파일에 전체 코드를 삭제하시고, 아래 파일로 대체하십시오.

```dart
import 'package:cloud_firestore/cloud_firestore.dart';
import 'package:flutter/material.dart';

void main() => runApp(MyApp());

final dummySnapshot = [
  {"name": "Filip", "votes": 15},
  {"name": "Abraham", "votes": 14},
  {"name": "Richard", "votes": 11},
  {"name": "Ike", "votes": 10},
  {"name": "Justin", "votes": 1},
];

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Baby Names',
      home: MyHomePage(),
    );
  }
}

class MyHomePage extends StatefulWidget {
  @override
  _MyHomePageState createState() {
    return _MyHomePageState();
  }
}

class _MyHomePageState extends State<MyHomePage> {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Baby Name Votes')),
      body: _buildBody(context),
    );
  }

  Widget _buildBody(BuildContext context) {
    // TODO: get actual snapshot from Cloud Firestore
    return _buildList(context, dummySnapshot);
  }

  Widget _buildList(BuildContext context, List<Map> snapshot) {
    return ListView(
      padding: const EdgeInsets.only(top: 20.0),
      children: snapshot.map((data) => _buildListItem(context, data)).toList(),
    );
  }

  Widget _buildListItem(BuildContext context, Map data) {
    final record = Record.fromMap(data);

    return Padding(
      key: ValueKey(record.name),
      padding: const EdgeInsets.symmetric(horizontal: 16.0, vertical: 8.0),
      child: Container(
        decoration: BoxDecoration(
          border: Border.all(color: Colors.grey),
          borderRadius: BorderRadius.circular(5.0),
        ),
        child: ListTile(
          title: Text(record.name),
          trailing: Text(record.votes.toString()),
          onTap: () => print(record),
        ),
      ),
    );
  }
}

class Record {
  final String name;
  final int votes;
  final DocumentReference reference;

  Record.fromMap(Map<String, dynamic> map, {this.reference})
      : assert(map['name'] != null),
        assert(map['votes'] != null),
        name = map['name'],
        votes = map['votes'];

  Record.fromSnapshot(DocumentSnapshot snapshot)
      : this.fromMap(snapshot.data, reference: snapshot.reference);

  @override
  String toString() => "Record<$name:$votes>";
}
```

4. 파일(`main.dart`)을 저장하고, 앱을 hot-reload 하십시오.
   - IDE를 사용하신다면, 자동으로 저장이 되고 hot-reload됩니다.
   - editor를 사용하신다면, `flutter run` 을 실행한 같은 디렉토리 위치에서 command에서 `r` 을 입력하십시오.

당신은 다음과 같은 화면을 보게 될 것입니다.

![image](https://user-images.githubusercontent.com/43080040/88307333-c84b3c00-cd46-11ea-94b9-304b6d47f5e4.png)

앱은 단지 mock입니다. 이름을 클릭하면 콘솔에 이름이 찍힐 것입니다.

다음 단계는 Colud Firestore에 이 앱을 연결 시키는 것입니다. 그렇게 하기 전에, `main.dart` 코드가 어떻게 구성 되어있는지 읽어보십시오.

>  **👨🏻‍💻 Optional:** 방금 copy-paste한 `main.dart` 코드를 배우기 위해선 아래를 읽어보십시오.
>
> 이 프로그램의 전반부는 매우 간단합니다:
>
> - Colud Firestore와 커뮤니케이션을 위한 플러그인`cloud_firestore.dart` 를 import합니다. 
> - Dummy data를 추가합니다. (곧 Cloud Firestore instance로 대체 될 것입니다.)
> - 앱의 title을 **Baby Names**로 세팅합니다.
>
> 코드의 재밌는 부분은 `_MyHomePageState`입니다. 이 문단에서 `build` 메소드가 앱에서 display되는 부분을 만들기 위한  [widget hierarchy](https://flutter.dev/docs/development/ui/widgets-intro)를 정의합니다.
>
> - 앱에서 `AppBar`가 있습니다.(title은 `Baby Name Votes`).
> - 앱의 body에는 각 더미 데이터 포인트를 개별 list item으로 렌더링하는 `ListView` 가 포함 되어 있습니다.
>
> 가장 긴 메소드는 `_buildListItem`으로, 리스트에서 각 아이템이 어떻게 빌드가 되는지를 이야기 합니다.(baby names와 투표수로 구성된 둥근 사각형으로 display됩니다.)
>
> 마지막으로, `Record`가 있는데, 이름에 대한 단일 레코드를 보유하는 편의를 위한 클래스입니다. 이 클래스는 이와같은 간단한 앱에선 꼭 필요하진 않지만, 코드를 더 깔끔하게 만들어줍니다.



## 6. Create a Firebase project

1. Firebase 계정이 있다면, [로그인](https://firebase.google.com/) 하십시오.

계정이 없다면, [Firebase account](https://firebase.google.com/pricing/)를 생성할 필요가 있습니다. codelab을 위한 free plan으로 하십시오.

2. [Firebase console](https://console.firebase.google.com/)에서 Add project를 클릭하십시오.
3. 아래 screencap에서 보이듯이, Firebase project를 위한 이름(예를 들면, "baby names app db")을 입력하고, "**Continue**"를 클릭하세요.
4. 다음으로, Google Analytics를 구성하세요. 이번 codelab에선 Analytics를 사용하지 않습니다. 버튼을 끄고, "**Create project**"를 클릭하세요.
5. 몇분이 지나면, Firebase project가 준비 될 것입니다. **Continue**를 클릭하세요.



## 7. Platform-specific Firebase configuration

Firebase project를 생성후, Firebase project를 사용하기 위한 하나(혹은 그 이상의) 앱을 구성할 수 있습니다. 이것을 하기위한 것은 이렇습니다 :

- Firebase에 앱의 platform-specific ID를 등록하십시오.
- 당신의 앱을 위한 구성 파일을 생성한후, 프로젝트 folder에 추가하십시오.

만약 iOS와 Android 두 플랫폼을 위한 Flutter 앱을 개발중이라면, 같은 Firebase project에서 두 플랫폼 따로 등록해야 합니다. 하나의 플랫폼만 개발중이라면, 필요하지 않은 section은 넘기셔도 됩니다.

> **✅ Tip:** 시간을 단축시키기 위해서, 지금은 하나의 플랫폼만 세팅해보길 추천합니다. 다른 하나는 언제든지 추가하실 수 있습니다.

Flutter 앱의 최상위 디렉토리에보면 `ios`와 `android`라는 하위 디렉토리가 있습니다. 이 디렉토리에는 `iOS` 와 `Android` 각각의 구성파일이 있습니다.

> 💢[현재 iOS 버전 이슈있음!]

###  Configure iOS

1. Firebase console 페이지의 왼쪽 nav에서 **Project Overview** 를 선택한 후, "Get started by adding Firebase to your app" 아래에 있는 iOS버튼을 클릭합니다.

...



### 📱Configure Android

1. Firebase console 페이지의 왼쪽 nav에서 **Project Overview** 를 선택한 후, "Get started by adding Firebase to your app" 아래에 있는 Android버튼을 클릭합니다.

> ✏️ **Note** : 이미 등록된 앱이 있다면( 예를들어, iOS app이 이전 섹션에서 등록했다면 ), **Add app** 을 클릭합니다.

아래와같은 화면을 보실 수 있습니다.

![image](https://user-images.githubusercontent.com/43080040/88308587-2fb5bb80-cd48-11ea-9e93-32ef52568e18.png)

2. 제공해야 할 중요한 가치는 **Android package name** 입니다. 두 단계를 거쳐서 얻을 수 있을 것입니다.

> **✅ Tip :** application ID와 package에 대한 더 많은 정보를 보시려면 [Set the application ID](https://developer.android.com/studio/build/application-id.html)를 참고하세요.

3. Flutter app 디렉토리에서, `android/app/src/main/AndroidManifest.xml` 파일을 열어주십시오.

4. `manifest` 요소에서, `package` 속성의 String 값을 찾으실 수 있을 것입니다. 이 값은 Android package의 이름입니다.( 예를 들면 `com.yourcompany.yourproject` ). 이 값을 복사해주세요.

5. Firebase dialog로 돌아와서, **Android package name** field에 복사한 package 이름을 붙여넣어주세요.

6. (Optional) [Google Sign In](https://firebase.google.com/docs/auth/) 혹은 [Firebase Dynamic Links](https://firebase.google.com/products/dynamic-links/) 를 사용할 계획이 있다면, **Debug signing certificate SHA-1** 값을 입력할 필요가 있습니다. 

   ```
   /// mac 기준
   
   keytool -list -v \
   -alias androiddebugkey -keystore ~/.android/debug.keystore
   ```

7. **Register App** 을 클릭해주세요.

8. Firebase에서 계속해서 `google-services.json` 구성파일을 다운로드 받습니다.

9. Flutter app 디렉토리로 돌아와서, `android/app` 디렉토리안에 `google-services.json` 파일을 이동합니다.

10. Firebase console로 돌아가서, 남아있는 단계는 건너뛰고 Firebase console의 메인 페이지로 이동합니다.

11. 마지막으로, Firebase로 부터 생성된 `google-services.json`을 적용시키기 위해 [Google Services Gradle plugin](https://developers.google.com/android/guides/google-services-plugin) 가 필요합니다.

12. IDE나 editor에서, `android/app/build.gradle` 파일을 열고, 파일의 마지막 줄에 추가하십시오.

    ```
    apply plugin: 'com.android.application'
    // Add this line
    apply plugin: 'com.google.gms.google-services'
    
    dependencies {
      // add the Firebase SDK for Google Analytics
      implementation 'com.google.firebase:firebase-analytics:17.2.2'
      // add SDKs for any other desired Firebase products
      // https://firebase.google.com/docs/android/setup#available-libraries
    }
    ```

13. `android/build.gralde` 파일을 열고, `buildscript` 태그 안에 새로운 dependency를 추가하십시오.

    ```g
    buildscript {
       repositories {
           // ...
       }
    
       dependencies {
           // ...
           classpath 'com.google.gms:google-services:4.3.3'   // new
       }
    }
    ```

    > **✏️ Note:** "new" 주석은 추가된 행을 의미합니다. 새 종속성이 다른 `classpath`와 정확히 일치하는지 확인해주세요.
    >
    >
    > 더 많은 정보를 위해선, [Add Firebase to Your Android Project](https://firebase.google.com/docs/android/setup#manually_add_firebase) 문서의 **Add the SDK** 를 확인해주세요.

14. 앱이 만약 실행중이라면, 닫고 모든 dependencies를 설치하고 다시 빌드해주세요.

안드로이드를 위한 Flutter app이 다 구성되었습니다!

## FlutterFire plugins

Flutter app이 이제 Firebase에 연결되었습니다.

Flutter는 Firebase API와 plugin을 포함한 특정 플랫폼 서비스의 광대한 범위를 위한 접근을 제공합니다. 플러그인에는 iOS 및 Android의 서비스 및 API에 액세스하기위한 플랫폼 별 코드가 포함되어 있습니다.

Firebase는 각 Firebase제품(예를들면, databses, auth, analytics, storage)마다 하나씩 다른 라이브러리를 통해 접근합니다. Flutter는 **FlutterFire** 라고 하는 각 Firebase 제품에 접근할 수 있는 자체 플러그인 세트를 제공합니다. FilutterFire 플러그인의 최신 리스트는 [Github 페이지](https://github.com/flutter/plugins/blob/master/FlutterFire.md)를 확인해주세요.



## 8. Create your Cloud Firestore databse

Firebase-Flutter 셋업이 끝나면 앱 빌딩을 시작할 준비가 된 것입니다!

몇가지 값을 초기화하고 Cloud Firestore를 세팅함으로써 시작하게 됩니다.

1. Firebase console에 들어가서 셋업하면서 만들었던 Firebase Project를 선택합니다.
2. 왼쪽 nav의 `Develop` 섹션에서 **Database**를 선택합니다.
3. Cloud Firestore 에서 `Create database(데이터베이스 만들기)` 를 클릭합니다.
4. **Security rules for Cloud Firestore** dialog가 나오면, **Start in test mode(테스트모드로 시작)** 을 클릭한다.

> ‼️ **Important :** Test모드는 개발하는 동안 편의를 위해 누구든지 데이터베이스에 쓰고 읽을 수 있습니다. 하지만, 보안적 위험성이 있습니다. production에 앱을 출시하기 전, [security rules](https://firebase.google.com/docs/reference/rules/rules)를 추가하십시오.

우리의 DB에는 "baby" 라고 이름 지을 하나의 collection이 있을 것입니다. 이 collection에는 이름과 투표수가 저장될 것입니다.

5. **Add Collection** 을 클릭하고, `baby` 라고 collection의 이름을 넣고 **Next** 를 클릭합니다.

이제 collection에 documents를 추가할 수 있습니다. 각 document는 **Document ID** 를 가지고, 우리는 **name** 과 **votes** 필드가 필요합니다. 

6. 이름을 입력합니다. 예를들면 `꿍디꿍디`

> ✏️ **Note :** 각 Document ID에 실제 아기 이름을 사용하면 문서가 아기 이름으로 알파벳순으로 표시됩니다. 기본적으로 Document ID는 timestamps가 자동으로 생성될 것이고, document는 작성된 순서대로 표시될 것입니다.

7. 기존 **Field** 의 경우, `name`의 값을 입력하고, Type을 `string` 으로 선택하고, 값은 `꿍디꿍디` 라고 입락한다.
8. `votes` 의 숫자를 포함하기 위해 두번째 field 추가를 위한 **Add Field아이콘**을 클릭합니다. Type은 `number` 를 선택하고 값은 `0` 으로 초기화합니다.
9. **Save** 를 클릭합니다.

![image](https://user-images.githubusercontent.com/43080040/88398937-0dcc3f80-ce01-11ea-8129-973f5874cafe.png)

10. **Add Document** 를 클릭해서 아기 이름을 추가합니다.

collection에 몇가지 documents를 추가하면, database는 다음과 같이 보일 것입니다.

![image](https://user-images.githubusercontent.com/43080040/88400653-9ba92a00-ce03-11ea-83d0-39b75a838533.png)



## 9. Connect your Flutter app to Cloud Firestore

