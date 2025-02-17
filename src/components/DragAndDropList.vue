<template>
  <!-- 
    The main container that holds multiple lists.
    Each list can contain multiple draggable items.
    -->
  <div
    :class="['drag-and-drop-container', customClasses.container]"
    :style="customStyles.container"
  >
    <!-- 
      Loop through each list in the "lists" prop.
      'listIndex' is use to identify the current list when dragging an item.
      -->
    <div
      v-for="(list, listIndex) in lists"
      :key="listIndex"
      :class="['drag-and-drop-lists', customClasses.list]"
      :style="customStyles.list"
      :data-list-index="listIndex"
      @dragover.prevent="onDragOver($event, listIndex)"
      @drop="onDrop(listIndex)"
      ref="listContainer"
    >
      <!-- 
        Conditionally render the title of the list if "showTitle" is true.
        The text can be customized using "customClasses.title" or "customStyles.title".
        -->
      <h2
        v-if="showTitle"
        :class="['drag-and-drop-title', customClasses.title]"
        :style="customStyles.title"
      >
        {{ list.title }}
      </h2>
      <!-- 
        Render each draggable item within the current list.
        We pass the "item", its "index", and the parent list index to the DraggableItem component.
        -->
      <DraggableItem
        v-for="(item, itemIndex) in list.items"
        :key="itemIndex"
        :item="item"
        :index="itemIndex"
        :listIndex="listIndex"
        :customClasses="customClasses.item"
        :customStyles="customStyles.item"
        ref="draggableItem"
        @dragstart="onDragStart(listIndex, itemIndex)"
        @dragend="onDragEnd"
        @touchstart.passive="onTouchstart(listIndex, itemIndex, $event)"
        @touchend.passive="onTouchEnd($event)"
      />
      <!-- 
        If "deleteZone" is true, render a delete zone below the list.
        When an item is dragged over and dropped here, it will be removed from the list.
        -->
      <div
        v-if="deleteZone"
        v-show="draggingListIndex === listIndex"
        :class="['delete-zone', customClasses.deleteZone]"
        :style="customStyles.deleteZone"
        ref="deleteZone"
        @dragover.prevent
        @drop="onDeleteDrop()"
      >
        {{ deleteZoneText }}
      </div>
    </div>
  </div>
</template>

<script>
import DraggableItem from "./DraggableItem.vue";

