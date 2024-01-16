<script>
  import { grid } from "../store/grid.js";
  import { list } from "../store/list.js";
  import Item from "./Item.svelte";

  const getItemById = (id, data) => data.find((item) => item.id === Number(id));

  const allowDrop = (event) => {
    event.preventDefault();
  };

  const handleDragStart = (event) => {
    event.dataTransfer.setData("text", event.target.id);
  };

  const handleDrop = (event) => {
    let idDragElement = event.dataTransfer.getData("text");
    let indexDragElement = $grid.findIndex(
      (item) => item.id === Number(idDragElement)
    );
    let isGridItem = getItemById(idDragElement, $grid);
    let dragElement = getItemById(idDragElement, $grid);

    if (isGridItem) {
      // Set 'isEmpty: true' to show empty state in 'grid' store.
      grid.update((grid) => {
        grid[indexDragElement] = { isEmpty: true };
        return grid;
      });

      // Save drag element in 'list' store.
      list.update((list) => {
        list.splice(0, 0, dragElement);
        return list;
      });
    }
  };
</script>

<ul
  class="flex flex-col basis-1/3 md:basis-1/4 lg:basis-1/5 gap-4 my-4 mr-4 p-4 rounded-2xl border-2 border-gray-300 border-dotted"
  on:drop={handleDrop}
  on:dragover={allowDrop}
>
  <!-- svelte-ignore a11y-no-static-element-interactions -->
  {#each $list as item}
    <li id={item.id} draggable="true" on:dragstart={handleDragStart}>
      <Item content={item.id} />
    </li>
  {/each}
</ul>
