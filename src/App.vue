<template>
  <div id="app">
    <div class="container-fluid px-0">
      <div id="map"></div>
      <span class="information" @click="openModal()">
        <i class="fas fa-exclamation-circle fa-3x text-primary"></i>
      </span>
      <div class="slideBox--toggle"
      :class="{ 'IconClose' : toggleBox === true }"
      @click="toggleBox = !toggleBox"
      >
        <i class="fas"
        :class="{ 'fa-chevron-left': toggleBox === false, 'fa-chevron-right': toggleBox === true }">
        </i>
      </div>
      <div class="slideBox"
        :class="{ 'slideClose' : toggleBox === true }">
        <div class="slideBox__title">
          <div class="clearfix mb-1">
            <h2 class="float-left mb-0 mr-0 mr-sm-5">星期{{ day }}</h2>
            <div class="float-right">
              <p class="text-right mb-0">{{ date }}</p>
              <p class="mb-0" v-if="day === '一' || day === '三' || day === '五'">身分證末碼
                <strong class="numStyle">1,3,5,7,9</strong> 可購買</p>
              <p class="mb-0" v-else-if="day === '二' || day === '四' || day === '六'">身分證末碼
                <strong class="numStyle">2,4,6,8,0</strong> 可購買</p>
              <p class="mb-0" v-else><strong class="numStyle">今日皆可購買</strong></p>
            </div>
          </div>
          <p class="mb-1">防疫專線 1922 | 口罩資訊 1911</p>
          <label for="county" class="sr-only">縣市</label>
          <select
            name="county"
            id="county"
            class="form-control mb-2"
            v-model="select.city"
            @change="select.area = ''"
          >
            <option value="" disabled selected>---請選擇縣市---</option>
            <option :value="c.CityName" v-for="c in cityName" :key="c.CityName">
              {{ c.CityName }}
            </option>
          </select>
          <label for="town" class="sr-only">地區</label>
          <select
            name="town"
            id="town"
            class="form-control"
            v-model="select.area"
            v-if="select.city.length"
            @change="updateSelect"
          >
            <option value="" disabled selected>---請選擇地區---</option>
            <option :value="a.AreaName"
              v-for="a in cityName.find((city) => city.CityName === select.city).AreaList"
              :key="a.AreaName"
            >{{ a.AreaName }}</option>
          </select>
        </div>
        <div class="card">
          <div class="card__header">
            <ul class="nav nav-tabs">
              <li class="nav-item">
                <a
                  href="#"
                  class="nav-link"
                  @click="search = 'pharmacy'"
                  :class="{ 'active' : search === 'pharmacy' }"
                >
                  藥局
                </a>
              </li>
              <li class="nav-item">
                <a
                  href="#"
                  class="nav-link"
                  @click="search = 'star'"
                  :class="{ 'active' : search === 'star' }"
                >
                  收藏
                </a>
              </li>
            </ul>
          </div>
          <ul class="list-group pharmacy__group">
            <template v-for="(i, k) in filterData">
              <a href="#" class="list-group-item pharmacy__group--style" :key="k"
                v-if="i.properties.county === select.city
                && i.properties.town === select.area"
                @click="penTo(i)"
              >
                <a href="#" @click.prevent="setStared(i)">
                  <i class="fa-star fa-2x text-accent" :class="starColor(i)"></i>
                </a>
                <h3>{{ i.properties.name }}</h3>
                <a :href="`https://www.google.com.tw/maps/place/${i.properties.address}`"
                target="_blank" title="Google Map">{{ i.properties.address }}</a>
                <p class="mb-1">{{ i.properties.phone }}</p>
                <div class="d-flex justify-content-between">
                  <span class="btn btn-primary mask-button mr-2">
                    成人口罩 {{ i.properties.mask_adult }}
                  </span>
                  <span class="btn btn-secondary mask-button">
                    兒童口罩 {{ i.properties.mask_child }}
                  </span>
                </div>
              </a>
            </template>
          </ul>
        </div>
      </div>
    </div>
    <!-- Modal -->
    <div class="modal fade" id="maskModal" tabindex="-1" role="dialog"
    aria-labelledby="exampleModalLabel" aria-hidden="true">
      <div class="modal-dialog" role="document">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title" id="exampleModalLabel">3/5 最新公告</h5>
            <button type="button" class="close" data-dismiss="modal" aria-label="Close">
              <span aria-hidden="true">&times;</span>
            </button>
          </div>
          <div class="modal-body">
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import L from 'leaflet';
import moment from 'moment';
import $ from 'jquery';

