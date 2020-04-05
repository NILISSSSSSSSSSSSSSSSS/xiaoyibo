<script>
import util from './common/util.js';
export default {
	onLaunch: function() {
		//#ifdef APP-PLUS
		/* 5+环境锁定屏幕方向 */
		plus.screen.lockOrientation('portrait-primary'); //锁定
		/* 5+环境升级提示 */
		var server = util.apiurl + 'syj/index/checkupdate'; //检查更新地址
		var req = {
			//升级检测数据
			appid: plus.runtime.appid,
			version: plus.runtime.version,
			imei: plus.device.imei,
			shebei: util.shebei
		};
		uni.request({
			url: server,
			data: req,
			method: 'POST',
			dataType: 'json',
			header: {
				auth: util.httpauth
			},
			success: res => {
				console.log(JSON.stringify(res.data));
				if (res.statusCode == 200 && res.data && res.data.isUpdate) {
					let openUrl = plus.os.name === 'iOS' ? res.data.iOS : res.data.Android;
					uni.showModal({
						//提醒用户更新
						title: '更新提示',
						content: res.data.note ? res.data.note : '是否选择更新',
						success: res => {
							if (res.confirm) {
								plus.runtime.openURL(openUrl);
							}
						}
					});
				}
			}
		});
		uni.getProvider({
			service: 'push',
			success: function(res) {
				// 个推的名称为 igexin
				if (~res.provider.indexOf('igexin')) {
					uni.subscribePush({
						provider: 'igexin',
						success: function(res) {}
					});
				}
			}
		});
		uni.onPush({
			provider: 'igexin',
			callback: function(data) {
				var title = '';
				var jsondata = JSON.parse(data.data);
				for (var a in jsondata) {
					title += a + '=' + jsondata[a];
				}
				uni.showModal({
					title: '提示',
					content: title,
					success: function(res) {
						if (res.confirm) {
						} else if (res.cancel) {
						}
					}
				});
			}
		});
		//#endif
	}
};
</script>
<style>
@import './common/common.css';
@import './common/icon.css';
@import './common/uni.css';
page,
view {
	display: flex;
}
page {
	min-height: 100%;
	background-color: #f1f1f1;
}
.index .card {
	margin: 8upx auto;
	padding-bottom: 10upx;
	padding-top: 10upx;
}
.index .card image,
.index .card video {
	display: block;
	max-width: 96%;
	margin: 8upx auto;
}
.index .card video {
	width: 100%;
}
.index .card .car-title-view {
	font-size: 32upx;
	padding: 20upx 25upx;
	display: block;
}
#mask {
	position: fixed;
	width: 100%;
	height: 100%;
	top: 0;
	left: 0;
	z-index: 99;
	background: rgba(0, 0, 0, 0.5);
	overflow: hidden;
}
#showlogin {
	position: fixed;
	width: 50%;
	left: 50%;
	margin-left: -25%;
	top: 30%;
	z-index: 9999;
}
.iconbottom {
	align-items: center;
	text-align: center;
	justify-content: center;
	margin: 25upx auto 15upx;
}
.iconbottom .iconfont {
	display: inline-block;
	margin: auto 50upx;
	font-size: 30upx;
}
.iconbottom .iconfont.active {
	color: #f44336;
}
.iconbottom .iconfont text {
	padding: 0 10upx;
	color: #777777;
	font-size: 20upx;
}
</style>
