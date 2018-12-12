<template>
  <div id="app">
    <div style="height: 15em; background: red;"></div>
    <ScrollComponent
        @scroll_change="current_scroll_step=$event"
    >
        <template slot="graph">
            <div class="graph">
                Test test <b>{{current_scroll_step}}</b>
            </div>
        </template>

        <div class="scroll_step" :data-scroll_step="s" v-for="s in ['Schritt 1','Schritt 2','Schritt 3']" :key="s">
            Step: {{s}}
        </div>
    </ScrollComponent>
    <div style="height: 100vh; background: red;"></div>

  </div>
</template>

<script>
import ScrollComponent from './components/ScrollComponent.vue';

export default {
    name: 'app',
    components: {
        ScrollComponent,
    },
    data: () => ({
        current_scroll_step: 'test',
    })
};
</script>

<style scoped>
.graph {
    height:20vh;
}
</style>
