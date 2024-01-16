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
    let indexDroppedElement = event.target.closest("li").id;
    let isGridItem = getItemById(idDragElement, $grid);
    let dragElement = getItemById(idDragElement, $list);
    let droppedElement = $grid[indexDroppedElement];

    if (isGridItem) {
      // Swap indexes of drag element and dropped element.
      grid.update((grid) => {
        [grid[indexDragElement], grid[indexDroppedElement]] = [
          grid[indexDroppedElement],
          grid[indexDragElement],
        ];
        return grid;
      });
    }

    if (!isGridItem) {
      // Replace dropped element with drag element in 'grid' store.
      grid.update((grid) => {
        grid[indexDroppedElement] = dragElement;
        return grid;
      });

      // Delete drag element in 'list' store.
      list.update((list) => {
        let index = list.findIndex((item) => item.id === Number(idDragElement));
        list.splice(index, 1);

        if (!droppedElement.isEmpty) {
          list.push(droppedElement);
        }

        return list;
      });
    }
  };
</script>

<ul
  class="place-self-start basis-2/3 md:basis-3/4 lg:basis-4/5 grid grid-cols-2 lg:grid-cols-4 gap-4 p-4"
>
  <!-- svelte-ignore a11y-no-static-element-interactions -->
  {#each $grid as item, index}
    <li
      id={index}
      class="p-4 rounded-2xl border-2 border-gray-300 border-dotted"
      on:drop={handleDrop}
      on:dragover={allowDrop}
    >
      <div id={item.id} draggable="true" on:dragstart={handleDragStart}>
        {#if !item.isEmpty}
          <Item content={item.id} />
        {/if}
      </div>
    </li>
  {/each}
</ul>
