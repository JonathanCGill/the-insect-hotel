# Prove You're an Insect...

Before we can confirm your stay, we need to check that you are, in fact, an
insect. We take this very seriously.

The grid below contains **16 colour blocks**. Some are painted in
**ultraviolet**, a colour that any insect can see clearly, but that is
completely invisible to the human eye.

**Select all the ultraviolet blocks**, then press Submit.

---

<div id="uv-grid" class="uv-grid"></div>

<div style="text-align: center; margin-top: 1.5rem;">
  <p id="uv-hint" class="uv-hint">Select the ultraviolet blocks to continue.</p>
  <button id="uv-submit" class="uv-submit" onclick="submitVerification()">Submit</button>
</div>

<script>
document.addEventListener('DOMContentLoaded', function() {
  const grid = document.getElementById('uv-grid');
  const colours = [
    '#f5f0ff', '#f3eeff', '#f4efff', '#f5f1ff',
    '#f4f0fe', '#f3efff', '#f5f0fe', '#f4effe',
    '#f3f0ff', '#f5efff', '#f4f1ff', '#f3eefe',
    '#f5f0ff', '#f4effe', '#f3f0fe', '#f5effe'
  ];

  for (let i = 0; i < 16; i++) {
    const block = document.createElement('div');
    block.className = 'uv-block';
    block.style.backgroundColor = colours[i];
    block.dataset.index = i;
    block.addEventListener('click', function() {
      this.classList.toggle('selected');
      updateHint();
    });
    grid.appendChild(block);
  }
});

function updateHint() {
  const selected = document.querySelectorAll('.uv-block.selected').length;
  const hint = document.getElementById('uv-hint');
  if (selected === 0) {
    hint.textContent = 'Select the ultraviolet blocks to continue.';
  } else {
    hint.textContent = selected + ' block' + (selected !== 1 ? 's' : '') + ' selected.';
  }
}

function submitVerification() {
  var current = window.location.pathname;
  // Strip trailing slash and last segment (e.g. /verify/ -> /)
  var base = current.replace(/\/[^/]*\/?$/, '/');
  window.location.href = base + 'not-an-insect/';
}
</script>
