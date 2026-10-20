<script lang="ts">
    const DEFAULT_COLOR = "#393939";

    let cols = $state(40);
    let rows = $state(20);
    let totalCells = $derived(cols * rows);

    let cells = $state<string[]>([]);
    let isDrawing = $state(false);
    let activeColor = $state("#ffa500");
    let activeTool = $state("pan");

    function resetGrid() {
        cells = new Array(totalCells).fill(DEFAULT_COLOR);
    }

    function paintCell(index: number) {
        if (!isDrawing || activeTool === "pan") return;
        cells[index] = activeTool === "erase" ? DEFAULT_COLOR : activeColor;
    }

    $effect(() => {
        resetGrid();
    });
</script>

<svelte:window onpointerup={() => (isDrawing = false)} />

<main
    class="bg-stone-900 text-stone-100 flex flex-col h-dvh w-screen overflow-hidden"
>
    <!-- Top Bar-->
    <header
        class="print:hidden bg-stone-800 p-3 flex flex-wrap justify-center gap-4 items-center text-sm"
    >
        <button onclick={resetGrid} class="bg-stone-600 px-3 py-1 rounded">
            Reset
        </button>

        <label>
            Cols:
            <input
                class="w-12 bg-stone-900 p-1 text-center rounded"
                bind:value={cols}
                type="number"
                min="1"
                max="50"
            />
        </label>

        <label>
            Rows:
            <input
                class="w-12 bg-stone-900 p-1 text-center rounded"
                bind:value={rows}
                type="number"
                min="1"
                max="50"
            />
        </label>

        <input
            bind:value={activeColor}
            type="color"
            class="h-8 w-8 bg-transparent cursor-pointer"
        />

        <button
            onclick={() => window.print()}
            class="bg-amber-600 px-3 py-1 rounded"
        >
            Print / Save
        </button>
    </header>

    <!-- Canvas -->
    <div
        class="flex-1 overflow-auto p-6 grid place-items-center {activeTool ===
        'pan'
            ? ''
            : 'touch-none'}"
    >
        <div
            class="select-none grid gap-px bg-stone-950 p-px w-max h-max grid-rows-(--rows) grid-cols-(--cols)"
            style:--rows="repeat({rows}, 28px)"
            style:--cols="repeat({cols}, 28px)"
        >
            {#each cells as cellColor, index}
                <div
                    class="w-7 h-7 bg-(--cell-color) print:[print-color-adjust:exact]"
                    style:--cell-color={cellColor}
                    onpointerdown={(e) => {
                        isDrawing = true;
                        e.currentTarget.releasePointerCapture(e.pointerId);
                        paintCell(index);
                    }}
                    onpointerenter={() => paintCell(index)}
                    role="presentation"
                ></div>
            {/each}
        </div>
    </div>

    <!-- Floating Buttons -->
    <div
        class="print:hidden fixed bottom-6 left-1/2 -translate-x-1/2 bg-stone-800 border border-stone-600 p-2 rounded-lg flex gap-2 shadow-xl"
    >
        <button
            onclick={() => (activeTool = "pen")}
            class="px-3 py-2 rounded {activeTool === 'pen'
                ? 'bg-amber-600'
                : ''}"
        >
            🖊️
        </button>
        <button
            onclick={() => (activeTool = "erase")}
            class="px-3 py-2 rounded {activeTool === 'erase'
                ? 'bg-amber-600'
                : ''}"
        >
            🧹
        </button>
        <button
            onclick={() => (activeTool = "pan")}
            class="px-3 py-2 rounded {activeTool === 'pan'
                ? 'bg-amber-600'
                : ''}"
        >
            🖐️
        </button>
    </div>
</main>
