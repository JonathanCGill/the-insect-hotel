# The Insect Hotel Network

You didn't think we were the only ones, did you?

Across the world, from botanical gardens to Formula One circuits, humans have
been building places for insects to stay. Some are record-breakers. Some are
works of art. Some are quiet corners in a London square. All of them are real,
and all of them are open for business.

We mapped them. Because if you're a bee with a long commute, it helps to know
where the next hotel is.

---

## The Network

Click any node to learn more. Hover over an edge to see the distance.

<div id="network-container">
  <div id="network-controls">
    <button class="network-btn active" data-filter="all">All</button>
    <button class="network-btn" data-filter="record">Record Holders</button>
    <button class="network-btn" data-filter="garden">Gardens &amp; Parks</button>
    <button class="network-btn" data-filter="art">Art &amp; Design</button>
    <button class="network-btn" data-filter="wildcard">Wildcards</button>
  </div>
  <svg id="network-graph" viewBox="0 0 900 520" xmlns="http://www.w3.org/2000/svg"></svg>
  <div id="network-info"></div>
</div>

<script>
(function() {
  var hotels = [
    // Home
    { id: "home", name: "The Insect Hotel", loc: "Western Cape, South Africa", lat: -34.05, lng: 18.55, cat: "home",
      desc: "Our boutique retreat in the fynbos. The one that started it all — for us, at least.", url: "" },
    // Record holders
    { id: "duror", name: "Highland Titles Bug Hotel", loc: "Duror, Scotland", lat: 56.66, lng: -5.37, cat: "record",
      desc: "Guinness World Record holder — 199.9 m\u00b3. Built from felled sitka spruce, bamboo, clay pipes and bark. Completed March 2022." },
    { id: "warsaw", name: "Warsaw Giant", loc: "Warsaw, Poland", lat: 52.23, lng: 21.01, cat: "record",
      desc: "Previous world record holder at 89.37 m\u00b3. Built by the Polish Association of Developers on International Day for Biological Diversity." },
    { id: "sthelens", name: "St Helens School Hotel", loc: "St Helens, England", lat: 53.45, lng: -2.74, cat: "record",
      desc: "The original record holder at 81.26 m\u00b3 — proof that school projects can change the world." },
    // Gardens & Parks
    { id: "kew", name: "Kew Gardens Bee Tower", loc: "Richmond, London", lat: 51.48, lng: -0.30, cat: "garden",
      desc: "Insect and bee tower at the Royal Botanic Gardens. Also home to the 'Seed Stories' pollinator sculptures at Wakehurst." },
    { id: "russell", name: "Russell Square Bug Hotels", loc: "Bloomsbury, London", lat: 51.52, lng: -0.13, cat: "garden",
      desc: "Community project in Russell Square Gardens, sponsored by local hotels. Free entry, 7:30am \u2013 10pm daily." },
    { id: "paris", name: "Jardin des Plantes", loc: "Paris, France", lat: 48.84, lng: 2.36, cat: "garden",
      desc: "Bee hotel in the botanical garden of the National Museum of Natural History, 5th arrondissement." },
    { id: "denver", name: "Denver Botanic Gardens", loc: "Denver, Colorado", lat: 39.73, lng: -104.96, cat: "garden",
      desc: "Insect hotels attracting solitary native bees and wasps among the Rocky Mountain flora." },
    { id: "phoenix", name: "Desert Botanical Garden", loc: "Phoenix, Arizona", lat: 33.46, lng: -111.94, cat: "garden",
      desc: "Bug hotels on trails near the Butterfly Exhibit and the Center for Desert Living Trail." },
    { id: "sonoma", name: "Sonoma State University", loc: "Rohnert Park, California", lat: 38.34, lng: -122.71, cat: "garden",
      desc: "Student-refurbished insect hotel in the Garden Classroom. Proof that learning and nesting go together." },
    { id: "sydney", name: "Botanic Gardens of Sydney", loc: "Sydney, Australia", lat: -33.86, lng: 151.22, cat: "garden",
      desc: "Insect hotel programme in Australia\u2019s oldest botanical garden." },
    { id: "agder", name: "University of Agder", loc: "Kristiansand, Norway", lat: 58.16, lng: 8.00, cat: "garden",
      desc: "Multiple insect hotels in the botanical garden, including a large one at the Riding Hall." },
    { id: "ottawa", name: "Fletcher Wildlife Garden", loc: "Ottawa, Canada", lat: 45.39, lng: -75.71, cat: "garden",
      desc: "South of the Old Woodlot. Home to leaf-cutter bees and mason bees." },
    // Art & Design
    { id: "masnou", name: "Parc Vallmora Hotel", loc: "El Masnou, Barcelona", lat: 41.48, lng: 2.31, cat: "art",
      desc: "Designed by Batlle i Roig Arquitectura in 2016. Architecture award nominee and pedagogical tool." },
    { id: "utrecht", name: "Air Bee 'n Bee", loc: "Utrecht, Netherlands", lat: 52.09, lng: 5.12, cat: "art",
      desc: "Billboard tower with 200+ hives in a 7,000 m\u00b2 wildflower meadow along the A2 highway. 20 bee species spotted." },
    { id: "va", name: "Please Stand By", loc: "V&A Museum, London", lat: 51.50, lng: -0.17, cat: "art",
      desc: "Marl\u00e8ne Huissoud\u2019s sculptural pollinator hotels, shown at London Design Festival. Temporary installation." },
    // Switzerland circuit
    { id: "lausanne", name: "Beau Rivage Palace", loc: "Lausanne, Switzerland", lat: 46.51, lng: 6.63, cat: "art",
      desc: "Luxury bee hotel inspired by four seasons and local flora, set in the hotel\u2019s park." },
    { id: "basel", name: "Wild Bee Lodges", loc: "Basel, Switzerland", lat: 47.56, lng: 7.59, cat: "art",
      desc: "Five accommodation buildings near the Silo Boutique Hostel. Natural materials, communal living." },
    { id: "zurich", name: "Tiny Dolder Grand", loc: "Zurich, Switzerland", lat: 47.37, lng: 8.54, cat: "art",
      desc: "A miniature replica of the Dolder Grand hotel. Hedgehogs on the ground floor, birds on the rooftop." },
    { id: "lugano", name: "Villa Carona Bee Hotel", loc: "near Lugano, Switzerland", lat: 45.96, lng: 8.94, cat: "art",
      desc: "Home to 10,000\u201330,000 bees in a historic villa setting." },
    // Wildcard
    { id: "suzuka", name: "Buzzin Corner", loc: "Suzuka Circuit, Japan", lat: 34.84, lng: 136.54, cat: "wildcard",
      desc: "11 insect hotels at Turn 2, built by Sebastian Vettel. Kerbs painted black & yellow. Each F1 team customised their own." }
  ];

  // Edges: meaningful connections
  var edges = [
    // Record-breaker chain
    { from: "sthelens", to: "warsaw", label: "Record chain" },
    { from: "warsaw", to: "duror", label: "Record chain" },
    // London triangle
    { from: "kew", to: "russell", label: "London" },
    { from: "russell", to: "va", label: "London" },
    { from: "kew", to: "va", label: "London" },
    // Swiss circuit
    { from: "lausanne", to: "basel", label: "Swiss circuit" },
    { from: "basel", to: "zurich", label: "Swiss circuit" },
    { from: "zurich", to: "lugano", label: "Swiss circuit" },
    { from: "lugano", to: "lausanne", label: "Swiss circuit" },
    // European neighbours
    { from: "paris", to: "russell", label: "Cross-channel" },
    { from: "utrecht", to: "warsaw", label: "Neighbours" },
    { from: "agder", to: "duror", label: "North Sea" },
    { from: "masnou", to: "lausanne", label: "Mediterranean" },
    // US west coast
    { from: "sonoma", to: "phoenix", label: "US West" },
    { from: "denver", to: "phoenix", label: "US Mountain" },
    // Home connections
    { from: "home", to: "sydney", label: "Southern hemisphere" },
    { from: "home", to: "kew", label: "Commonwealth" },
    // Long haul
    { from: "ottawa", to: "denver", label: "Continental" },
    { from: "suzuka", to: "sydney", label: "Pacific" },
  ];

  function haversine(lat1, lng1, lat2, lng2) {
    var R = 6371;
    var dLat = (lat2 - lat1) * Math.PI / 180;
    var dLng = (lng2 - lng1) * Math.PI / 180;
    var a = Math.sin(dLat/2) * Math.sin(dLat/2) +
            Math.cos(lat1 * Math.PI / 180) * Math.cos(lat2 * Math.PI / 180) *
            Math.sin(dLng/2) * Math.sin(dLng/2);
    return R * 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a));
  }

  // Project lat/lng to SVG coords (simple Mercator-ish)
  function project(lat, lng) {
    var x = (lng + 130) / 280 * 860 + 20;
    var y = (1 - (lat + 45) / 115) * 480 + 20;
    return { x: x, y: y };
  }

  var catColors = {
    home: "#BF360C",
    record: "#FF8F00",
    garden: "#558B2F",
    art: "#6A1B9A",
    wildcard: "#0277BD"
  };

  var catLabels = {
    home: "Home",
    record: "Record Holder",
    garden: "Garden & Park",
    art: "Art & Design",
    wildcard: "Wildcard"
  };

  var svg = document.getElementById("network-graph");
  var info = document.getElementById("network-info");
  var hotelMap = {};
  hotels.forEach(function(h) { hotelMap[h.id] = h; });

  function render(filter) {
    svg.innerHTML = "";

    var visible = hotels.filter(function(h) {
      return filter === "all" || h.cat === filter || h.cat === "home";
    });
    var visibleIds = {};
    visible.forEach(function(h) { visibleIds[h.id] = true; });

    // Draw edges
    edges.forEach(function(e) {
      if (!visibleIds[e.from] || !visibleIds[e.to]) return;
      var a = hotelMap[e.from], b = hotelMap[e.to];
      var pa = project(a.lat, a.lng), pb = project(b.lat, b.lng);
      var dist = Math.round(haversine(a.lat, a.lng, b.lat, b.lng));

      var line = document.createElementNS("http://www.w3.org/2000/svg", "line");
      line.setAttribute("x1", pa.x); line.setAttribute("y1", pa.y);
      line.setAttribute("x2", pb.x); line.setAttribute("y2", pb.y);
      line.setAttribute("stroke", "#D7CCC8");
      line.setAttribute("stroke-width", "1.5");
      line.setAttribute("class", "network-edge");

      var hitLine = document.createElementNS("http://www.w3.org/2000/svg", "line");
      hitLine.setAttribute("x1", pa.x); hitLine.setAttribute("y1", pa.y);
      hitLine.setAttribute("x2", pb.x); hitLine.setAttribute("y2", pb.y);
      hitLine.setAttribute("stroke", "transparent");
      hitLine.setAttribute("stroke-width", "12");
      hitLine.setAttribute("style", "cursor:pointer");

      var title = document.createElementNS("http://www.w3.org/2000/svg", "title");
      title.textContent = a.name + " \u2194 " + b.name + ": " + dist.toLocaleString() + " km (" + e.label + ")";
      hitLine.appendChild(title);

      hitLine.addEventListener("mouseenter", function() {
        line.setAttribute("stroke", "#8D6E63");
        line.setAttribute("stroke-width", "2.5");
      });
      hitLine.addEventListener("mouseleave", function() {
        line.setAttribute("stroke", "#D7CCC8");
        line.setAttribute("stroke-width", "1.5");
      });

      svg.appendChild(line);
      svg.appendChild(hitLine);
    });

    // Draw nodes
    visible.forEach(function(h) {
      var p = project(h.lat, h.lng);
      var r = h.cat === "home" ? 8 : 6;
      var g = document.createElementNS("http://www.w3.org/2000/svg", "g");
      g.setAttribute("class", "network-node");
      g.setAttribute("style", "cursor:pointer");

      var circle = document.createElementNS("http://www.w3.org/2000/svg", "circle");
      circle.setAttribute("cx", p.x); circle.setAttribute("cy", p.y);
      circle.setAttribute("r", r);
      circle.setAttribute("fill", catColors[h.cat]);
      circle.setAttribute("stroke", "#FAF9F6");
      circle.setAttribute("stroke-width", "2");

      var label = document.createElementNS("http://www.w3.org/2000/svg", "text");
      label.setAttribute("x", p.x); label.setAttribute("y", p.y - r - 4);
      label.setAttribute("text-anchor", "middle");
      label.setAttribute("fill", "#3E2723");
      label.setAttribute("font-size", "8");
      label.setAttribute("font-family", "Nunito, sans-serif");
      label.textContent = h.name.length > 22 ? h.name.substring(0, 20) + "\u2026" : h.name;

      g.appendChild(circle);
      g.appendChild(label);

      g.addEventListener("click", function() {
        info.innerHTML = '<div class="network-card">' +
          '<span class="network-cat" style="background:' + catColors[h.cat] + '">' + catLabels[h.cat] + '</span>' +
          '<h3>' + h.name + '</h3>' +
          '<p class="network-loc">' + h.loc + '</p>' +
          '<p>' + h.desc + '</p>' +
          '<p class="network-coords">' + Math.abs(h.lat).toFixed(2) + '\u00b0' + (h.lat >= 0 ? 'N' : 'S') +
          ', ' + Math.abs(h.lng).toFixed(2) + '\u00b0' + (h.lng >= 0 ? 'E' : 'W') + '</p>' +
          '</div>';
      });

      svg.appendChild(g);
    });
  }

  // Filter buttons
  document.querySelectorAll(".network-btn").forEach(function(btn) {
    btn.addEventListener("click", function() {
      document.querySelectorAll(".network-btn").forEach(function(b) { b.classList.remove("active"); });
      btn.classList.add("active");
      render(btn.getAttribute("data-filter"));
      info.innerHTML = "";
    });
  });

  render("all");
})();
</script>

