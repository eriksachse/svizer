# Svizer 📐

A component that creates a container which is resizeable.

Preview: https://svizer.vercel.app/

---

Install:

```bash
npm i svizer
```

---

Usage:

```html
<script>
  import Svizer from "svizer";
</script>

<Svizer
  left={100}
  top={100}
  width={200}
  height={100}
  background={"#f2f2f2"}
  showhandle={true}
  handlecolor={"brown"}
  showcorner={true}
  handlewidth={3}>Test text
</Svizer>
```

---

Roadmap:

- Add min max width height
- Disable overdrag from left to right / top to bottom
- Option to drag the entire container
- Shift click resizes keeping aspect ratio
