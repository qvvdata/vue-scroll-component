# vue-scroll-component

A Vue component that wraps `graph-scroll.js`

### Component Slots

The default slot is used for the Scroll Steps – the content that scrolls past the graph.
Every scroll step should have a `data-scroll-step` Attribute.

An additional `graph` slot holds the content that the Scroll Steps scroll past. It is positioned in a way that imitates `position: sticky`,
however header sizes for Addendum.org are considered. The `graph` is always full height – minus the header size.

The ScrollComponent emits a `scroll_change` event, which is emitted when the currently active Scroll Step changes. Its parameter/event is the value of the currently active's Scroll Step `data-scroll-step` attribute.

### Importing
Import the ScrollComponent using
```
import ScrollComponent from 'vue-scroll-component';
```
The library exports `src/components/ScrollComponent.vue` Vue Single File Component by default, so the component can be precompiled.

### Example
```
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
```

### Developing
```
npm run serve
```