---

## The Directory

### :material-trophy: Record Holders

These are the ones that went big. Really big.

| Hotel | Location | Size | Record Held |
|-------|----------|------|-------------|
| **Highland Titles Bug Hotel** | Duror, Scotland | 199.9 m³ | Current Guinness World Record (March 2022) |
| **Warsaw Giant** | Warsaw, Poland | 89.37 m³ | Previous record (May 2021) |
| **St Helens School Hotel** | St Helens, England | 81.26 m³ | Original record holder |

The record-breaker chain spans 2,800 km from northwest England to the Scottish
Highlands via Warsaw. Each one dethroned the last. The insects don't care about
the rankings, but we find it charming.

### :material-flower: Gardens & Parks

Quiet places where the serious conservation work happens.

| Hotel | Location | What lives there |
|-------|----------|------------------|
| **Kew Gardens Bee Tower** | Richmond, London | Solitary bees, via drilled wood and hollow stems |
| **Russell Square Bug Hotels** | Bloomsbury, London | Ladybirds, lacewings, solitary bees |
| **Jardin des Plantes** | Paris, France | Documented bee populations in the 5th arrondissement |
| **Denver Botanic Gardens** | Denver, Colorado | Solitary native bees and wasps |
| **Desert Botanical Garden** | Phoenix, Arizona | Desert-adapted pollinators |
| **Sonoma State University** | Rohnert Park, California | Student-maintained, educational focus |
| **Botanic Gardens of Sydney** | Sydney, Australia | Southern hemisphere outpost |
| **University of Agder** | Kristiansand, Norway | Multiple hotels, large installation at the Riding Hall |
| **Fletcher Wildlife Garden** | Ottawa, Canada | Leaf-cutter bees and mason bees |

