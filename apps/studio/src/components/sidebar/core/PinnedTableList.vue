<template>
  <nav class="list-group flex-col">
    <div class="list-heading row">
      <div class="sub row flex-middle expand">
        <div>Pinned <span class="badge">{{orderedPins.length}}</span></div>
      </div>
    </div>
    <Draggable :options="{handle: '.drag-handle'}" v-model="orderedPins" tag="div" ref="pinContainer" class="list-body">
      <div class="pin-wrapper" v-for="p in orderedPins" :key="p.id || p.entity.name">
        <table-list-item
          v-if="p.entityType !== 'routine'"
          :table="p.entity"
          :pinned="true"
          :connection="connection"
          :draggable="true"
          :container="$refs.pinContainer"
          :forceExpand="allExpanded"
          :forceCollapse="allCollapsed"
          @selected="refreshColumns"
          :noSelect="true"
          @contextmenu.prevent.stop="$bks.openMenu({item: p.entity, event: $event, options: tableMenuOptions})"

        />
        <routine-list-item
          v-else
          :container="$refs.pinContainer"
          :draggable="true"
          :routine="p.entity"
          :connection="connection"
          :pinned="true"
          :forceExpand="allExpanded"
          :forceCollapse="allCollapsed"
          @contextmenu.prevent.stop="$bks.openMenu({item: p.entity, event: $event, options: routineMenuOptions})"

        />

      </div>


    </Draggable>
  </nav>
</template>
<script lang="ts">
import Draggable from 'vuedraggable'
import { PinnedEntity } from '@/common/appdb/models/PinnedEntity'
import RoutineListItem from '@/components/sidebar/core/table_list/RoutineListItem.vue'
import TableListItem from '@/components/sidebar/core/table_list/TableListItem.vue'
import Vue from 'vue'
import TableListContextMenus from '@/mixins/TableListContextMenus'
export default Vue.extend({
  components: { RoutineListItem, Draggable, TableListItem },
  mixins: [ TableListContextMenus],
  props: [
    'allExpanded', 'allCollapsed', 'connection'
  ],
  computed: {
    orderedPins: {
      get(): PinnedEntity[] {
        return this.$store.getters['pins/orderedPins']
      },
      set(pins: PinnedEntity[]) {
        this.$store.dispatch('pins/reorder', pins)
      }
    }
  },
  methods: {
    refreshColumns(table) {
      this.$store.dispatch('updateTableColumns', table)
    },
  }

})
</script>
