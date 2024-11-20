<template>
  <div>
    <!-- 输入框和按钮 -->
    <div style="position: absolute; top: 10px; left: 10px; z-index: 1000; background-color: white; padding: 10px; border-radius: 5px;">
      <input v-model="coordinatesInput" placeholder="请输入电子围栏坐标" />
      <button @click="renderGeofence">确认</button>
    </div>

    <!-- 地图容器 -->
    <div id="map" style="width: 100%; height: 100vh;"></div>
  </div>
</template>

<script>
import { ref, onMounted, onBeforeUnmount } from 'vue';
import L from 'leaflet'; // 引入 Leaflet

export default {
  name: 'MapComponent',
  setup() {
    const map = ref(null); // 地图实例
    const coordinatesInput = ref(''); // 输入框绑定的变量
    let geofenceLayer = null; // 存储电子围栏图层

    // 初始化地图
    const initializeMap = () => {
      map.value = L.map('map').setView([39.90530149493601,116.45157147233039], 15); // 初始中心点
      L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
        attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors',
      }).addTo(map.value);
    };

    // 渲染电子围栏
    const renderGeofence = () => {
      // 获取输入的坐标，假设用户输入的是逗号分隔的经纬度列表
      const input = coordinatesInput.value.trim();
      if (!input) {
        alert('请输入电子围栏坐标');
        return;
      }
      // 39.903269423106885,116.45501969600647;39.90697873663222,116.45528463869285;39.90684819516835,116.44970114805064;39.90300100519302,116.44967998481599;39.903269423106885,116.45501969600647
      // 将输入内容解析为坐标数组
      const coordinates = input.split(';').map(coord => {
        const [lat, lng] = coord.split(',').map(Number);
        return [lat, lng];
      });

      // 验证坐标是否有效
      if (coordinates.some(coord => coord.length !== 2 || isNaN(coord[0]) || isNaN(coord[1]))) {
        alert('请输入有效的坐标');
        return;
      }

      // 如果之前已经有电子围栏图层，先移除
      if (geofenceLayer) {
        map.value.removeLayer(geofenceLayer);
      }

      // 绘制电子围栏
      geofenceLayer = L.polygon(coordinates, {
        color: 'red',
        fillColor: 'red',
        fillOpacity: 0.2,
      }).addTo(map.value).bindPopup('电子围栏');

      // 自动调整地图视野范围
      map.value.fitBounds(geofenceLayer.getBounds());
    };

    onMounted(() => {
      initializeMap(); // 初始化地图
    });

    onBeforeUnmount(() => {
      // 清理图层
      if (geofenceLayer) {
        map.value.removeLayer(geofenceLayer);
      }
    });

    return {
      coordinatesInput,
      renderGeofence,
    };
  },
};
</script>

<style scoped>
#map {
  width: 100%;
  height: 100vh;
}

input {
  padding: 5px;
  margin-right: 5px;
  font-size: 14px;
}

button {
  padding: 5px 10px;
  font-size: 14px;
}
</style>
