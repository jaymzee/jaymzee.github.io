---
layout: post
title: Conway's game of life in Rust and WebAssembly
---
based on the [Rust and WebAssembly book tutorial](https://rustwasm.github.io/docs/book/introduction.html)

<canvas id="game-of-life-canvas"></canvas>
<script src="/assets/js/wgol/bootstrap.js"></script>

{% highlight rust %}
let live_neighbors = self.live_neighbor_count(row, col);

let next_cell = match (cell, live_neighbors) {
    // Rule 1: Any live cell with fewer than two live neighbours
    // dies, as if caused by underpopulation.
    (Cell::Alive, x) if x < 2 => Cell::Dead,
    // Rule 2: Any live cell with two or three live neighbours
    // lives on to the next generation.
    (Cell::Alive, 2) | (Cell::Alive, 3) => Cell::Alive,
    // Rule 3: Any live cell with more than three live
    // neighbours dies, as if by overpopulation.
    (Cell::Alive, x) if x > 3 => Cell::Dead,
    // Rule 4: Any dead cell with exactly three live neighbours
    // becomes a live cell, as if by reproduction.
    (Cell::Dead, 3) => Cell::Alive,
    // All other cells remain in the same state.
    (otherwise, _) => otherwise,
};

next[idx] = next_cell;
{% endhighlight %}