### :material-palette: Art & Design

Where architecture meets ecology. Some temporary, all beautiful.

| Hotel | Location | Designer / Initiative |
|-------|----------|----------------------|
| **Parc Vallmora** | El Masnou, Barcelona | Batlle i Roig Arquitectura (2016) |
| **Air Bee 'n Bee** | Utrecht, Netherlands | Billboard tower, 200+ hives, wildflower meadow |
| **Please Stand By** | V&A Museum, London | Marlène Huissoud, London Design Festival |
| **Beau Rivage Palace** | Lausanne, Switzerland | Switzerland Tourism "Bees & Friends" |
| **Wild Bee Lodges** | Basel, Switzerland | "Bees & Friends" |
| **Tiny Dolder Grand** | Zurich, Switzerland | Josua Glünkin, "Bees & Friends" |
| **Villa Carona Bee Hotel** | near Lugano, Switzerland | "Bees & Friends", 10,000–30,000 bees |

### :material-flag-checkered: Wildcards

Because insect hotels turn up in the most unexpected places.

| Hotel | Location | The story |
|-------|----------|-----------|
| **Buzzin Corner** | Suzuka Circuit, Japan | 11 hotels at Turn 2, built by four-time F1 champion Sebastian Vettel. Each team customised their own. Kerbs painted black and yellow. |

