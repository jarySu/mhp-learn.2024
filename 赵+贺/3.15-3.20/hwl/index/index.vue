<template>
  <view>
    <text v-if="weatherData">
      当前天气：{{ weatherData.temperature }}℃
    </text>
    <text v-else>
      正在查询天气...
    </text>
  </view>
</template>
<script>
	export default {
	  data() {
	    return {
			city:'',
	      weatherData: null,
	    };
	  },
	  methods: {
		  getWeather(){
	      uni.request({
	              url: 'https://restapi.amap.com/v3/weather/weatherInfo?',
	              data: {
	                key: 'e28be8b2ac1edc9c7c96ce036278b24d',
					city: this.city,
					extensions: 'base'
					      },
	        success: (res) => {
	          if (res.data.status === '1') {
	            this.weatherData = res.data.live[0].weather;
	            console.log(this.weatherData);
	          } else {
	            console.error('查询天气失败', res.data);
	          }
	        },
	        fail: (err) => {
	          console.error('请求失败', err);
	        },
	      });
	    },
	  },
	  mounted() {
	    this.getWeather('北京'); 
	  },
	};
</script>