import cityName from './assets/cityName.json';

let osmMap = {};

const iconsConfig = {
  iconSize: [25, 41],
  iconAnchor: [12, 41],
  popupAnchor: [1, -34],
  shadowSize: [41, 41],
};
const icons = {
  green: new L.Icon({
    iconUrl: 'https://cdn.rawgit.com/pointhi/leaflet-color-markers/master/img/marker-icon-2x-green.png',
    ...iconsConfig,
  }),
  grey: new L.Icon({
    iconUrl: 'https://raw.githubusercontent.com/pointhi/leaflet-color-markers/master/img/marker-icon-grey.png',
    ...iconsConfig,
  }),
};

const osm = {
  addMapMarker(x, y, item) {
    const icon = item.mask_adult || item.mask_child ? icons.green : icons.grey;
    L.marker([y, x], {
      icon,
    }).addTo(osmMap).bindPopup(`
                <h3>${item.name}</h3>
                <a href="https://www.google.com.tw/maps/place/${item.address}"
                target="_blank" title="Google Map">${item.address}</a><br>
                <p class="my-1">${item.phone}</p>
                <div class="d-flex justify-content-between">
                  <span class="btn btn-primary mask-button-popup mr-2">
                    成人口罩 ${item.mask_adult}
                  </span>
                  <span class="btn btn-secondary mask-button-popup">
                    兒童口罩 ${item.mask_child}
                  </span>
                </div>`);
  },
  removeMapMarker() {
    osmMap.eachLayer((layer) => {
      if (layer instanceof L.Marker) {
        osmMap.removeLayer(layer);
      }
    });
  },
  penTo(x, y, item) {
    const icon = item.mask_adult || item.mask_child ? icons.green : icons.grey;
    osmMap.panTo(new L.LatLng(y, x));
    L.marker([y, x], {
      icon,
    }).addTo(osmMap).bindPopup(`
                <h3>${item.name}</h3>
                <a href="https://www.google.com.tw/maps/place/${item.address}"
                target="_blank" title="Google Map">${item.address}</a><br>
                <p class="my-1">${item.phone}</p>
                <div class="d-flex justify-content-between">
                  <span class="btn btn-primary mask-button-popup mr-2">
                    成人口罩 ${item.mask_adult}
                  </span>
                  <span class="btn btn-secondary mask-button-popup">
                    兒童口罩 ${item.mask_child}
                  </span>
                </div>`).openPopup();
  },
};

export default {
  name: 'App',
  data() {
    return {
      date: '',
      day: '',
      data: [],
      cityName,
      select: {
        city: '新北市',
        area: '五股區',
      },
      toggleBox: false,
      search: 'pharmacy',
      stared: [],
      staredLocalStor: JSON.parse(localStorage.getItem('pharmacyStaredData')) || [],
    };
  },
  methods: {
    getMaskData() {
      const api = 'https://raw.githubusercontent.com/kiang/pharmacies/master/json/points.json';
      this.$http.get(api).then((res) => {
        this.data = res.data.features;
        this.updateMarker();
        this.getLocalStorage();
      });

      osmMap = L.map('map', {
        center: [25.0722973, 121.4316257],
        zoom: 16,
      });

      L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
        attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors',
      }).addTo(osmMap);
    },
    getDate() {
      moment.locale('zh-cn');
      const date = moment().format('L');
      const day = moment().format('dddd').substring(2);
      this.date = date;
      this.day = day;
    },
    updateMarker() {
      const pharmacies = this.data.filter((pharmacy) => {
        if (!this.select.area) {
          return pharmacy.properties.county === this.select.city;
        }
        return pharmacy.properties.town === this.select.area
        && pharmacy.properties.county === this.select.city;
      });
      pharmacies.forEach((pharmacy) => {
        const { properties, geometry } = pharmacy;
        osm.addMapMarker(
          geometry.coordinates[0],
          geometry.coordinates[1],
          properties,
        );
      });
      this.penTo(pharmacies[0]);
    },
    removeMarker() {
      osm.removeMapMarker();
    },
    penTo(pharmacy) {
      const { properties, geometry } = pharmacy;
      osm.penTo(geometry.coordinates[0], geometry.coordinates[1], properties);
    },
    updateSelect() {
      this.removeMarker();
      this.updateMarker();
      const pharmacy = this.data.find((item) => item.properties.town === this.select.area
      && item.properties.county === this.select.city);
      const { geometry, properties } = pharmacy;
      osm.penTo(geometry.coordinates[0], geometry.coordinates[1], properties);
    },
    setStared(item) {
      const vm = this;
      if (vm.stared.indexOf(item) === -1) {
        vm.stared.push(item);
      } else {
        vm.stared.splice(vm.stared.indexOf(item), 1);
      }

      vm.staredLocalStor = vm.stared.map((i) => i.properties.name);

      localStorage.setItem('pharmacyStaredData', JSON.stringify(vm.staredLocalStor));
    },
    getLocalStorage() {
      const vm = this;
      vm.staredLocalStor.forEach((item) => {
        vm.data.forEach((data) => {
          if (item === data.properties.name) {
            vm.stared.push(data);
          }
        });
      });
    },
    openModal() {
      $('#maskModal').modal('show');
    },
  },
  computed: {
    filterData() {
      if (this.search === 'star') {
        return this.stared;
      }
      return this.data;
    },
    starColor() {
      return function starColor(item) {
        if (this.stared.indexOf(item) === -1) {
          return 'far';
        }
        return 'fas';
      };
    },
  },
  mounted() {
    this.getMaskData();
    this.getDate();
  },
};
</script>

