<script type="text/javascript">
  import { onMount } from 'svelte'

  let go = true
  let width = 17
  let fn = (m,i,r,c) => i === 0 || !!(i && !(i%2))
  let fnVal = 'i === 0 || !!(i && !(i%2))'
  let M = new Map([])

  const wait = ms => new Promise(r => setTimeout(r, ms))

  const seedRow = (width) => async (M, fn) => {
    let r = M.size + 1
    let row = [...new Array(width)]
      .map(() => false)
      .map((val, c) => {
        let i = (r - 1) * width + c
        return fn(M, i, r, c)
      })
    M.set(r, row)
    return M
  }

  let appendRow = seedRow(width)

  const run = async (append, m, fn) => {
    M = await append(m, fn)
    while(go) {
      await wait(300)
      M = await append(M, fn)
    }
  }

  onMount(async () => {
    run(appendRow, M, fn)
  })


</script>

<main>
  <output style="--width: {width}">

    {#if M.size > 0}
      {#each [...M.entries()] as row, r}
        {#each [...M.get(r + 1)] as val, c}
          <input
            disabled=true
            type="checkbox"
            checked={val}>
        {/each}
      {/each}
    {/if}
  </output>

  <form>
<!--     <input
      min="2"
      max="32"
      type="range"
      name="width"
      bind:value={width}> -->
    <label>
      (m,i,r,c) =>
    </label>
    <textarea
      autocomplete="off"
      autocapitalize="none"
      spellcheck="false"
      autocorrect="off"
      bind:value={fnVal} />
  </form>

  <label>
    Stop
    <input type="checkbox" name="pause" bind:checked={go}>
  </label>
</main>


<style type="text/css">
  main {
    max-width: 18rem;
    margin-inline: auto;
  }

  input,
  textarea {
    display: block;
    width: 100%;
  }

  input {
    padding: 0;
    margin: 0;
  }

  output {
    display: grid;
    grid-template-columns: repeat(var(--width), 1fr);
    height: 24.5rem;
    align-content: end;
    margin-top: 4rem;
    overflow-block: hidden;
/*    scroll-snap-type: y proximity;*/
  }
  output::after {
/*    display: block;*/
/*    content: "";*/
/*    scroll-snap-align: end;*/
  }
</style>