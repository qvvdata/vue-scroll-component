<template>
  <div class="qvvdata_scrollsection">
    <div class="graph_holder" :style="{height: `${graph_holder_height}px`}">
      <div class="graph" v-if="graph_holder_height">
        <slot name="graph"></slot>
      </div>
    </div>
    <div class="scroll_steps">
      <slot></slot>
    </div>
  </div>
</template>

<script>
import * as d3 from 'd3';
import { graphScroll } from '../lib/graph-scroll';

export default {
    name: 'ScrollComponent',
    props: {
    // Miliseconds for regular update via setInterval. No interval if set to 0
        update_interval: {
            type: Number,
            default: 0
        },
    },
    data: () => ({
        graphScroll: null, // future graphScroll instance
        scroll_state: null, // future scroll state
        graph_holder_height: null, // graph holder height, to ensure it's always 100 % of viewport height minus header
    }),
    methods: {
        update_graphScroll() {
            if (this.graphScroll) {
                this.graphScroll
                    .graph(d3.select(this.$el).select('.graph_holder'))
                    .offset(this.$el.clientWidth > 700 ? 120 : 60)
                    .sections(d3.select(this.$el).selectAll('.scroll_steps > .scroll_step'))
                    .container(d3.select(this.$el));
            }
        },
        resize() {
            let h = 450;
            const iw = this.$el.clientWidth;

            if (iw > 700) {
                h = window.innerHeight - 120;
            } else {
                h = window.innerHeight - 60;
            }

            this.graph_holder_height = h;
            // this.width = window.innerWidth;
        }
    },
    watch: {
        scroll_state(o, n) {
            if (o !== n) {
                this.$emit('scroll_change', this.scroll_state);
            }
        }
    },
    mounted() {
        this.graphScroll = graphScroll()
            .on('active', () => {
                const activeEl = d3.select(this.$el).select('.graph-scroll-active');
                if (!activeEl.empty()) {
                    this.scroll_state = activeEl.attr('data-scroll_step');
                }
            });
        this.update_graphScroll();
        window.addEventListener('resize', this.resize);
        setInterval(this.update_graphScroll, 500);
        this.resize();
    },
    updated() {
        this.update_graphScroll();
    }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.qvvdata_scrollsection >>> * {
    box-sizing: border-box;
}

.qvvdata_scrollsection >>> .graph_holder {
  text-align: center;
}

.qvvdata_scrollsection {
    position: relative;
    top: -115px;
    margin-bottom: -115px;
    z-index: 0; /* this is a compromise.
                   -1 would mean it has no impact on the text around it, but would not allow interaction with graph or text
                   0 allows interaction, but since we have a negative top margin, it also disables interaction on content within 115px on top of the scroll section
                */
}

.qvvdata_scrollsection >>> .graph_holder  {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    margin-top: 115px;
    z-index: 49;
}

.qvvdata_scrollsection.graph-scroll-fixed >>> .graph_holder {
    top: 0;
    width: 100%;
    position: fixed;
    z-index: 0;
}

.qvvdata_scrollsection.graph-scroll-below >>> .graph_holder {
    bottom: 0;
    position: absolute;
    top: auto;
    margin: 0;
    margin-bottom: -115px;
}

.qvvdata_scrollsection .scroll_steps {
    /* position: relative; */
    /* z-index: 50; */
    border-top: 115px solid transparent;
    padding-bottom: 10vh; /* fixes margin-caused stutter at bottom */
}

.qvvdata_scrollsection .scroll_steps >>> .scroll_step {
    margin-top: 70vh;
    margin-bottom: 10vh;
    z-index: 50;
    opacity: 0.5;
    position: relative;
}

.qvvdata_scrollsection .scroll_steps >>> .scroll_step:last-child {
    margin-bottom: 0;
}

.qvvdata_scrollsection .scroll_steps .graph-scroll-active.scroll_step {
  opacity: 1;
}

@media screen and (max-width: 520px)  {
  .qvvdata_scrollsection {
      position: relative;
      top: -61px;
      margin-bottom: -61px;
  }
  .qvvdata_scrollsection .scroll_steps {
      border-top: 61px solid transparent;
  }
  .qvvdata_scrollsection >>> .graph_holder {
      margin-top: 61px;
  }
}
</style>
