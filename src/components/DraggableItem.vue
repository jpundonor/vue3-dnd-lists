<template>
  <!-- 
    This component represents a draggable item.
    It is used within lists to allow reordering and drag-and-drop functionality.
    -->
  <div
    :class="['draggable-item', customClasses]"
    :style="customStyles"
    draggable="true"
    @dragstart="$emit('dragstart', $event)"
    @dragend="$emit('dragend')"
    ref="draggableItem"
  >
    {{ item }}
  </div>
</template>

<script>
export default {
  name: "DraggableItem",
  props: {
    /**
     * The content to be displayed inside the item.
     * Expected to be a string.
     */
    item: {
      type: String,
      required: true,
    },
    /**
     * The index of the item within its list.
     * This value is used to identify the item's position.
     */
    index: {
      type: Number,
      required: true,
    },
    /**
     * The index of the list that this item belongs to.
     * Helps identify from which list the item is being dragged.
     */
    listIndex: {
      type: Number,
      required: true,
    },
    /**
     * Allows applying custom classes to the element.
     * Used to modify or extend the base styling.
     */
    customClasses: {
      type: String,
      default: "",
    },
    /**
     * Allows applying custom inline styles to the element.
     * Used to modify or extend the base styling.
     */
    customStyles: {
      type: Object,
      default: () => ({}),
    },
  },
};
</script>

<style scoped>
.draggable-item {
  padding: 10px 10px;
  margin: 5px 0;
  background-color: #fff;
  border-bottom: 1px solid #ddd;
  cursor: grab;
  transition: all 0.2s ease-in-out;
}
.draggable-item:hover {
  background-color: #f0f0f0;
  transform: scale(1.05);
}
.draggable-item:active {
  cursor: grabbing;
}
</style>
