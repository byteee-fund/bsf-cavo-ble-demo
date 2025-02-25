# bsf-cavo-ble

此项目是一款基于`cavo-ble-sdk`开发的适用于`Uniapp`/`UniappX`的智能手环蓝牙通信`UTS`插件。

## 平台支持
- Android
- iOS
- Uniapp/Uniappx

## 使用说明

### 引入插件

```js
import * as CavoManager from "@uni_modules/bsf-cavo-ble";
```

## API 接口

### checkPermission (检查蓝牙权限)
```js
CavoManager.checkPermission()
```

### requestPermission (请求蓝牙权限)
```js
CavoManager.requestPermission({
  onPermit: () => {
    // 授权成功回调
  },
  onRefuse: () => {
    // 授权失败回调
  }
});
```

### startScan (开始扫描设备)
```js
CavoManager.startScan({
  onWristbandDeviceFind: (device, rssi) => {
    // 发现设备回调
  },
  onLeScanEnable: (enable) => {
    // 蓝牙状态回调
  },
  onWristbandLoginStateChange: (connected) => {
    // 设备登录状态回调
  },
  onStartLeScan: () => {
    // 开始扫描回调
  },
  onCancelLeScan: () => {
    // 取消扫描回调
  },
  onStopLeScan: () => {
    // 停止扫描回调
  }
});
```

### stopScan (停止扫描)
```js
CavoManager.stopScan()
```

### connect (连接设备)
```js
CavoManager.connect(device.address)
```

### destroySDK (注销SDK)
```js
CavoManager.destroySDK()
```

### initSDK (初始化SDK)
```js
CavoManager.initSDK({
  onConnectionStateChange: (res) => {
    // 连接状态变化回调
  },
  onLoginStateChange: (res) => {
    // 登录状态变化回调
  },
  onError: (res) => {
    // 错误回调
  },
  onDeviceInfo: (res) => {
    // 设备信息回调
  },
  onStepDataReceiveIndication: (res) => {
    // 步数数据接收回调
  },
  onSleepDataReceiveIndication: (res) => {
    // 睡眠数据接收回调
  },
  onSyncDataBegin: () => {
    // 同步开始回调
  },
  onSyncDataEnd: () => {
    // 同步结束回调
  }
});
```

### login (登录设备)
```js
CavoManager.login("1234567890")
```

### getDeviceInfo (获取设备信息)
```js
CavoManager.getDeviceInfo()
```

### getDeviceFunction (获取设备功能)
```js
CavoManager.getDeviceFunction()
```

### syncTime (同步时间)
```js
CavoManager.syncTime()
```

### syncData (同步数据)
```js
CavoManager.syncData()  // 返回布尔值表示是否成功
```

### setLanguage (设置语言)
```js
CavoManager.setLanguage(languageCode)
```

### setStepData (设置步数数据)
```js
CavoManager.setStepData(steps, distance, calories)
```

### setSleepData (设置睡眠数据)
```js
CavoManager.setSleepData(minutes, mode, type)
```

### getStepData (获取步数数据)
```js
CavoManager.getStepData({
  year: 2024,
  month: 12,
  day: 21,
  callback: (res) => {
    // 步数数据回调
    // res 数据结构:
    // {
    //   stepCount: number,    // 步数
    //   day: number,         // 日期
    //   userID: string,      // 用户ID
    //   mode: number,        // 模式
    //   activeTime: number,  // 活动时间
    //   id: number,          // ID
    //   offset: number,      // 偏移量
    //   year: number,        // 年份
    //   date: string,        // 日期字符串
    //   month: number,       // 月份
    //   distance: number,    // 距离
    //   calory: number      // 卡路里
    // }
  }
});
```

### getSleepData (获取睡眠数据)
```js
CavoManager.getSleepData({
  year: 2024,
  month: 12,
  day: 21,
  callback: (res) => {
    // 睡眠数据回调
    // res 数据结构:
    // {
    //   userID: string,     // 用户ID
    //   day: number,        // 日期
    //   minutes: number,    // 分钟数
    //   id: number,         // ID
    //   year: number,       // 年份
    //   month: number,      // 月份
    //   mode: number,       // 模式
    //   date: string        // 日期字符串
    // }
  }
});
```

## 状态码说明

### 登录状态码
- 91: 登录中
- 2: 绑定中
- 3: 登录成功
- 4: 同步数据
- 5: 同步历史数据

### 错误状态码
- 2: 绑定错误
- 3: 发送命令错误
- 101: 无登录响应

