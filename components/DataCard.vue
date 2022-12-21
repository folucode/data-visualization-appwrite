<template>
  <div class="chart">
    <div class="chart-inner stat-1">
      <div class="header">
        <div class="tagline">{{ tagline }}</div>
        <div class="count">{{ count }}</div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    tagline: String,
    count: Number,
  },
  name: "DataCard",
};
</script>
