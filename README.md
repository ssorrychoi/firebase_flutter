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

   > 만약 IDE에서 옵션으로 "New Flutter Project" 가 보이지 않는다면, Flutter와 Dart 플러그인이 설치되어 있는지 확인하십시오.

2. emulator나 device에서 실행중이라면, 계속하기 전에 닫아주십시오.

3. IDE나 Editor에서 `pubspec.yaml` 파일을 열어주십시오. `cloud_firestore` 를 추가해 주시고, 파일을 저장해주세요.

   ```yaml
   dependencies:
     flutter:
       sdk: flutter
     cloud_firestore: ^0.13.7    # new
   ```

   > [Pub.dev](https://pub.dev/packages/cloud_firestore) 에서 Cloud Firestore의 현재 버전을 적어주세요. 더 자세한 정보를 보고싶으시다면 dart.dev에서 [package dependencies](https://dart.dev/tools/pub/dependencies) 를 참고해주세요.

4. IDE에서 `flutter packages get` 명령어를 실행시켜주세요. 또는, 프로젝트의 맨 위에 command line에서 `flutter packages get` 을 실행해서 Flutter Packages를 추가해주세요.

에러가 난다면, `dependencies` 블럭에 들여쓰기를 확인해주세요.(tab이 아닌) 2칸 스페이스를 써야합니다.