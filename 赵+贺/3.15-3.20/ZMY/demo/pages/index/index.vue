<template>
  <view>
    <uni-forms :modelValue="formData" label-position="top">
      <uni-forms-item label="查询城市" name="name">
        <uni-easyinput type="text" v-model="name" placeholder="请输入城市名字" />
      </uni-forms-item>
      <button @click="submitForm">查询</button>
    </uni-forms>
    <uni-card title="查询结果">
      <text v-if="getweather">城市：{{ name }}\n</text>
      <text v-if="getweather">天气：{{ getweather.weather }}\n</text>
      <text v-if="getweather">温度：{{ getweather.temperature }}\n</text>
      <text v-if="error">查询失败：{{ error.message }}</text>
    </uni-card>
  </view>
</template>

<script>
export default {
  data() {
    return {
      name: '',
      data: null,
      getweather: null,
      error: null,
      address: '西安市雁塔区',
      url1: 'https://restapi.amap.com/v3/geocode/geo?key=563705d1a6e244f1bd38e09b8836463c&address=',
      url2: 'https://restapi.amap.com/v3/weather/weatherInfo?key=563705d1a6e244f1bd38e09b8836463c&city='
    };
  },
  methods: {
    submitForm() {
      this.getdata();
    },
    getdata() {
        // 清空之前的错误信息
        this.error = null;
        
        if (!this.name) {
            uni.showToast({
                title: '请输入城市名',
                icon: 'none'
            });
            return;
        }
        
        // 获取地理编码
        const geocodeUrl = `${this.url1}${encodeURIComponent(this.name)}`;
        uni.request({
            url: geocodeUrl,
            method: 'GET',
            success: res => {
                if (res.data.status === '1' && res.data.geocodes.length > 0) {
                    const adcode = res.data.geocodes[0].adcode;
                    // 使用行政区划代码获取天气信息
                    const weatherUrl = `${this.url2}${adcode}`;
                    uni.request({
                        url: weatherUrl,
                        method: 'GET',
                        success: res => {
                            if (res.data.status === '1' && res.data.lives.length > 0) {
                                this.getweather = res.data.lives[0];
                            } else {
                                this.error = { message: '获取天气信息失败' };
                                uni.showToast({
                                    title: '获取天气信息失败',
                                    icon: 'none'
                                });
                            }
                        },
                        fail: err => {
                            this.error = { message: '请求天气信息出错' };
                            uni.showToast({
                                title: '请求天气信息出错',
                                icon: 'none'
                            });
                        }
                    });
                } else {
                    this.error = { message: '获取地理编码失败' };
                    uni.showToast({
                        title: '获取地理编码失败',
                        icon: 'none'
                    });
                }
            },
            fail: err => {
                this.error = { message: '请求地理编码出错' };
                uni.showToast({
                    title: '请求地理编码出错',
                    icon: 'none'
                });
            }
        });
    }
  }
}
</script>

<style scoped>
</style>