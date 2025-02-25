<template>
  <view class="content">
    <view class="title">{{ title }}</view>
    <view class="text-area">
      <text :class="getScanStateClass">扫描状态：{{scanState}}</text>
      <text :class="getConnectStateClass">连接状态：{{connectState}}</text>
    </view>
    <view class="device-list">
      <view v-for="(device, index) in devices" :key="index" class="device-item">
        <text>设备名称: {{ device?.name || '未知' }}</text>
        <text>MAC地址: {{ device?.address || '未知' }}</text>
        <button @click="connectToDevice(device)" class="connect-button">连接</button>
      </view>
    </view>
    <view class="buttons">
      <view class="button-group">
        <button @click="startScan" class="primary-button">开始扫描</button>
        <button @click="stopScan">停止扫描</button>
      </view>
      
      <view class="button-group">
        <button @click="login" class="required-button">登录[必须]</button>
        <button @click="getDeviceInfo" class="required-button">获取设备信息[必须]</button>
        <button @click="getDeviceFunc" class="required-button">获取设备特征[必须]</button>
      </view>
      
      <view class="button-group">
        <button @click="syncData" class="data-button">同步数据</button>
        <button @click="getStepData" class="data-button">获取步数</button>
        <button @click="getSleepData" class="data-button">获取睡眠</button>
        <button @click="setStepData" class="data-button">设置步数</button>
        <button @click="setSleepData" class="data-button">设置睡眠</button>
      </view>
    </view>
    <view class="footer">
		<text>Copyright © BSF 2025</text>
		<text class="link" @click="openUrl">https://byteee.fund</text>
	</view>
  </view>
</template>



<script>
import * as CavoManager from "../../uni_modules/bsf-cavo-ble";

export default {
  data() {
    return {
	  title: "bsf-cavo-ble示例工程",
      sdkVersion: '',
      scanState: "",
	  connectState: "",
      bmVersion: '',
      devices: [] // 用于存储设备列表
    }
  },
  onLoad() {
	 this.requestPermission();
  },
  onShow() {
	 
  },
  onHide() {
	// 注销SDK
	CavoManager.destroySDK();
  },
  methods: {
	requestPermission() {
	  if (!CavoManager.checkPermission()) {
		CavoManager.requestPermission({
			onPermit: () => {
				// 初始化
				this.init();
			},
			onRefuse: () => {
				
			}
		});  
	  } else {
		  this.init();
	  }
	},
	
	init() {
	CavoManager.initSDK({
		onConnectionStateChange: (res) => {
			console.log(res);
			this.connectState = res ? "已连接" : "未连接";
			CavoManager.syncTime();
			// CavoManager.setLanguage(0);
		},
		onLoginStateChange: (res) => {
			console.log(res);
			const stateDescriptions = {
			  91: "登录中",
			  2: "绑定中",
			  3: "登录成功",	
			  4: "同步数据",
			  5: "同步历史数据",	  
			};
			
			uni.showToast({
				title:  stateDescriptions[res] || "未知错误",
				duration:2000
			});
		},
		onError: (res) => {
			console.log(res);
			const stateDescriptions = {
			  2: "绑定错误",
			  3: "发送命令错误",
			  101:"无登录响应"
			};
			
			uni.showToast({
				title:  stateDescriptions[res] || "未知错误",
				duration:2000
			});	
		},
		onDeviceInfo: (res)=> {
				console.log("获取设备信息");
				console.log(res);
		},
		onStepDataReceiveIndication: (res)=> {
			console.log(res);
		},
		onSleepDataReceiveIndication: (res)=> {
			console.log(res);
		},
		onSyncDataBegin: () => {
			console.log("同步开始");
		},
		onSyncDataEnd: () => {
			console.log("同步结束");
		}
	});	
	},
	
    startScan() {
	  CavoManager.startScan({
		  onWristbandDeviceFind: (device, rssi) => {
			  console.log(device);
			  this.devices = [device];
		  },
		  onLeScanEnable: (enable) => {
			  this.scanState = enable ? "蓝牙未启用": "蓝牙已启用";
		  },
		  onWristbandLoginStateChange: (connected) => {
			   this.scanState = connected ? "设备已登录": "设备未登录";
		  },
		  onStartLeScan: () => {
			   this.scanState = "正在扫描";
		  },
		  onCancelLeScan: () => {
			  this.scanState = "取消扫描";
		  },
		  onStopLeScan: () => {
			  this.scanState = "停止扫描";
		  }
	  })
    },
    stopScan() {
      CavoManager.stopScan()
    },
	connectToDevice(device) {
		console.log(device)
		CavoManager.connect(device.address)	
	},
	login() {
		CavoManager.login("1234567890");
	},
	getDeviceInfo() {
		CavoManager.getDeviceInfo();
	},
	getDeviceFunc() {
		CavoManager.getDeviceFunction();
	},
	syncData() {
		if(CavoManager.syncData()) {
			uni.showToast({
				title:  "操作成功",
				duration:2000
			});
		} else {
			uni.showToast({
				title:  "操作失败",
				duration:2000
			});
		}
	},
	setStepData() {
		CavoManager.setStepData(999,999,999);
	},
	setSleepData() {
		CavoManager.setSleepData(100,120,1);
	},
	getStepData() {
		CavoManager.getStepData({
			year: 2025,
			month: 1,
			day: 3,
			callback:(res)=> {
				// 安卓返回
				// {
				//     "stepCount": 273,
				//     "day": 20,
				//     "userID": "1234567890",
				//     "mode": 0,
				//     "activeTime": 0,
				//     "id": 1,
				//     "offset": 70,
				//     "year": 2024,
				//     "date": "Dec 21, 2024 3:19:15 PM",
				//     "month": 12,
				//     "distance": 217,
				//     "calory": 10415
				// }
		
				console.log(res)
			}
		});
		
	},
	getSleepData() {
		CavoManager.getSleepData({
			year: 2024,
			month: 12,
			day: 21,
			callback:(res)=> {
				// 安卓数据
				// {
				//     "userID": "1234567890",
				//     "day": 20,
				//     "minutes": 1200,
				//     "id": 3,
				//     "year": 2024,
				//     "month": 12,
				//     "mode": 3,
				//     "date": "Dec 21, 2024 3:19:15 PM"
				// }
				console.log(res)
			}
		});
	},
    updateState(state) {
      // const stateDescriptions = {
      //   0: "蓝牙不可用",
      //   1: "蓝牙可用",
      //   2: "停止扫描",
      //   3: "正在扫描",
      //   4: "将要连接",
      //   5: "已经连接",
      //   6: "连接出错",
      //   7: "外设特征回调",
      //   8: "断开连接",
      //   9: "通过验证",
      //   10: "未通过验证",
      //   255: "没有蓝牙权限"
      // };
      // this.state = stateDescriptions[state] || "未知";
	  
	  // if (state == 9) {
		 //  // 设置单位
		 //  AlinkManager.setUnit(0)	
	  // }
	  
	  
      // console.log("当前状态：", this.state);
    },
	openUrl() {
		// #ifdef APP-PLUS
		plus.runtime.openURL('https://byteee.fund', (err) => {
			if (err) {
				uni.showToast({
					title: '打开链接失败',
					icon: 'none'
				});
			}
		});
		// #endif
		
		// #ifdef H5
		window.open('https://byteee.fund', '_blank');
		// #endif
	},
  },
  computed: {
    getScanStateClass() {
      if (this.scanState.includes('正在扫描')) return 'status-scanning'
      if (this.scanState.includes('停止') || this.scanState.includes('取消')) return 'status-disconnected'
      return ''
    },
    getConnectStateClass() {
      if (this.connectState === '已连接') return 'status-connected'
      if (this.connectState === '未连接') return 'status-disconnected'
      return ''
    }
  }
}
</script>


