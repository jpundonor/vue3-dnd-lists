<template>
  <DragAndDropList
    :lists="myLists"
    :deleteZone="true"
    :showTitle="true"
    deleteZoneText="🗑️ Drag here to delete"
    :customClasses="customClasses"
  />
  <DragAndDropList
    :lists="uniqueList"
    :deleteZone="true"
    :showTitle="true"
    :customStyles="customStyles"
  />
</template>

<script>
import DragAndDropList from "./components/DragAndDropList.vue";
export default {
  name: "App",
  components: {
    DragAndDropList,
  },
  data() {
    return {
      myLists: [
        {
          title: "To Do",
          items: ["Task 1", "Task 2", "Task 3"],
        },
        {
          title: "In Progress",
          items: ["Task A", "Task B"],
        },
        {
          title: "Done",
          items: [],
        },
      ],
      uniqueList: [
        {
          title: "Unique List",
          items: ["Item 1", "Item 2", "Item 3", "Item 4", "Item 5", "Item 6"],
        },
      ],
      customClasses: {
        container: "my-container-class",
        list: "my-list-class",
        title: "my-title-class",
        item: "my-item-class",
        deleteZone: "my-delete-zone-class",
      },
      customStyles: {
        container: {
          backgroundColor: "#aaa",
          borderRadius: "0.5rem",
          padding: "1rem",
          margin: "1.5rem 0",
        },
        list: {
          width: "500px",
          padding: "1rem",
          backgroundColor: "black",
        },
        title: {
          fontSize: "1.5rem",
          fontWeight: "bold",
          color: "#fff",
        },
        item: {
          padding: "8px 10px",
          margin: "5px 0",
          backgroundColor: "#e59ef7",
          borderBottom: "4px solid #fff",
          borderRadius: "0.3rem",
          cursor: "grab",
          transition: "all 0.5s ease-in-out",
        },
        deleteZone: {
          backgroundColor: "#e59ef75c",
          borderRadius: "0.5rem",
          fontSize: "1.2rem",
          fontWeight: "600",
        },
      },
    };
  },
};
</script>

<style scoped>
.my-container-class {
  display: flex;
  justify-content: center;
  margin: 0;
  gap: 10px;
  padding: 1rem;
  background-color: #adca7276;
  border-radius: 0.5rem;
}
::v-deep(.my-list-class) {
  width: 500px;
  padding: 1rem;
}
::v-deep(.my-title-class) {
  font-size: 1.5rem;
  font-weight: bold;
}
::v-deep(.my-item-class) {
  padding: 10px 10px;
  margin: 5px 0;
  background-color: #fff;
  border-bottom: 1px solid #ddd;
  border-radius: 0.3rem;
  cursor: grab;
  transition: all 0.5s ease-in-out;
}
::v-deep(.my-item-class:hover) {
  background-color: #000000c9;
  color: #fff;
}
::v-deep(.my-delete-zone-class) {
  background-color: #f0f0f0;
  border-radius: 0.5rem;
  font-size: 1.2rem;
  font-weight: 600;
}
</style>
