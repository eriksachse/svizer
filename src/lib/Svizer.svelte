<script>
  export let left, top, width, height;
  export let showhandle = true;
  export let showcorner = true;
  export let handlecolor = "red";
  export let cornercolor = "red";
  export let handlewidth = 2;
  export let background = "#e1e1e1";

  function move(element) {
    return {
      destroy() {},
    };
  }

  function resize(element) {
    const right = document.createElement("div");
    right.direction = "east";
    right.classList.add("svizer-grabber");
    right.classList.add("svizer-right");
    const borderRight = document.createElement("div");
    borderRight.classList.add("svizer-border");
    borderRight.style.height = "100%";
    borderRight.style.width = handlewidth + "px";
    borderRight.style.background = showcorner ? handlecolor : "transparent";
    right.appendChild(borderRight);

    const left = document.createElement("div");
    left.direction = "west";
    left.classList.add("svizer-grabber");
    left.classList.add("svizer-left");
    const borderLeft = document.createElement("div");
    borderLeft.classList.add("svizer-border");
    borderLeft.style.height = "100%";
    borderLeft.style.width = handlewidth + "px";
    borderLeft.style.background = showcorner ? handlecolor : "transparent";
    left.appendChild(borderLeft);

    const top = document.createElement("div");
    top.direction = "north";
    top.classList.add("svizer-grabber");
    top.classList.add("svizer-top");
    const borderTop = document.createElement("div");
    borderTop.classList.add("svizer-border");
    borderTop.style.width = "100%";
    borderTop.style.height = handlewidth + "px";
    borderTop.style.background = showcorner ? handlecolor : "transparent";
    top.appendChild(borderTop);

    const bottom = document.createElement("div");
    bottom.direction = "south";
    bottom.classList.add("svizer-grabber");
    bottom.classList.add("svizer-bottom");
    const borderBottom = document.createElement("div");
    borderBottom.classList.add("svizer-border");
    borderBottom.style.width = "100%";
    borderBottom.style.height = handlewidth + "px";
    borderBottom.style.background = showcorner ? handlecolor : "transparent";
    bottom.appendChild(borderBottom);

    const topLeft = document.createElement("div");
    topLeft.direction = "northwest";
    topLeft.classList.add("svizer-grabber");
    topLeft.classList.add("svizer-top-left");

    const topRight = document.createElement("div");
    topRight.direction = "northeast";
    topRight.classList.add("svizer-grabber");
    topRight.classList.add("svizer-top-right");

    const bottomLeft = document.createElement("div");
    bottomLeft.direction = "southwest";
    bottomLeft.classList.add("svizer-grabber");
    bottomLeft.classList.add("svizer-bottom-left");

    const bottomRight = document.createElement("div");
    bottomRight.direction = "southeast";
    bottomRight.classList.add("svizer-grabber");
    bottomRight.classList.add("svizer-bottom-right");

    const grabbers = [
      right,
      left,
      top,
      bottom,
      topLeft,
      topRight,
      bottomLeft,
      bottomRight,
    ];

    let active = null,
      initialRect = null,
      initialPos = null;

    grabbers.forEach((grabber) => {
      element.appendChild(grabber);
      grabber.addEventListener("mousedown", onMousedown);
    });

    function onMousedown(event) {
      active = event.target;
      const rect = element.getBoundingClientRect();
      const parent = element.parentElement.getBoundingClientRect();

      console.log({ rect, parent });

      initialRect = {
        width: rect.width,
        height: rect.height,
        left: rect.left - parent.left,
        right: parent.right - rect.right,
        top: rect.top - parent.top,
        bottom: parent.bottom - rect.bottom,
      };
      initialPos = { x: event.pageX, y: event.pageY };
      active.classList.add("selected");
    }

    function onMouseup(event) {
      if (!active) return;

      active.classList.remove("selected");
      active = null;
      initialRect = null;
      initialPos = null;
    }

    function onMove(event) {
      if (!active) return;

      const direction = active.direction;
      let delta;

      if (direction.match("east")) {
        delta = event.pageX - initialPos.x;
        element.style.width = `${initialRect.width + delta}px`;
      }

      if (direction.match("west")) {
        delta = initialPos.x - event.pageX;
        element.style.left = `${initialRect.left - delta}px`;
        element.style.width = `${initialRect.width + delta}px`;
      }

      if (direction.match("north")) {
        delta = initialPos.y - event.pageY;
        element.style.top = `${initialRect.top - delta}px`;
        element.style.height = `${initialRect.height + delta}px`;
      }

      if (direction.match("south")) {
        delta = event.pageY - initialPos.y;
        element.style.height = `${initialRect.height + delta}px`;
      }
    }

    window.addEventListener("mousemove", onMove);
    window.addEventListener("mouseup", onMouseup);

    return {
      destroy() {
        window.removeEventListener("mousemove", onMove);
        window.removeEventListener("mousemove", onMousedown);

        grabbers.forEach((grabber) => {
          element.removeChild(grabber);
        });
      },
    };
  }

  let grabber = true;
</script>

<main class:hide-grabber={!grabber}>
  <div
    class="box"
    style="left: {left}px; top: {top}px; width: {width}px; height: {height}px; background:{background}"
    use:move
    use:resize
  >
    <slot />
  </div>
</main>

<style>
  .box {
    position: relative;
  }

  :global(.svizer-grabber) {
    position: absolute;
    box-sizing: border-box;
    display: flex;
    justify-content: center;
    align-items: center;
  }
  :global(.svizer-border) {
    pointer-events: none;
  }

  :global(.svizer-grabber.svizer-right) {
    width: 10px;
    height: 100%;
    right: -5px;
    top: 0px;
    cursor: col-resize;
  }
  /* :global(.svizer-handle){
    background: red;
  } */

  :global(.svizer-grabber.svizer-left) {
    width: 10px;
    height: 100%;
    left: -5px;
    top: 0px;
    cursor: col-resize;
  }

  :global(.svizer-grabber.svizer-top) {
    height: 10px;
    width: 100%;
    top: -5px;
    cursor: row-resize;
  }

  :global(.svizer-grabber.svizer-bottom) {
    height: 10px;
    width: 100%;
    bottom: -5px;
    cursor: row-resize;
  }

  :global(.svizer-grabber.svizer-top-left) {
    height: 20px;
    width: 20px;
    background: orange;
    top: -10px;
    left: -10px;
    cursor: nw-resize;
    border-radius: 100%;
  }

  :global(.svizer-grabber.svizer-top-right) {
    height: 20px;
    width: 20px;
    background: orange;
    top: -10px;
    right: -10px;
    cursor: ne-resize;
    border-radius: 100%;
  }

  :global(.svizer-grabber.svizer-bottom-left) {
    height: 20px;
    width: 20px;
    background: green;
    bottom: -10px;
    left: -10px;
    cursor: sw-resize;
    border-radius: 100%;
  }

  :global(.svizer-grabber.svizer-bottom-right) {
    height: 20px;
    width: 20px;
    background: green;
    bottom: -10px;
    right: -10px;
    cursor: se-resize;
    border-radius: 100%;
  }

  :global(.svizer-hide-grabber .svizer-grabber) {
    background: transparent !important;
    border: none !important;
  }

  :global(.svizer-grabber.svizer-selected) {
    border: solid 1px black;
  }
</style>
