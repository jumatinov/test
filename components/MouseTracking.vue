<template>
  <div>
    <div v-for="coordinate in coordinates">{{ `id: ${coordinate.id} X: ${coordinate.x}, Y:${coordinate.y}` }}</div>
  </div>
</template>

<script>
export default {
  name: 'MouseTracking',
  data() {
    return {
      coordinates: [],
      winFrameObject: null,
      broadCast: null,
    }
  },
  mounted() {
    document.addEventListener('mousemove', this.trackCoordinates);

    this.broadCast = new BroadcastChannel('mouseTracking');
    this.broadCast.addEventListener('message', (message) => {
      this.coordinates.push(JSON.parse(message.data))
    });
  },
  methods: {
    trackCoordinates(event) {
      const x = event.clientX;
      const y = event.clientY;
      const coordinate = { id: this.uuidv4(), x, y }
      this.broadCast.postMessage(JSON.stringify(coordinate));
      this.coordinates.push(coordinate);
    },
    uuidv4() {
      // для убедительности что координаты уникальные
      return "10000000-1000-4000-8000-100000000000".replace(/[018]/g, c =>
        (c ^ crypto.getRandomValues(new Uint8Array(1))[0] & 15 >> c / 4).toString(16)
      );
    }
  }
}
</script>
