---
title: create-react-native-module
date: 2019-11-09 12:29:47
tags: React Native
---

# create-react-native-module

记一下接入商汤银行卡 SDK Android 接入流程

## 创建库

借助[create-react-native-module](https://github.com/brodybits/create-react-native-module),简单步骤如下:

```js
  npm install -g react-native-cli yarn

  npm install -g create-react-native-module

  create-react-native-module MyFancyLibrary

  npm install
```

经过如上步骤,我们就得到一个模板,然后,我们只需要在我们的主项目,如下步骤,就可以引入改库

```json
  dependencies:{
    "react-native-my-fancy-library": "../react-native-my-fancy-library"  // 我的库放在与项目同级
  }
```

最后,yarn install 即可,由于[auto-linking](https://github.com/react-native-community/cli/blob/master/docs/autolinking.md) 的原因,我们不再需要其他操作.

关于 auto-linking 简单的配置简单列举如下:

react-native.config.js

```js
// react-native.config.js
module.exports = {
  dependency: {
    platforms: {
      android: null, // disable Android platform, other platforms will still autolink if provided  //Android 平台不自动auto-linking
    },
  },
};

// react-native.config.js
module.exports = {
  dependencies: {
    "some-unsupported-package": {
      platforms: {
        android: null, // disable Android platform, other platforms will still autolink if provided 禁用某个包自动autoLink
      },
    },
  },
};

//关于初始化某个包,并且传入一定参数的配置
module.exports = {
  dependencies: {
    "jshare-react-native": {
      platforms: {
        android: {
          packageInstance: "new JSharePackage(false, false)",
        },
      },
    },
  },
};
```

### autoLink 链接推荐

-[auto-linking 的相关介绍](https://juejin.im/post/5dc14bfc6fb9a04aac11355e)

### 错误

可能会莫名其妙的一个错误,不知道原因

error: Error: Unable to resolve module `warnOnce` from `node_modules\react-native-my-fancy-library\node_mo dules\react-native\Libraries\react-native\react-native-implementation.js`: warnOnce could not be fou
nd within the project.

https://github.com/facebook/react-native/issues/24065

metro.config.js

```js
// add
const blacklist = require("metro-config/src/defaults/blacklist");

module.exports = {
  // add ...
  resolver: {
    blacklistRE: blacklist([
      /node_modules\/.*\/node_modules\/react-native\/.*/,
    ]),
  },
  // add end...

  transformer: {
    getTransformOptions: async () => ({
      transform: {
        experimentalImportSupport: false,
        inlineRequires: false,
      },
    }),
  },
};
```

## 集成 sdk

Android studio 打开库项目,gradle 完成后. 步骤如下:

1. android 目录下 新建 libs,添加 ocr-fullCards-online-release.aar 文件

arr 是一个特殊的 android 二进制代码包,里面 存放 java,xml ..也就是跟我们项目代码一样的文件.

- https://blog.csdn.net/qq_32452623/article/details/79220522
- https://developer.android.com/studio/projects/android-library

2. RN 工程 android 目录下的 build.gradle 配置如下:

```gradle
  allprojects {
      repositories {
          ....
          flatDir {
            dirs "$rootDir/../node_modules/react-native-my-fancy-library/libs"
          }
      }
  }
```

3. 库项目 build.gradle 配置如下:

```gradle
    dependencies {
      // ref:
      // https://github.com/facebook/react-native/blob/0.61-stable/template/android/app/build.gradle#L192
      //noinspection GradleDynamicVersion
      implementation 'com.facebook.react:react-native:+'  // From node_modules
      implementation 'androidx.appcompat:appcompat:1.0.0'  //  androidx 包
      implementation 'com.google.android.material:material:1.0.0'  //  material 包
      implementation 'androidx.constraintlayout:constraintlayout:2.0.0-alpha3' //  androidx

      implementation fileTree(dir: 'libs', include: ['*.aar'])
  }
```

4. 复制 SDK 提供的代码,选取 一些必要的功能,迁移到我们的库中.

迁移过程遇到的错误,我们可以根据 android studio 的提示进行解决,

需要注意的点

- 要注意引入的 Activity,以及在 manifests.xml 文件配置
- 迁移 androidManifests 文件时,要注意 AndroidX 包下组件存在问题(包名要正确)

一切直到 android studio 不提示报错,我们就可以进行 androidX 的迁移

5. 迁移 androidX

为了紧跟时代,我们的库必修声明为支持 androidX,local.properties 配置如下

配置如下:

```
  android.useAndroidX=true
  android.enableJetifier=true
```

步骤无非就是 更改包名 ..... 更改 xml 中的包名

旧库映射

https://developer.android.com/jetpack/androidx/migrate/artifact-mappings

android 支持迁移到 androidX

- https://juejin.im/post/5d821d336fb9a06af92bd965
- https://developer.android.com/jetpack/androidx/migrate
- https://medium.com/androiddevelopers/migrating-to-androidx-tip-tricks-and-guidance-88d5de238876

## 编写桥接代码

MyFancyLibraryModule.java 文件如下

```java

package com.reactlibrary;
/*
  ...  引入的一系列包
*/


public class MyFancyLibraryModule extends ReactContextBaseJavaModule {

    private static final int REQUEST_CODE_RESULT = 1;
    private static final int REQUEST_CODE_SCAN = 2;

    private Promise mScanBankCardPromise;

    private boolean mRequestScreenLandscape;

    private final ReactApplicationContext reactContext;

    private final ActivityEventListener mActivityEventListener = new BaseActivityEventListener() {
        @Override
        public void onActivityResult(Activity activity, int requestCode, int resultCode, Intent data) {
            if (requestCode == REQUEST_CODE_RESULT) {
                return;
            }
            switch (resultCode) {
                case RESULT_CANCELED:
                    Toast.makeText(getCurrentActivity(), R.string.canceled, Toast.LENGTH_LONG).show();
                    mScanBankCardPromise.reject("RESULT_CANCELED","扫描取消");
                    break;
                case ActivityUtils.RESULT_CODE_NO_PERMISSIONS:
                case ActivityUtils.RESULT_CODE_CAMERA_ERROR:
                    Toast.makeText(getCurrentActivity(), R.string.error_camera, Toast.LENGTH_LONG).show();
                    mScanBankCardPromise.reject("RESULT_CODE_CAMERA_ERROR","相机发生错误");
                    break;
                case ActivityUtils.RESULT_CODE_LICENSE_FILE_NOT_FOUND:
                    Toast.makeText(getCurrentActivity(), R.string.license_file_not_found, Toast.LENGTH_LONG).show();
                    mScanBankCardPromise.reject("RESULT_CODE_LICENSE_FILE_NOT_FOUND","授权文件未发现");
                    break;
               /*
                   为了缩减代码 .....取消了其他的一些case
               */
                case Activity.RESULT_OK:


                    // 获取结果.... 解析 intent.....
                    HashMap map=new HashMap();
                    final String EXTRA_CARD_NUMBER = "extra_card_number";
                    final String EXTRA_BANK_NAME = "extra_bank_name";
                    final String EXTRA_BANK_ID = "extra_bank_id";
                    final String EXTRA_CARD_NAME = "extra_card_name";
                    final String EXTRA_CARD_TYPE = "extra_card_type";
                    final String EXTRA_CARD_RESULT_IMAGE = "extra_card_result_image";

                    //  hash 暴露出去,一个object 对象
                    map.put(EXTRA_CARD_NUMBER,data.getStringExtra(EXTRA_CARD_NUMBER));
                    map.put(EXTRA_BANK_NAME,data.getStringExtra(EXTRA_BANK_NAME));
                    map.put(EXTRA_BANK_ID,data.getStringExtra(EXTRA_BANK_ID));
                    map.put(EXTRA_CARD_TYPE,data.getStringExtra(EXTRA_CARD_TYPE));
                    map.put(EXTRA_CARD_NAME,data.getStringExtra(EXTRA_CARD_NAME));
                    map.put(EXTRA_CARD_RESULT_IMAGE,data.getStringExtra(EXTRA_CARD_RESULT_IMAGE));
                    mScanBankCardPromise.resolve(map);
                    break;
                default:
                    break;
            }
        }
    };

    public MyFancyLibraryModule(ReactApplicationContext reactContext) {
        super(reactContext);
        this.reactContext = reactContext;
        // 注册打开的Activity 后返回的结果监听
        reactContext.addActivityEventListener(mActivityEventListener);
    }

    @Override
    public String getName() {
        return "MyFancyLibrary";  // 模块名
    }

    // 代用sdk 的一些先决条件的配置
    @ReactMethod
    public  void  registerSenseTime(String appId, String key){
        AppSecret.setKeyAndSecret(appId,key);
    }
    //  暴露出去的桥接方法 ,开始扫描卡
    @ReactMethod void scanCard(String orientation, Promise promise){
        // 判断方向
        if(orientation.equals("horizontal")){
            mScanBankCardPromise=promise;
            checkPermissionToDetect(BankCardActivity.CARD_ORIENTATION_HORIZONTAL);
        }
        if(orientation.equals("vertical")){
            checkPermissionToDetect(BankCardActivity.CARD_ORIENTATION_VERTICAL);
        }

    }

    private void checkPermissionToDetect(int cardOrientation) {
      //  一些权限判断
        if (Build.VERSION.SDK_INT >= 23) {
            List<String> permissions = null;
            if (ContextCompat.checkSelfPermission(reactContext,Manifest.permission.CAMERA) != PackageManager.PERMISSION_GRANTED) {
                permissions = new ArrayList<>();
                permissions.add(Manifest.permission.CAMERA);
            }
            if (ContextCompat.checkSelfPermission(reactContext,Manifest.permission.WRITE_EXTERNAL_STORAGE) != PackageManager.PERMISSION_GRANTED) {
                if (permissions == null) {
                    permissions = new ArrayList<>();
                }
                permissions.add(Manifest.permission.WRITE_EXTERNAL_STORAGE);
            }
            if (permissions != null) {
                String[] permissionArray = new String[permissions.size()];
                permissions.toArray(permissionArray);
                /// Request the permission. The result will be received
                // in onRequestPermissionResult()
                ActivityCompat.requestPermissions(getCurrentActivity(),permissionArray, cardOrientation);
            } else {
              //  开启activity
                startDetectActivity(cardOrientation);
            }
        } else {
            startDetectActivity(cardOrientation);
        }
    }



    private void startDetectActivity(int cardOrientation) {

        Intent intent = new Intent(getCurrentActivity(),
                this.mRequestScreenLandscape ? LandscapeScanActivity.class : PortraitScanActivity.class);
        intent.putExtra(BankCardActivity.EXTRA_CARD_ORIENTATION, cardOrientation);
            //  启动创窗口
        getCurrentActivity().startActivityForResult(intent, REQUEST_CODE_SCAN);
    }

}

```

至此,我们就完成了 android 模块的集成,只等 JS 端调用 即可,代码如下:

```js
import MyFancyLibrary from "react-native-my-fancy-library";
MyFancyLibrary.scanCard("horizontal")
  .then((res) => {})
  .catch((e) => {});
```