<style lang="scss">
@import './assets/scss/all';

a:hover {
  text-decoration: none;
}

select {
  appearance: none;
}

.leaflet-popup-content-wrapper {
  border-radius: 0 !important;
}

#map {
  height: 100vh;
}

.information {
  position: fixed;
  bottom: 0;
  right: 0;
  z-index: 1000;
}

.modal-body {
  width: 100%;
  height: 500px;
  background: url('./assets/images/mask.jpg') center;
  background-repeat: no-repeat;

  @media (max-width: 575px) {
    height: 320px;
    background: url('./assets/images/mask-sm.jpg') center;
    background-repeat: no-repeat;
  }
}

.slideBox {
  position: fixed;
  top: 0;
  bottom: 0;
  width: 340px;
  overflow-y: auto;
  z-index: 1000;
  transition: transform .5s ease-out;

  &--toggle {
    width: 20px;
    height: 50px;
    background-color: $primary;
    position: fixed;
    top: 162px;
    left: 340px;
    z-index: 1000;
    transition: left .5s ease-out;

    i {
      color: #fff;
      position: absolute;
      top: 50%;
      left: 70%;
      transform: translate(-70%, -50%);
    }
  }

  &__title {
    background-color: $primary;
    color: #fff;
    overflow: hidden;
    padding: 10px;

    &::after {
      display: block;
      clear: both;
      content: "";
    }
  }
}

.slideClose {
  transform: translateX(-340px);
}

.IconClose {
  left: 0;
}

.float-right {
  p:nth-child(1) {
    font-size: 12px;
  }

  p:nth-child(2) {
    font-size: 8px;
  }
}

.numStyle {
  color: $accent;
  font-size: 16px;
}

.mask-button {
  width: 100%;
  color: #fff;
  @media (max-width: 575px) {
    font-size: 14px;
  }

  &-popup {
    color: #fff;
  }
}

.list-group-item {
  h3 {
    color: #333333;
  }

  p, a {
    color: #666666;
  }
}

.card {
  &__header {
    padding: 0.75rem 1.25rem 0;
  }
}

.pharmacy__group {
  padding: 0.75rem 1.25rem;

  &--style {
    padding: 0 0 1rem 0;
    margin: 1rem 0 0 0;
    border-bottom: 1px solid rgba(0, 0, 0, 0.125);

    &:first-child {
      margin-top: 0;
    }
  }
}

.fa-star {
  position: absolute;
  top: 0;
  right: 0;
}

@media (max-width: 575px) {
  .slideBox {
    max-width: 300px;
    transition: transform .3s ease-out;
  }
  .slideBox--toggle {
    left: 300px;
    transition: left .2s linear;
  }

  .IconClose {
    left: 0px;
  }
}
</style>
