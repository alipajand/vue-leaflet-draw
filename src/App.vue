<template>
  <!--  <l-map-->
  <!--    ref="map"-->
  <!--    :zoom="15"-->
  <!--    :center="[45.551361, -73.469829]"-->
  <!--    style="width: 100vw; height: 100vh; padding: 0; margin: 0"-->
  <!--    :options="{-->
  <!--      zoomControl: false,-->
  <!--      attributionControl: false-->
  <!--    }"-->
  <!--  >-->
  <!--    <l-tile-layer url="https://{s}.tile.osm.org/{z}/{x}/{y}.png" />-->
  <!--  </l-map>-->
  <div></div>
</template>

<script>
import 'leaflet-draw';
import 'leaflet/dist/leaflet.css';
import './styles.scss';

// import { LMap, LTileLayer } from 'vue2-leaflet';

export default {
  // components: {
  //   LMap,
  //   LTileLayer
  // },
  props: {
    position: {
      type: String,
      default: 'topleft'
    },
    refs: {
      type: Object,
      default: null
    }
  },
  data() {
    return {
      map: null,
      editableLayers: null
    };
  },
  watch: {
    map() {
      this.init();
    }
  },
  mounted() {
    this.$nextTick(() => {
      this.map = this.refs || this.$refs.map.mapObject;
    });
  },
  methods: {
    init() {
      if (!this.map) return;

      this.editableLayers = new L.FeatureGroup();
      this.map.addLayer(this.editableLayers);

      const options = {
        position: this.position,
        draw: {
          circle: false,
          marker: false,
          polyline: false,
          rectangle: false,
          circlemarker: false,
          polygon: {
            metric: true,
            showArea: true,
            showLength: true,
            zIndexOffset: 0,
            allowIntersection: false, // Restricts shapes to simple polygons
            drawError: {
              color: '#e1e100', // Color the shape will turn when intersects
              message: '<strong>Oh snap!<strong> you can not draw that!' // Message that will show when intersect
            },
            shapeOptions: {
              color: '#2393f5'
            }
          }
        },
        edit: {
          featureGroup: this.editableLayers, //REQUIRED!!
          remove: true
        }
      };

      const drawControl = new L.Control.Draw(options);
      this.updateLabels();
      this.map.addControl(drawControl);

      this.$nextTick(() => {
        this.createEvents();
      });
    },
    createEvents() {
      this.map.on('draw:created', (e) => {
        const layer = e.layer;
        this.editableLayers.addLayer(layer);
        this.$emit('created', layer);
      });
      this.map.on('draw:edited', (e) => {
        this.$emit('edited', e.layers);
      });
      this.map.on('draw:deleted', (e) => {
        this.$emit('deleted', e.layers);
      });
    },
    updateLabels() {
      L.drawLocal.draw.toolbar.actions.title = 'انصراف';
      L.drawLocal.draw.toolbar.actions.text = 'انصراف';
      L.drawLocal.draw.toolbar.finish.title = 'اتمام';
      L.drawLocal.draw.toolbar.finish.text = 'اتمام';
      L.drawLocal.draw.toolbar.undo.title = 'حذف آخرین نقطه';
      L.drawLocal.draw.toolbar.undo.text = 'حذف آخرین نقطه';
      L.drawLocal.draw.toolbar.buttons.polygon =
        '<span class="mdi mdi-vector-polyline"></span>';

      L.drawLocal.draw.handlers.polygon.tooltip.start =
        'برای شروع روی نقشه کلیک کنید';
      L.drawLocal.draw.handlers.polygon.tooltip.cont =
        'نقطه بعدی را انتخاب کنید';
      L.drawLocal.draw.handlers.polygon.tooltip.end =
        'با کلیک روی نقطه ابتدایی، شکل شما نهایی می‌شود';

      L.drawLocal.edit.toolbar.actions.save.title = 'ذخیره';
      L.drawLocal.edit.toolbar.actions.save.text = 'ذخیره';
      L.drawLocal.edit.toolbar.actions.cancel.title = 'انصراف';
      L.drawLocal.edit.toolbar.actions.cancel.text = 'انصراف';

      L.drawLocal.edit.toolbar.actions.clearAll.title = 'حذف شکل‌ها';
      L.drawLocal.edit.toolbar.actions.clearAll.text = 'حذف شکل‌ها';

      L.drawLocal.edit.toolbar.buttons.edit = 'ویرایش';
      L.drawLocal.edit.toolbar.buttons.editDisabled = 'هیچ شکلی وجود ندارد';
      L.drawLocal.edit.toolbar.buttons.remove = 'حذف';
      L.drawLocal.edit.toolbar.buttons.removeDisabled =
        'شکلی برای حذف وجود ندارد';

      L.drawLocal.edit.handlers.edit.tooltip.text =
        'مربع‌های اضلاع را برای ویرایش حرکت دهید';
      L.drawLocal.edit.handlers.edit.tooltip.subtext = 'انصراف از ویرایش';
      L.drawLocal.edit.handlers.remove.tooltip.text =
        'روی شکلی که قصد حذف آن را دارید کلیک کنید';
    }
  }
};
</script>

<style lang="scss"></style>