---

## Distances

Some selected flights across the network, as the bee flies:

| From | To | Distance |
|------|-----|----------|
| The Insect Hotel | Kew Gardens | 9,680 km |
| The Insect Hotel | Sydney | 11,060 km |
| Kew Gardens | Russell Square | 13 km |
| Russell Square | V&A Museum | 3 km |
| Paris | Russell Square | 340 km |
| Duror | Warsaw | 2,360 km |
| St Helens | Warsaw | 1,640 km |
| Lausanne | Basel | 180 km |
| Basel | Zurich | 85 km |
| Zurich | Lugano | 180 km |
| Lugano | Lausanne | 250 km |
| Utrecht | Warsaw | 1,230 km |
| Suzuka | Sydney | 7,820 km |
| Denver | Phoenix | 940 km |
| Sonoma | Phoenix | 1,050 km |
| Ottawa | Denver | 2,850 km |

---

## How We Built This

Every hotel on this page is real. We verified each one against news reports,
institutional websites, Guinness World Records, or published research. No human
should arrive at a field to find nothing, and no insect either.

Some installations — particularly art pieces like Marlène Huissoud's *Please
Stand By* at the V&A — may have been temporary. We've noted this where known.

If you know of an insect hotel we've missed, we'd love to hear about it. The
network is always growing.

