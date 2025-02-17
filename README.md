# Vue3 Drag-and-Drop Lists

[![npm](https://img.shields.io/npm/v/vue3-dnd-lists?color=%2300f)](https://www.npmjs.com/package/vue3-dnd-lists)
[![npm bundle size](https://img.shields.io/bundlephobia/minzip/vue3-dnd-lists)](https://bundlephobia.com/package/vue3-dnd-lists)
![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)

This component provides a reusable Vue component for creating drag-and-drop lists. 
Users can move items between lists and delete items by dragging them to a designated delete zone.

## Features
*   **Drag and Drop:** Easily move items between lists using drag and drop.
*   **Delete Zone:** Delete items by dragging them to a specific delete zone.
*   **Customizable:** Customize the appearance of the lists, titles, items, and delete zone using props for classes and styles.
*   **Mobile-Friendly:** Supports touch events for mobile devices.

## Installation

```bash
npm install vue3-dnd-lists
```


## Requirements

- [Vue 3](https://vuejs.org/) or higher

## Usage
```bash

<template>
  <DragAndDropList 
  :lists="myLists" 
  
</template>

<script>
import DragAndDropList from 'vue3-dnd-list';
export default {
  components: {
    DragAndDropList
  },
  data() {
    return {
      myLists: [
        { title: 'To Do', items: ['Task 1', 'Task 2', 'Task 3'] },
        { title: 'In Progress', items: ['Task 4', 'Task 5'] },
        { title: 'Done', items: ['Task 6'] }
      ],      
      
    };
  }
};
</script>
```

## Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `lists` | Array | Required | An array of objects representing the lists to be displayed.|
| `deleteZone` | Boolean | `true` | Determines whether the delete zone is shown or not. |
| `showTitle` | Boolean | `true` | Determines whether the title is shown or not. |
| `deleteZoneText` | String | `'🗑️'` | Text displayed in the delete zone. |
| `customClasses` | Object | - | Allows the parent to apply custom classes to different parts of the component.|
| `customStyles` | Object | - | Allows the parent to apply custom inline styles to different parts of the component. |


## Methods

The component includes methods to handle drag and drop functionality, such as `onDragStart`, `onDragEnd`, `onDragOver`, `onDrop`, and `onDeleteDrop`.

## Styles

The component comes with basic styles, but you can customize them using the customClasses and customStyles props.

## Customization

1. Clases:
- You can pass custom classes via `customClasses`:

```bash

<template>
  <DragAndDropList 
  :lists="myLists"
  :delete-zone-text="'🗑️ Delete Item'"
  :custom-classes="customClasses" 
  />
</template>

<script>
export default {
  data() {
    return {
      customClasses: {
        container: 'my-container',
        list: 'my-list',
        title: 'my-title',
        item: 'my-item',
        deleteZone: 'my-delete-zone'
      },
    };
  }
};
</script>

<style scoped>
/* Example custom styles */
.my-container { /* Add your container styles here */ }
::v-deep(.my-list) { /* Add your list styles here */ }
::v-deep(.my-title) { /* Add your title styles here */ }
::v-deep(.my-item) { /* Add your item styles here */ }
::v-deep(.my-delete-zone) { /* Add your delete zone styles here */ }
</style>
```

2. Inline Styles:
- You can pass inline styles via `customStyles`:

```bash

<template>
  <DragAndDropList 
  :lists="myLists"
  :custom-styles="customStyles"
  />
</template>

<script>
import DragAndDropList from 'vue3-dnd-list';

export default {
  data() {
    return {
      customStyles: {
        container: { display: 'flex', gap: '30px' },
        list: { backgroundColor: '#e0e0e0' },
        title: { color: 'blue' },
        item: { padding: '8px', border: '1px solid #ccc', borderRadius: '4px'},
        deleteZone: { backgroundColor: '#f443361a', color: '#f44336'}
      }
    };
  }
};
</script>
```

## Contributing

Contributions are welcome! Feel free to open an issue or a pull request if you have any ideas, questions, or suggestions.


# License
MIT © 2025 [Javier Rojas](https://javier-rojas.vercel.app/)