export default {
  name: "DragAndDropList",
  /**
   * Register the DraggableItem component to handle individual items.
   */
  components: {
    DraggableItem,
  },
  props: {
    /**
     * An array of objects representing the list to be displayed.
     * Each list should have a "title" and an "items" array.
     * Example of a single list object:
     * {
     *  title: "To Do",
     *  items: ["Task 1", "Task 2", "Task 3"]
     * }
     */
    lists: {
      type: Array,
      required: true,
    },
    /**
     * Determines whether the delete zone is shown or not.
     * If true, items can be dropped into the delete zone to remove them from the list.
     */
    deleteZone: {
      type: Boolean,
      default: true,
    },
    /**
     * Determines whether the title is shown or not.
     */
    showTitle: {
      type: Boolean,
      default: true,
    },
    /**
     * Text displayed in the delete zone.
     */
    deleteZoneText: {
      type: String,
      default: "🗑️",
    },
    /**
     * Allows the parent to apply custom classes to different parts of the component:
     * container, list, title, item, and deleteZone.
     */
    customClasses: {
      type: Object,
      default: () => ({
        container: "",
        list: "",
        title: "",
        item: "",
        deleteZone: "",
      }),
    },
    /**
     * Allows the parent to apply custom inline styles to different parts of the component:
     * container, list, title, item, and deleteZone.
     */
    customStyles: {
      type: Object,
      default: () => ({
        container: {},
        list: {},
        title: {},
        item: {},
        deleteZone: {},
      }),
    },
  },

  /**
   *  The component's internal reactive data.
   */
  data() {
    return {
      draggedItem: null, // The item currently being dragged.
      draggingListIndex: null, // The index of the list from which the item is being dragged.
      draggingItemIndex: null, // The index of the item within its list.
      dropIndex: null, // The calculated position where the item will be dropped.
      touchStartY: null, // Used to track touch events for mobile devices.
    };
  },

  /**
   * The component's methods to handle drag and drop functionality.
   */
  methods: {
    /**
     * Called when a drag operation starts on a item.
     * @param {Number} listIndex - The index of the list being dragged from.
     * @param {Number} itemIndex - The index of the item within the list.
     */
    onDragStart(listIndex, itemIndex) {
      this.draggedItem = this.lists[listIndex].items[itemIndex];
      this.draggingListIndex = listIndex;
      this.draggingItemIndex = itemIndex;
    },

    /**
     * Called when a drag operation ends, regardless of where the item was dropped.
     * Resets the drag-related state.
     */
    onDragEnd() {
      this.resetDragState();
    },

    /**
     * Called when an item is dragged over a potential drop target.
     * @param {Event} event - The dragover event.
     */
    onDragOver(event, listIndex) {
      this.calculateDropIndex(event, listIndex);
    },

    /**
     * Calculates the position (dropIndex) where the dragged item should be inserted.
     * This is based on the Y coordinate of the mouse or touch event relative to the items.
     * @param {Event} event - The dragover or trouchmove event.
     */
    calculateDropIndex(event, listIndex) {
      // Make sure the event is treated as passive to improve scrolling performance.
      event.passive = true;

      const clientY = event.changedTouches
        ? event.changedTouches[0].clientY
        : event.clientY;

      // Identify wich list is beging hovered over.
      const target = this.$refs.listContainer.find((list) => {
        return list.contains(event.target);
      });

      if (target) {
        // Find all DraggableItem references and check their positions.
        const draggableRef = this.$refs.draggableItem.filter((item) => {
          return item.listIndex === listIndex;
        });
        draggableRef.find((item, index) => {
          const rect = item.$refs.draggableItem.getBoundingClientRect();
          const offset = rect.height;

          // If the Y coordinate is within the top and bottom of an item
          // update the dropIndex to insert before or after the item.
          if (clientY >= rect.top && clientY <= rect.top + offset) {
            this.dropIndex = index;
          } else if (clientY >= rect.top + offset && clientY <= rect.bottom) {
            this.dropIndex = index + 1;
          }
        });
      }
    },

    /**
     * Called when the dragged item is dropped on a list.
     * @param {Number} targetListIndex - The index of the list where the item is dropped.
     */
    onDrop(targetListIndex) {
      if (this.isDragging()) {
        // Remove the item from its original list
        const item = this.lists[this.draggingListIndex].items.splice(
          this.draggingItemIndex,
          1
        )[0];

        // Insert the item into the new position
        this.insertItem(targetListIndex, item);
        this.resetDragState();
      }
    },

    /**
     * Called when the dragged item is dropped on the delete zone.
     * Removes the item from the list.
     */
    onDeleteDrop() {
      if (this.isDragging()) {
        this.lists[this.draggingListIndex].items.splice(
          this.draggingItemIndex,
          1
        );
        this.resetDragState();
      }
    },

    /**
     * Helper method to insert an item into the specified list at the calculated dropIndex.
     * @param {Number} listIndex - The target list index.
     * @param {String} item - The item to insert.
     */
    insertItem(listIndex, item) {
      if (this.dropIndex !== null) {
        this.lists[listIndex].items.splice(this.dropIndex, 0, item);
      } else {
        this.lists[listIndex].items.push(item);
      }
    },

    /**
     * Checks if an item is currently being dragged.
     * @returns {Boolean} - True if an item is being dragged, false otherwise.
     */
    isDragging() {
      return (
        this.draggedItem &&
        this.draggingListIndex !== null &&
        this.draggingItemIndex !== null
      );
    },

    /**
     * Resets the drag-related state variables to their default values.
     */
    resetDragState() {
      this.draggedItem = null;
      this.draggingListIndex = null;
      this.draggingItemIndex = null;
      this.dropIndex = null;
      this.touchStartY = null;
    },

    /**
     * Touchstart handler for mobile devices.
     * @param {Number} listIndex - The index of the list.
     * @param {Number} itemIndex - The index of the item.
     * @param {Event} event - The touchstart event.
     */
    onTouchstart(listIndex, itemIndex, event) {
      this.onDragStart(listIndex, itemIndex);
      this.touchStartY = event.touches[0].clientY;
    },

    /**
     * Touchend handler for mobile devices.
     * Detects if the user has dropped the item on the delete zone or within a list.
     * @param {Event} event - The touchend event.
     */
    onTouchEnd(event) {
      const touch = event.changedTouches[0];
      const targetElement = document.elementFromPoint(
        touch.clientX,
        touch.clientY
      );

      // Find the list that contains the target element.
      const targetListContainer = this.$refs.listContainer.find((list) => {
        return list.contains(targetElement);
      });

      // Get the list index of the target element.
      const targetListIdex = targetListContainer
        ? Number(targetListContainer.dataset.listIndex)
        : this.draggingListIndex;

      // Check if the touchend element is within the delete zone.
      const deleteZone = this.$refs.deleteZone.find((zone) => {
        return zone.contains(targetElement);
      });

      // Recalculate the dropIndex based on the final touch position.
      this.calculateDropIndex(event, targetListIdex);

      // If the touchend element is within the delete zone, remove the item.
      if (deleteZone) {
        this.onDeleteDrop();
        return;
      }
      // Otherwise, drop the item in the list.
      this.onDrop(targetListIdex);
    },
  },
};
</script>

<style scoped>
.drag-and-drop-container {
  display: flex;
  gap: 20px;
}
.drag-and-drop-lists {
  margin: 20px;
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 8px;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.3);
  background-color: #f9f9f9;
}
.drag-and-drop-title {
  margin: 0;
  font-weight: bold;
  padding: 10px 0px;
  border-bottom: 1px solid #ddd;
}
.delete-zone {
  text-align: center;
  margin-top: 20px;
  padding: 10px;
  border-radius: 5px;
  border: 1px dashed red;
  color: red;
  background-color: #ff8a8a24;
}
</style>