<div class="references" markdown>

### Sources

1. [Guinness World Records — Largest insect hotel](https://www.guinnessworldrecords.com/world-records/430241-largest-insect-hotel-house)
2. [Highland Titles — World's Biggest Bug Hotel (2022)](https://www.highlandtitles.com/blog/worlds-biggest-bug-hotel-2022/)
3. [Notes from Poland — Warsaw insect hotel](https://notesfrompoland.com/2021/05/24/worlds-biggest-insect-hotel-created-in-warsaw/)
4. [Wallpaper — Seed Stories at Kew / Wakehurst](https://www.wallpaper.com/design-interiors/seed-stories-kew-gardens)
5. [Wild West End — Russell Square Insect Hotels](http://www.wildwestend.london/stories-feed/2017/4/26/insect-hotel)
6. [Bloomsbury Squares — Insect hotels for Russell Square](https://bloomsburysquares.com/2019/03/18/insect-hotels-for-russell-square-gardens/)
7. [Closer Cities — Air Bee 'n Bee, Utrecht](https://closercities.org/projects/air-bee-bee-hotel-wild-bees)
8. [Switzerland Tourism — Bees & Friends](https://www.myswitzerland.com/en-ch/accommodations/hotels/bees-friends/9-unique-accommodations-for-bees-friends/)
9. [Qatar Tribune — Vettel builds insect hotels at Suzuka](https://www.qatar-tribune.com/article/84251/sports/vettel-builds-insect-hotels-at-japans-suzuka-circuit)
10. [Landezine Award — Parc Vallmora insect hotel](https://landezine-award.com/insect-hotel/)
11. [DesignWanted — Marlène Huissoud interview](https://designwanted.com/marlene-huissoud-interview/)
12. [Wikipedia — Insect hotel](https://en.wikipedia.org/wiki/Insect_hotel)
13. [Pacific Horticulture — Insect Hotels](https://pacifichorticulture.org/articles/insect-hotels/)
14. [Botanic Gardens of Sydney — Insect hotel](https://www.botanicgardens.org.au/discover-and-learn/curious-kids/insect-hotel)
15. [University of Agder — Insect hotel](https://www.uia.no/naturmuseum/english/learning-and-school/insektshotell/)

</div>