<style>
.content {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: flex-start;
  padding: 20rpx;
  min-height: 100vh;
  background-color: #f5f6fa;
}

.text-area {
  background-color: white;
  padding: 16rpx 20rpx;
  border-radius: 12rpx;
  width: 100%;
  box-shadow: 0 2rpx 8rpx rgba(0, 0, 0, 0.05);
  margin-bottom: 20rpx;
}

.text-area text {
  display: block;
  padding: 10rpx;
  margin: 6rpx 0;
  border-radius: 6rpx;
  font-size: 26rpx;
}

/* 状态样式 */
.status-connected {
  background-color: #e8f5e9;
  color: #2e7d32;
}

.status-disconnected {
  background-color: #ffebee;
  color: #c62828;
}

.status-scanning {
  background-color: #e3f2fd;
  color: #1565c0;
}

.title {
  font-size: 32rpx;
  color: #2c3e50;
  font-weight: 600;
  margin-bottom: 16rpx;
  text-align: center;
}

.buttons {
  display: flex;
  flex-direction: column;
  width: 100%;
  gap: 8rpx;
}

/* 按钮分组 */
.button-group {
  margin-bottom: 16rpx;
  background: white;
  padding: 12rpx;
  border-radius: 12rpx;
  box-shadow: 0 2rpx 8rpx rgba(0, 0, 0, 0.05);
}

button {
  width: 100%;
  padding: 16rpx;
  font-size: 26rpx;
  background: #f5f6fa;
  color: #2c3e50;
  border: none;
  border-radius: 6rpx;
  text-align: center;
  margin-bottom: 8rpx;
  transition: all 0.2s ease;
}

button:active {
  transform: scale(0.98);
  background: #e8e8e8;
}

/* 主要操作按钮 */
.primary-button {
  background: linear-gradient(135deg, #007aff, #0056b3);
  color: white;
}

/* 必须操作按钮 */
.required-button {
  background: linear-gradient(135deg, #f5365c, #d63031);
  color: white;
}

/* 数据操作按钮 */
.data-button {
  background: linear-gradient(135deg, #2ecc71, #27ae60);
  color: white;
}

.device-list {
  margin: 16rpx 0;
  width: 100%;
}

.device-item {
  padding: 16rpx;
  background-color: white;
  border-radius: 12rpx;
  margin-bottom: 12rpx;
  box-shadow: 0 2rpx 8rpx rgba(0, 0, 0, 0.05);
  display: flex;
  flex-direction: column;
  gap: 8rpx;
}

.device-item text {
  color: #34495e;
  font-size: 26rpx;
}

.connect-button {
  width: 100%;
  margin-top: 12rpx;
  padding: 16rpx;
  color: #fff;
  background: linear-gradient(135deg, #00b894, #00a884);
  border: none;
  border-radius: 6rpx;
  font-size: 26rpx;
  box-shadow: 0 2rpx 6rpx rgba(0, 184, 148, 0.15);
  text-align: center;
}

.connect-button:active {
  background: linear-gradient(135deg, #00a884, #009874);
}

.footer {
  margin-top: auto;
  padding: 30rpx 0;
  text-align: center;
  font-size: 22rpx;
  color: #95a5a6;
}

.link {
  color: #3498db;
  margin-top: 8rpx;
  text-decoration: none;
  position: relative;
  display: inline-block;
  padding-bottom: 2rpx;
}

.link::after {
  content: '';
  position: absolute;
  width: 100%;
  height: 1rpx;
  bottom: 0;
  left: 0;
  background-color: #3498db;
  transform: scaleX(0);
  transition: transform 0.3s ease;
}

.link:active::after {
  transform: scaleX(1);
}
</style>

