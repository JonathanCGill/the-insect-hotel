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
  <svg id="network-graph" viewBox="0 0 900 400" xmlns="http://www.w3.org/2000/svg"></svg>
  <div id="network-info"></div>
</div>

<script>
(function() {
  var hotels = [
    // Home
    { id: "home", name: "The Insect Hotel", loc: "Western Cape, South Africa", lat: -34.05, lng: 18.55, cat: "home",
      desc: "Our boutique retreat in the fynbos. The one that started it all, for us at least.", url: "index.html" },
    // Record holders
    { id: "duror", name: "Highland Titles Bug Hotel", loc: "Duror, Scotland", lat: 56.66, lng: -5.37, cat: "record",
      desc: "Guinness World Record holder: 199.9 m\u00b3. Built from felled sitka spruce, bamboo, clay pipes and bark. Completed March 2022.",
      url: "https://www.highlandtitles.com/blog/worlds-biggest-bug-hotel-2022/" },
    { id: "warsaw", name: "Warsaw Giant", loc: "Warsaw, Poland", lat: 52.23, lng: 21.01, cat: "record",
      desc: "Previous world record holder at 89.37 m\u00b3. Built by the Polish Association of Developers on International Day for Biological Diversity.",
      url: "https://notesfrompoland.com/2021/05/24/worlds-biggest-insect-hotel-created-in-warsaw/" },
    { id: "sthelens", name: "St Helens School Hotel", loc: "St Helens, England", lat: 53.45, lng: -2.74, cat: "record",
      desc: "The original record holder at 81.26 m\u00b3. Proof that school projects can change the world.",
      url: "https://www.guinnessworldrecords.com/world-records/430241-largest-insect-hotel-house" },
    // Gardens & Parks
    { id: "kew", name: "Kew Gardens Bee Tower", loc: "Richmond, London", lat: 51.48, lng: -0.30, cat: "garden",
      desc: "Insect and bee tower at the Royal Botanic Gardens. Also home to the 'Seed Stories' pollinator sculptures at Wakehurst.",
      url: "https://www.kew.org/" },
    { id: "russell", name: "Russell Square Bug Hotels", loc: "Bloomsbury, London", lat: 51.52, lng: -0.13, cat: "garden",
      desc: "Community project in Russell Square Gardens, sponsored by local hotels. Free entry, 7:30am \u2013 10pm daily.",
      url: "https://bloomsburysquares.com/2019/03/18/insect-hotels-for-russell-square-gardens/" },
    { id: "paris", name: "Jardin des Plantes", loc: "Paris, France", lat: 48.84, lng: 2.36, cat: "garden",
      desc: "Bee hotel in the botanical garden of the National Museum of Natural History, 5th arrondissement.",
      url: "https://www.jardindesplantesdeparis.fr/en" },
    { id: "denver", name: "Denver Botanic Gardens", loc: "Denver, Colorado", lat: 39.73, lng: -104.96, cat: "garden",
      desc: "Insect hotels attracting solitary native bees and wasps among the Rocky Mountain flora.",
      url: "https://www.botanicgardens.org/" },
    { id: "phoenix", name: "Desert Botanical Garden", loc: "Phoenix, Arizona", lat: 33.46, lng: -111.94, cat: "garden",
      desc: "Bug hotels on trails near the Butterfly Exhibit and the Center for Desert Living Trail.",
      url: "https://dbg.org/" },
    { id: "sonoma", name: "Sonoma State University", loc: "Rohnert Park, California", lat: 38.34, lng: -122.71, cat: "garden",
      desc: "Student-refurbished insect hotel in the Garden Classroom. Proof that learning and nesting go together.",
      url: "https://pacifichorticulture.org/articles/insect-hotels/" },
    { id: "sydney", name: "Botanic Gardens of Sydney", loc: "Sydney, Australia", lat: -33.86, lng: 151.22, cat: "garden",
      desc: "Insect hotel programme in Australia\u2019s oldest botanical garden.",
      url: "https://www.botanicgardens.org.au/discover-and-learn/curious-kids/insect-hotel" },
    { id: "agder", name: "University of Agder", loc: "Kristiansand, Norway", lat: 58.16, lng: 8.00, cat: "garden",
      desc: "Multiple insect hotels in the botanical garden, including a large one at the Riding Hall.",
      url: "https://www.uia.no/naturmuseum/english/learning-and-school/insektshotell/" },
    { id: "ottawa", name: "Fletcher Wildlife Garden", loc: "Ottawa, Canada", lat: 45.39, lng: -75.71, cat: "garden",
      desc: "South of the Old Woodlot. Home to leaf-cutter bees and mason bees.",
      url: "https://ofnc.ca/programs/fletcher-wildlife-garden" },
    // Art & Design
    { id: "masnou", name: "Parc Vallmora Hotel", loc: "El Masnou, Barcelona", lat: 41.48, lng: 2.31, cat: "art",
      desc: "Designed by Batlle i Roig Arquitectura in 2016. Architecture award nominee and pedagogical tool.",
      url: "https://landezine-award.com/insect-hotel/" },
    { id: "utrecht", name: "Air Bee 'n Bee", loc: "Utrecht, Netherlands", lat: 52.09, lng: 5.12, cat: "art",
      desc: "Billboard tower with 200+ hives in a 7,000 m\u00b2 wildflower meadow along the A2 highway. 20 bee species spotted.",
      url: "https://closercities.org/projects/air-bee-bee-hotel-wild-bees" },
    { id: "va", name: "Please Stand By", loc: "V&A Museum, London", lat: 51.50, lng: -0.17, cat: "art",
      desc: "Marl\u00e8ne Huissoud\u2019s sculptural pollinator hotels, shown at London Design Festival. Temporary installation.",
      url: "https://designwanted.com/marlene-huissoud-interview/" },
    // Switzerland circuit
    { id: "lausanne", name: "Beau Rivage Palace", loc: "Lausanne, Switzerland", lat: 46.51, lng: 6.63, cat: "art",
      desc: "Luxury bee hotel inspired by four seasons and local flora, set in the hotel\u2019s park.",
      url: "https://www.myswitzerland.com/en-ch/accommodations/hotels/bees-friends/9-unique-accommodations-for-bees-friends/" },
    { id: "basel", name: "Wild Bee Lodges", loc: "Basel, Switzerland", lat: 47.56, lng: 7.59, cat: "art",
      desc: "Five accommodation buildings near the Silo Boutique Hostel. Natural materials, communal living.",
      url: "https://www.myswitzerland.com/en-ch/accommodations/hotels/bees-friends/9-unique-accommodations-for-bees-friends/" },
    { id: "zurich", name: "Tiny Dolder Grand", loc: "Zurich, Switzerland", lat: 47.37, lng: 8.54, cat: "art",
      desc: "A miniature replica of the Dolder Grand hotel. Hedgehogs on the ground floor, birds on the rooftop.",
      url: "https://www.myswitzerland.com/en-ch/accommodations/hotels/bees-friends/9-unique-accommodations-for-bees-friends/" },
    { id: "lugano", name: "Villa Carona Bee Hotel", loc: "near Lugano, Switzerland", lat: 45.96, lng: 8.94, cat: "art",
      desc: "Home to 10,000\u201330,000 bees in a historic villa setting.",
      url: "https://www.myswitzerland.com/en-ch/accommodations/hotels/bees-friends/9-unique-accommodations-for-bees-friends/" },
    // Wildcard
    { id: "suzuka", name: "Buzzin Corner", loc: "Suzuka Circuit, Japan", lat: 34.84, lng: 136.54, cat: "wildcard",
      desc: "11 insect hotels at Turn 2, built by Sebastian Vettel. Kerbs painted black & yellow. Each F1 team customised their own.",
      url: "https://www.qatar-tribune.com/article/84251/sports/vettel-builds-insect-hotels-at-japans-suzuka-circuit" }
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

  // Project lat/lng to SVG coords (padded to avoid label clipping)
  function project(lat, lng) {
    var x = (lng + 130) / 290 * 780 + 60;
    var y = (1 - (lat + 40) / 105) * 340 + 30;
    return { x: x, y: y };
  }

  // Detailed continent outlines [lng, lat] for realistic world map
  var continents = [
    // North America mainland
    [[-130,49],[-126,49],[-124,46],[-124,42],[-120,35],[-118,34],[-117,32],[-115,31],[-112,31],[-110,31],[-108,32],[-106,32],[-104,30],[-103,29],[-100,28],[-98,26],[-97,26],[-97,28],[-96,29],[-94,30],[-92,29],[-90,29],[-89,30],[-85,30],[-84,30],[-83,29],[-82,28],[-82,25],[-81,25],[-80,26],[-80,28],[-81,31],[-79,33],[-76,35],[-76,37],[-74,40],[-72,41],[-71,42],[-70,42],[-70,44],[-67,45],[-67,47],[-65,44],[-60,46],[-60,47],[-64,48],[-66,49],[-69,47],[-70,46],[-72,45],[-75,45],[-77,44],[-79,43],[-82,42],[-84,42],[-84,46],[-82,47],[-80,48],[-83,48],[-85,49],[-88,48],[-90,48],[-92,49],[-95,49],[-97,49],[-100,49],[-105,49],[-110,49],[-115,49],[-120,49],[-123,49],[-125,50],[-127,52],[-130,54],[-133,56],[-135,58],[-138,58],[-142,60],[-147,61],[-150,60],[-152,60],[-155,58],[-160,59],[-163,61],[-165,62],[-168,64],[-166,66],[-164,67],[-160,70],[-156,71],[-148,70],[-142,70],[-138,69],[-136,68],[-132,67],[-128,69],[-120,70],[-113,68],[-110,68],[-108,70],[-100,72],[-95,72],[-90,70],[-87,68],[-85,66],[-83,64],[-80,63],[-79,62],[-78,60],[-80,58],[-82,56],[-80,52],[-82,50],[-85,52],[-88,53],[-90,54],[-92,54],[-93,52],[-90,50],[-87,48],[-85,47],[-82,46],[-80,45],[-77,44],[-75,44]],
    // Central America
    [[-104,23],[-103,20],[-100,19],[-98,19],[-96,18],[-94,18],[-92,17],[-91,18],[-90,16],[-88,16],[-87,14],[-85,14],[-84,11],[-83,10],[-82,9],[-80,9],[-79,8],[-78,9],[-77,8],[-78,9],[-80,9],[-80,10],[-83,11],[-84,14],[-86,16],[-88,18],[-90,20],[-92,19],[-93,19],[-95,20],[-97,20],[-99,22],[-100,22],[-102,21],[-104,23]],
    // South America
    [[-80,10],[-77,8],[-75,11],[-73,12],[-72,11],[-72,10],[-70,12],[-68,11],[-67,10],[-64,10],[-62,11],[-60,11],[-60,8],[-58,7],[-57,6],[-55,6],[-54,4],[-52,4],[-50,2],[-50,0],[-48,-2],[-45,-2],[-42,-3],[-40,-2],[-38,-4],[-36,-5],[-35,-7],[-35,-10],[-37,-12],[-39,-14],[-39,-18],[-41,-22],[-44,-23],[-46,-24],[-48,-26],[-48,-28],[-50,-29],[-51,-30],[-52,-33],[-53,-34],[-54,-35],[-57,-36],[-58,-38],[-62,-39],[-63,-41],[-65,-42],[-66,-45],[-67,-46],[-66,-48],[-67,-50],[-68,-52],[-69,-54],[-68,-55],[-66,-55],[-64,-54],[-70,-52],[-72,-50],[-74,-48],[-75,-46],[-75,-44],[-73,-42],[-72,-40],[-72,-36],[-71,-34],[-71,-30],[-70,-28],[-70,-24],[-70,-22],[-70,-18],[-72,-16],[-76,-14],[-77,-12],[-76,-10],[-77,-8],[-75,-6],[-76,-4],[-78,-3],[-80,0],[-80,2],[-78,4],[-77,6],[-78,8],[-80,10]],
    // Africa
    [[-5,36],[-2,36],[0,36],[2,36],[5,37],[8,37],[10,37],[11,34],[10,32],[10,31],[12,31],[15,32],[20,32],[23,32],[25,32],[28,31],[30,31],[32,31],[33,30],[35,30],[38,28],[41,24],[43,17],[44,12],[46,11],[48,8],[49,6],[50,3],[50,0],[48,-2],[44,-5],[42,-8],[41,-10],[40,-12],[40,-14],[38,-16],[36,-18],[35,-22],[33,-26],[30,-30],[28,-33],[26,-34],[22,-34],[20,-35],[18,-34],[17,-32],[16,-29],[15,-27],[13,-22],[12,-17],[11,-12],[10,-6],[9,-3],[9,0],[9,4],[7,5],[5,5],[3,6],[1,6],[-2,5],[-5,5],[-8,5],[-10,5],[-13,6],[-16,12],[-17,15],[-17,17],[-16,19],[-16,21],[-14,22],[-13,25],[-13,28],[-10,32],[-8,34],[-5,36]],
    // Europe mainland
    [[-9,43],[-8,44],[-5,44],[-2,44],[-1,46],[0,47],[1,48],[2,51],[0,51],[-1,50],[-3,49],[-5,49],[-5,48],[-9,43]],
    // Iberian peninsula
    [[-9,37],[-8,37],[-6,37],[-5,36],[-2,36],[0,38],[0,40],[1,41],[3,42],[3,43],[1,43],[0,43],[-1,44],[-2,44],[-5,44],[-8,44],[-9,43],[-9,40],[-8,39],[-9,37]],
    // Europe cont. + Scandinavia
    [[2,51],[4,52],[7,54],[8,55],[10,55],[10,54],[12,54],[14,54],[16,54],[18,55],[20,55],[22,55],[24,55],[25,57],[22,58],[18,60],[16,60],[14,58],[12,58],[12,60],[10,58],[10,62],[12,64],[14,66],[16,68],[18,69],[20,70],[24,71],[28,71],[30,70],[32,67],[30,62],[28,58],[28,56],[26,56],[24,55]],
    // Mediterranean Europe
    [[3,43],[5,43],[7,44],[8,44],[10,44],[12,44],[14,42],[15,41],[16,39],[16,38],[13,38],[11,38],[8,39],[7,44]],
    // SE Europe / Turkey
    [[20,40],[22,40],[24,39],[26,38],[26,40],[28,41],[30,40],[32,37],[34,36],[36,37],[38,37],[40,38],[42,40],[44,40],[44,42],[42,42],[40,42],[38,42],[36,42],[34,42],[32,42],[30,42],[28,42],[26,42],[24,42],[22,42],[22,40],[20,40]],
    // British Isles
    [[-6,50],[-5,50],[-4,51],[0,51],[1,52],[2,53],[1,54],[0,54],[-1,55],[-2,56],[-3,57],[-5,58],[-3,58],[-2,58],[0,58],[-1,60],[-3,59],[-5,58],[-6,57],[-5,56],[-3,55],[-4,54],[-5,53],[-4,52],[-5,51],[-6,50]],
    [[-10,52],[-9,52],[-8,52],[-6,53],[-7,54],[-8,55],[-10,54],[-10,52]],
    // Asia
    [[30,42],[32,42],[34,42],[36,42],[40,42],[44,42],[48,42],[50,42],[52,42],[54,38],[56,36],[56,26],[56,24],[54,22],[52,24],[50,26],[48,28],[46,30],[44,28],[42,24],[44,18],[46,14],[48,12],[50,10],[52,12],[54,14],[56,16],[60,22],[62,24],[64,25],[66,25],[68,24],[70,22],[72,22],[74,20],[76,16],[78,14],[78,10],[80,8],[80,14],[82,16],[84,18],[86,20],[88,22],[90,22],[92,20],[94,18],[98,16],[100,14],[101,12],[102,8],[103,3],[104,2],[104,10],[106,14],[108,16],[108,22],[110,20],[112,22],[115,22],[118,24],[120,22],[122,25],[118,30],[120,32],[122,30],[122,25],[-118,34]],
    // China/Korea/Russia coast
    [[118,30],[120,32],[118,34],[117,36],[118,38],[120,38],[122,37],[124,38],[126,36],[128,36],[129,35],[130,33],[128,34],[126,34],[124,34],[122,36],[120,36],[118,34],[118,30]],
    // Korea
    [[126,34],[128,36],[129,38],[128,38],[127,36],[126,34]],
    // Japan - Honshu
    [[130,31],[131,33],[132,34],[134,34],[135,35],[136,36],[138,36],[140,36],[141,38],[141,40],[140,42],[140,40],[139,38],[138,36],[136,35],[134,34],[132,33],[130,31]],
    // Japan - Hokkaido
    [[140,42],[141,43],[143,44],[145,44],[145,43],[143,42],[141,42],[140,42]],
    // Russia far east
    [[130,43],[132,44],[135,46],[135,50],[138,48],[140,50],[140,54],[145,50],[150,52],[155,55],[160,60],[155,58],[150,55],[145,48],[142,46],[140,46],[138,44],[135,44],[132,43],[130,43]],
    // SE Asia / Malay peninsula
    [[100,14],[102,8],[103,3],[104,2],[104,4],[102,6],[100,8],[100,14]],
    // India / Sri Lanka
    [[68,24],[70,22],[72,22],[72,20],[74,18],[76,16],[78,12],[80,8],[80,12],[78,16],[76,18],[74,20],[72,22]],
    // Australia
    [[115,-35],[116,-34],[117,-35],[118,-35],[120,-34],[122,-34],[124,-34],[126,-33],[128,-32],[130,-32],[132,-30],[134,-28],[136,-25],[137,-22],[137,-18],[136,-16],[136,-14],[135,-13],[134,-12],[133,-12],[131,-12],[130,-13],[130,-16],[128,-16],[128,-14],[126,-14],[124,-14],[122,-16],[120,-18],[118,-20],[116,-22],[115,-25],[114,-28],[114,-30],[114,-32],[115,-34],[115,-35]],
    // Tasmania
    [[145,-39],[146,-40],[147,-41],[148,-42],[148,-44],[146,-44],[145,-43],[145,-41],[145,-39]],
    // New Zealand North Island
    [[173,-37],[174,-38],[176,-38],[178,-38],[178,-40],[176,-40],[175,-41],[174,-40],[173,-39],[173,-37]],
    // New Zealand South Island
    [[167,-45],[168,-44],[170,-43],[172,-42],[174,-41],[172,-43],[170,-44],[168,-46],[167,-45]],
    // Borneo
    [[109,1],[110,2],[112,2],[115,4],[118,6],[118,4],[117,2],[116,1],[115,0],[112,-1],[110,-1],[109,1]],
    // Indonesia / Sumatra
    [[95,6],[98,4],[100,2],[102,0],[104,-2],[106,-6],[106,-4],[104,-1],[100,0],[98,2],[95,6]],
    // Java
    [[106,-6],[108,-7],[110,-7],[112,-8],[114,-8],[115,-8],[114,-7],[112,-7],[110,-6],[108,-6],[106,-6]],
    // Philippines (Luzon)
    [[120,14],[121,16],[122,18],[122,16],[121,14],[120,14]],
    // Madagascar
    [[44,-12],[46,-14],[48,-16],[49,-18],[50,-22],[49,-24],[47,-26],[44,-24],[44,-20],[44,-16],[44,-12]],
    // Iceland
    [[-22,64],[-20,64],[-18,65],[-16,66],[-14,66],[-14,65],[-16,64],[-18,64],[-20,63],[-22,64]],
    // Greenland
    [[-52,60],[-50,62],[-48,64],[-45,66],[-42,68],[-38,70],[-35,72],[-30,74],[-25,76],[-20,78],[-18,80],[-20,82],[-24,82],[-30,82],[-38,82],[-45,80],[-50,78],[-52,76],[-55,74],[-55,72],[-54,70],[-52,68],[-48,66],[-48,64],[-50,62],[-52,60]]
  ];

  function drawMap() {
    continents.forEach(function(pts) {
      var d = pts.map(function(p, i) {
        var pt = project(p[1], p[0]);
        return (i === 0 ? "M" : "L") + pt.x.toFixed(1) + "," + pt.y.toFixed(1);
      }).join(" ");
      var path = document.createElementNS("http://www.w3.org/2000/svg", "path");
      path.setAttribute("d", d);
      path.setAttribute("fill", "#F5F0EB");
      path.setAttribute("stroke", "#DDD5CC");
      path.setAttribute("stroke-width", "0.6");
      path.setAttribute("stroke-linejoin", "round");
      svg.appendChild(path);
    });
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
    drawMap();

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
        var linkHtml = h.url ? '<p class="network-visit"><a href="' + h.url + '"' +
          (h.url.startsWith("http") ? ' target="_blank" rel="noopener"' : '') +
          '>Visit \u2192</a></p>' : '';
        info.innerHTML = '<div class="network-card">' +
          '<span class="network-cat" style="background:' + catColors[h.cat] + '">' + catLabels[h.cat] + '</span>' +
          '<h3>' + h.name + '</h3>' +
          '<p class="network-loc">' + h.loc + '</p>' +
          '<p>' + h.desc + '</p>' +
          '<p class="network-coords">' + Math.abs(h.lat).toFixed(2) + '\u00b0' + (h.lat >= 0 ? 'N' : 'S') +
          ', ' + Math.abs(h.lng).toFixed(2) + '\u00b0' + (h.lng >= 0 ? 'E' : 'W') + '</p>' +
          linkHtml +
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

Some installations, particularly art pieces like Marlène Huissoud's *Please
Stand By* at the V&A, may have been temporary. We've noted this where known.

If you know of an insect hotel we've missed, we'd love to hear about it. The
network is always growing.

<div class="references" markdown>

### Sources

1. [Guinness World Records: Largest insect hotel](https://www.guinnessworldrecords.com/world-records/430241-largest-insect-hotel-house)
2. [Highland Titles: World's Biggest Bug Hotel (2022)](https://www.highlandtitles.com/blog/worlds-biggest-bug-hotel-2022/)
3. [Notes from Poland: Warsaw insect hotel](https://notesfrompoland.com/2021/05/24/worlds-biggest-insect-hotel-created-in-warsaw/)
4. [Wallpaper: Seed Stories at Kew / Wakehurst](https://www.wallpaper.com/design-interiors/seed-stories-kew-gardens)
5. [Wild West End: Russell Square Insect Hotels](http://www.wildwestend.london/stories-feed/2017/4/26/insect-hotel)
6. [Bloomsbury Squares: Insect hotels for Russell Square](https://bloomsburysquares.com/2019/03/18/insect-hotels-for-russell-square-gardens/)
7. [Closer Cities: Air Bee 'n Bee, Utrecht](https://closercities.org/projects/air-bee-bee-hotel-wild-bees)
8. [Switzerland Tourism: Bees & Friends](https://www.myswitzerland.com/en-ch/accommodations/hotels/bees-friends/9-unique-accommodations-for-bees-friends/)
9. [Qatar Tribune: Vettel builds insect hotels at Suzuka](https://www.qatar-tribune.com/article/84251/sports/vettel-builds-insect-hotels-at-japans-suzuka-circuit)
10. [Landezine Award: Parc Vallmora insect hotel](https://landezine-award.com/insect-hotel/)
11. [DesignWanted: Marlène Huissoud interview](https://designwanted.com/marlene-huissoud-interview/)
12. [Wikipedia: Insect hotel](https://en.wikipedia.org/wiki/Insect_hotel)
13. [Pacific Horticulture: Insect Hotels](https://pacifichorticulture.org/articles/insect-hotels/)
14. [Botanic Gardens of Sydney: Insect hotel](https://www.botanicgardens.org.au/discover-and-learn/curious-kids/insect-hotel)
15. [University of Agder: Insect hotel](https://www.uia.no/naturmuseum/english/learning-and-school/insektshotell/)

</div>
