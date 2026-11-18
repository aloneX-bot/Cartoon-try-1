
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>The Beer Flood — 1814</title>
<style>
  * { box-sizing: border-box; }
  html, body {
    margin: 0;
    width: 100%;
    height: 100%;
    overflow: hidden;
    background: #171411;
  }
  body {
    display: grid;
    place-items: center;
  }
  canvas {
    display: block;
    width: min(100vw, 56.25vh);
    height: min(177.78vw, 100vh);
    background: #28211b;
  }
</style>
</head>
<body>
<canvas id="film" width="540" height="960" aria-label="A hand-drawn animated scene about the 1814 London beer flood"></canvas>

<script>
(() => {
  "use strict";

  const canvas = document.getElementById("film");
  const ctx = canvas.getContext("2d");
  const W = 540, H = 960;
  const TAU = Math.PI * 2;

  let lastTime = 0;
  let elapsed = 0;

  // Slightly irregular, illustrated-world linework.
  function line(points, color = "#29221c", width = 4, alpha = 1) {
    ctx.save();
    ctx.globalAlpha *= alpha;
    ctx.beginPath();
    points.forEach(([x, y], i) => i ? ctx.lineTo(x, y) : ctx.moveTo(x, y));
    ctx.strokeStyle = color;
    ctx.lineWidth = width;
    ctx.lineCap = "round";
    ctx.lineJoin = "round";
    ctx.stroke();
    ctx.restore();
  }

  function shape(points, fill, stroke = "#29221c", width = 4) {
    ctx.beginPath();
    points.forEach(([x, y], i) => i ? ctx.lineTo(x, y) : ctx.moveTo(x, y));
    ctx.closePath();
    if (fill) {
      ctx.fillStyle = fill;
      ctx.fill();
    }
    if (stroke) {
      ctx.lineWidth = width;
      ctx.lineJoin = "round";
      ctx.strokeStyle = stroke;
      ctx.stroke();
    }
  }

  function ellipse(x, y, rx, ry, fill, stroke = "#29221c", width = 3) {
    ctx.beginPath();
    ctx.ellipse(x, y, rx, ry, 0, 0, TAU);
    if (fill) {
      ctx.fillStyle = fill;
      ctx.fill();
    }
    if (stroke) {
      ctx.lineWidth = width;
      ctx.strokeStyle = stroke;
      ctx.stroke();
    }
  }

  function rect(x, y, w, h, fill, stroke = "#29221c", width = 3) {
    ctx.fillStyle = fill;
    ctx.fillRect(x, y, w, h);
    if (stroke) {
      ctx.lineWidth = width;
      ctx.strokeStyle = stroke;
      ctx.strokeRect(x, y, w, h);
    }
  }

  function text(str, x, y, size, color = "#f2dfb0", align = "center") {
    ctx.save();
    ctx.font = `bold ${size}px Georgia, serif`;
    ctx.textAlign = align;
    ctx.textBaseline = "middle";
    ctx.fillStyle = color;
    ctx.fillText(str, x, y);
    ctx.restore();
  }

  function seeded(n) {
    const v = Math.sin(n * 127.1 + 311.7) * 43758.5453;
    return v - Math.floor(v);
  }

  function paperGrain(t, opacity = 0.12) {
    ctx.save();
    ctx.globalAlpha = opacity;
    for (let i = 0; i < 75; i++) {
      const x = seeded(i + 9) * W;
      const y = seeded(i + 201) * H;
      const len = 3 + seeded(i + 77) * 12;
      ctx.strokeStyle = i % 2 ? "#fff1cb" : "#241911";
      ctx.lineWidth = 1;
      ctx.beginPath();
      ctx.moveTo(x, y);
      ctx.lineTo(x + len, y + (seeded(i + 66) - 0.5) * 2);
      ctx.stroke();
    }
    ctx.restore();
  }

  function vignette() {
    const g = ctx.createRadialGradient(270, 420, 170, 270, 470, 650);
    g.addColorStop(0, "rgba(0,0,0,0)");
    g.addColorStop(1, "rgba(17,12,9,.45)");
    ctx.fillStyle = g;
    ctx.fillRect(0, 0, W, H);
  }

  function drawOwner(x, y, s = 1, pose = "smug") {
    ctx.save();
    ctx.translate(x, y);
    ctx.scale(s, s);

    // Rear arm and legs: deliberately simple stickman limbs, no shoes.
    line([[-8, 70], [-18, 116], [-36, 159]], "#171614", 8);
    line([[12, 70], [22, 119], [36, 159]], "#171614", 8);
    line([[-36, 159], [-43, 198]], "#171614", 7);
    line([[36, 159], [40, 198]], "#171614", 7);

    // Long black frock coat.
    shape([[-34, 49], [-20, 41], [20, 41], [34, 51], [39, 153],
           [23, 174], [7, 151], [0, 175], [-12, 151], [-28, 174], [-40, 152]],
          "#211f1c", "#11110f", 4);

    // Green waistcoat and gold chain.
    shape([[-14, 48], [0, 55], [14, 48], [11, 122], [0, 131], [-11, 122]],
          "#34503a", "#111c15", 2);
    line([[-7, 76], [0, 83], [7, 76], [6, 112], [0, 119], [-5, 112]],
         "#c59b44", 2);
    ellipse(0, 119, 3.5, 4.5, "#d6ad52", "#725223", 1.5);

    // Arms with tiny round, black hands.
    if (pose === "point") {
      line([[-24, 57], [-43, 83], [-51, 105]], "#151412", 7);
      ellipse(-52, 107, 6, 6, "#151412", null);
      line([[24, 57], [42, 86], [61, 91]], "#151412", 7);
      ellipse(63, 91, 6, 6, "#151412", null);
    } else {
      line([[-24, 57], [-43, 87], [-45, 111]], "#151412", 7);
      ellipse(-45, 114, 6, 6, "#151412", null);
      line([[24, 57], [42, 84], [42, 110]], "#151412", 7);
      ellipse(42, 113, 6, 6, "#151412", null);
    }

    // Pure white, imperfect circle head.
    ctx.beginPath();
    ctx.ellipse(0, 20, 25, 27, -0.04, 0, TAU);
    ctx.fillStyle = "#fffdf4";
    ctx.fill();
    ctx.lineWidth = 4;
    ctx.strokeStyle = "#171614";
    ctx.stroke();

    // Smug, half-lidded expression.
    line([[-13, 14], [-7, 12]], "#211d19", 2.5);
    line([[6, 12], [13, 14]], "#211d19", 2.5);
    ellipse(-9, 18, 2.2, 2.4, "#171614", null);
    ellipse(9, 18, 2.2, 2.4, "#171614", null);
    line([[-7, 30], [0, 32], [9, 28]], "#4b2118", 2.5);

    // Tall black top hat.
    rect(-21, -24, 42, 9, "#191816", "#10100f", 3);
    shape([[-14, -25], [-11, -62], [11, -62], [14, -25]],
          "#201e1b", "#10100f", 3);
    line([[-10, -39], [10, -39]], "#7d2920", 3);

    ctx.restore();
  }

  function drawCobblestones(t) {
    // Wet, irregular perspective stones.
    for (let row = 0; row < 9; row++) {
      const y = 510 + row * 58;
      const skew = row * 13;
      line([[0, y], [540, y + (row % 2 ? 8 : -4)]], "#5a4030", 2, 0.8);
      const count = 5 + row % 3;
      for (let c = 0; c < count; c++) {
        const x = c * (540 / count) + (row % 2) * 23 + skew;
        line([[x, y + 2], [x + 25 + row * 2, y + 22 + row * 2]],
             "#5a4030", 2, 0.7);
      }
    }

    // Beer surging down the narrow street.
    const surge = Math.sin(t * 3.1) * 22;
    const beer = ctx.createLinearGradient(0, 470, 0, 960);
    beer.addColorStop(0, "#633312");
    beer.addColorStop(0.55, "#9b5019");
    beer.addColorStop(1, "#4b250f");
    shape([[0, 516 + surge], [72, 492], [131, 516], [196, 484 + surge],
           [258, 514], [322, 475], [390, 510 + surge], [461, 488],
           [540, 517], [540, 960], [0, 960]],
          beer, "#43230f", 5);

    // Foam ridges and bubbles.
    for (let i = 0; i < 16; i++) {
      const x = (i * 43 + t * 55) % 580 - 20;
      const y = 519 + Math.sin(i * 2.2 + t * 2) * 30 + (i % 4) * 34;
      ellipse(x, y, 12 + (i % 3) * 5, 5 + (i % 2) * 3,
              i % 2 ? "#e3c58a" : "#c99a56", "#70451f", 1.5);
    }
    for (let i = 0; i < 12; i++) {
      const x = (i * 71 + t * 29) % 560;
      const y = 605 + (i * 53) % 315;
      ellipse(x, y, 4 + (i % 4), 3 + (i % 3), "#dfb365", null);
    }
  }

  function drawStreet(t) {
    const sky = ctx.createLinearGradient(0, 0, 0, 530);
    sky.addColorStop(0, "#33313a");
    sky.addColorStop(1, "#b17d4a");
    rect(0, 0, W, 550, sky, null);

    // Distant slum silhouettes.
    rect(0, 276, 115, 280, "#554136", "#30231c", 4);
    rect(422, 240, 118, 320, "#49372e", "#30231c", 4);
    shape([[0, 277], [36, 234], [78, 259], [115, 218], [115, 555], [0, 555]],
          "#5a4437", "#30231c", 4);
    shape([[422, 272], [460, 217], [494, 251], [540, 206], [540, 555], [422, 555]],
          "#4c392f", "#30231c", 4);

    // Crooked windows.
    for (const x of [22, 68, 445, 491]) {
      for (const y of [310, 380, 454]) {
        rect(x, y, 25, 38, "#2c2927", "#211a16", 3);
        line([[x + 12, y], [x + 12, y + 38]], "#957450", 2);
      }
    }

    // Street narrowing toward a smoky vanishing point.
    shape([[0, 505], [190, 426], [350, 426], [540, 506], [540, 600], [0, 600]],
          "#59483b", "#30251e", 3);
    drawCobblestones(t);

    // Distant fog and a few paper-like smoke curls.
    ctx.save();
    ctx.globalAlpha = 0.17;
    rect(0, 360, W, 130, "#e0bd87", null);
    ctx.restore();
    line([[111, 218], [96, 196], [111, 178], [103, 162]], "#c3a078", 5, 0.45);
    line([[450, 180], [466, 156], [451, 137], [464, 117]], "#c3a078", 5, 0.4);
  }

  function sceneA(t) {
    drawStreet(t);

    // Slow push-in on the Owner.
    const zoom = 1 + 0.08 * (t / 3);
    ctx.save();
    ctx.translate(270, 505);
    ctx.scale(zoom, zoom);
    ctx.translate(-270, -505);
    drawOwner(270, 405, 1.22, "smug");
    ctx.restore();

    // Beer wave looming behind the foreground figure.
    const pulse = Math.sin(t * 4) * 9;
    shape([[0, 444 + pulse], [44, 420], [79, 436], [121, 399],
           [160, 433], [205, 412], [248, 440], [298, 403 + pulse],
           [343, 433], [387, 411], [427, 439], [475, 410],
           [540, 442], [540, 572], [0, 572]],
          "#874111", "#49230f", 5);
    for (let i = 0; i < 12; i++) {
      const x = (i * 49 + t * 26) % 560 - 10;
      const y = 426 + Math.sin(i + t * 2) * 17;
      ellipse(x, y, 13 + (i % 3) * 4, 7, "#dfbd7b", "#70421d", 2);
    }

    // Title-card-like caption.
    text("LONDON, 1814", 270, 73, 23, "#f1d8a7");
    text("THE BEER FLOOD", 270, 106, 15, "#c6a87a");
  }

  function drawOffice(t) {
    // Golden candlelit office.
    const bg = ctx.createLinearGradient(0, 0, 540, 960);
    bg.addColorStop(0, "#39251a");
    bg.addColorStop(0.55, "#845128");
    bg.addColorStop(1, "#2c1b14");
    rect(0, 0, W, H, bg, null);

    // Bookshelves.
    rect(26, 90, 180, 360, "#51331f", "#241811", 5);
    rect(330, 88, 180, 366, "#51331f", "#241811", 5);
    for (const y of [180, 275, 370]) {
      line([[28, y], [204, y]], "#241811", 7);
      line([[332, y], [508, y]], "#241811", 7);
    }
    const bookColors = ["#812f20", "#a06b32", "#334035", "#75512c", "#573538"];
    for (let i = 0; i < 27; i++) {
      const x = i < 13 ? 36 + (i % 7) * 23 : 340 + ((i - 13) % 7) * 23;
      const row = i < 13 ? Math.floor(i / 7) : Math.floor((i - 13) / 7);
      const y = 128 + row * 95;
      rect(x, y, 14, 45 + (i % 3) * 7, bookColors[i % bookColors.length], "#2b1b12", 2);
      line([[x + 4, y + 10], [x + 10, y + 10]], "#d0a75b", 1.5);
    }

    // Candle and warm pool of light.
    const glow = ctx.createRadialGradient(273, 362, 5, 273, 362, 220);
    glow.addColorStop(0, "rgba(255,206,103,.48)");
    glow.addColorStop(1, "rgba(255,185,68,0)");
    ctx.fillStyle = glow;
    ctx.fillRect(45, 130, 450, 460);
    rect(264, 328, 17, 51, "#e6d3a4", "#4b2c17", 2);
    line([[272, 329], [272, 314]], "#272019", 3);
    shape([[268, 313], [272, 299], [277, 313], [272, 320]], "#ffcf65", "#86441c", 2);

    // Wood-grain desk.
    shape([[0, 550], [540, 530], [540, 960], [0, 960]], "#633b20", "#241811", 5);
    for (let i = 0; i < 13; i++) {
      const y = 586 + i * 29;
      line([[10, y], [170, y - 8], [306, y + 4], [520, y - 12]],
           "#a36b37", 2, 0.62);
    }
    for (let i = 0; i < 6; i++) {
      line([[i * 97, 574], [i * 97 + 20, 954]], "#3e2719", 1.5, 0.5);
    }
  }

  function drawBag(x, y, s) {
    ctx.save();
    ctx.translate(x, y);
    ctx.scale(s, s);
    // Fat tied money bag.
    shape([[-79, -40], [-58, -54], [-39, -48], [-22, -60], [0, -50],
           [24, -57], [43, -45], [62, -47], [78, -29], [67, 27],
           [43, 52], [0, 60], [-46, 48], [-70, 19]],
          "#a9782e", "#342114", 5);
    line([[-51, -45], [-28, -30], [0, -25], [32, -34], [59, -43]],
         "#e1b651", 4);
    line([[-17, -48], [-10, -25], [8, -25], [16, -50]], "#362317", 4);
    text("$", 0, 7, 53, "#f5d267");
    // Coin edges peeking from the mouth of the bag.
    for (let i = -2; i <= 2; i++) {
      ellipse(i * 18, -48 - Math.abs(i) * 3, 10, 7, "#e9c35e", "#81551d", 2);
    }
    ctx.restore();
  }

  function drawParchment(x, y, s) {
    ctx.save();
    ctx.translate(x, y);
    ctx.rotate(-0.12);
    ctx.scale(s, s);
    shape([[-105, -34], [-87, -42], [93, -42], [108, -32], [102, 38],
           [85, 44], [-94, 42], [-108, 32]],
          "#e6d2a1", "#3e2a1a", 4);
    for (let i = 0; i < 4; i++) {
      line([[-68, -19 + i * 15], [58 - (i % 2) * 18, -19 + i * 15]],
           "#94784c", 2, 0.8);
    }
    // Red ribbon and bow.
    line([[0, -39], [1, 39]], "#9c3023", 7);
    line([[-4, -35], [-31, -57], [-16, -61], [2, -44],
          [23, -62], [37, -56], [5, -34]], "#b5392b", 5);
    ellipse(1, -37, 6, 6, "#c34835", "#61251c", 2);
    ctx.restore();
  }

  function sceneB(t) {
    drawOffice(t);

    // Tracking left across the bribe and the Owner's knowing expression.
    const track = (t - 3) * 52;
    ctx.save();
    ctx.translate(-track, 0);

    drawParchment(330, 650, 1.25);
    drawBag(194, 640, 1.23);

    // Owner's face in an extreme close-up, held near the right edge.
    ctx.save();
    ctx.translate(461, 277);
    ctx.scale(2.65, 2.65);
    ctx.beginPath();
    ctx.ellipse(0, 0, 47, 49, -0.04, 0, TAU);
    ctx.fillStyle = "#fffdf4";
    ctx.fill();
    ctx.lineWidth = 5;
    ctx.strokeStyle = "#171614";
    ctx.stroke();
    // Half-closed eyes and smug smile.
    line([[-25, -8], [-11, -11]], "#211d19", 4);
    line([[10, -11], [25, -8]], "#211d19", 4);
    ellipse(-18, -2, 3, 3, "#171614", null);
    ellipse(18, -2, 3, 3, "#171614", null);
    line([[-12, 22], [0, 27], [17, 20]], "#4b2118", 4);
    // Hat brim visible above the close-up.
    rect(-50, -57, 100, 13, "#191816", "#10100f", 3);
    shape([[-33, -61], [-27, -111], [27, -111], [33, -61]],
          "#201e1b", "#10100f", 4);
    line([[-25, -83], [25, -83]], "#7d2920", 4);
    ctx.restore();

    // A tiny dot hand gripping the gold bag.
    line([[449, 423], [414, 481], [348, 522]], "#171614", 10);
    ellipse(344, 524, 8, 8, "#171614", null);
    ctx.restore();

    // Candlelit labels.
    text("A MOST FORTUNATE ACCIDENT", 270, 871, 18, "#e5c689");
  }

  function drawBrickBuilding(x, y, w, h, color = "#783e2e") {
    rect(x, y, w, h, color, "#2b211c", 5);

    // Hand-drawn brick courses with alternating joints.
    for (let row = 0; row < Math.ceil(h / 27); row++) {
      const yy = y + 19 + row * 27;
      line([[x + 3, yy], [x + w - 3, yy]], "#b27653", 1.5, 0.75);
      const offset = row % 2 ? 28 : 0;
      for (let xx = x + offset; xx < x + w; xx += 56) {
        line([[xx, yy - 27], [xx, yy]], "#4e2d25", 1.4, 0.8);
      }
    }
  }

  function drawArchedWindow(cx, baseY, width, height) {
    ctx.beginPath();
    ctx.moveTo(cx - width / 2, baseY);
    ctx.lineTo(cx - width / 2, baseY - height * 0.6);
    ctx.quadraticCurveTo(cx, baseY - height * 1.42, cx + width / 2, baseY - height * 0.6);
    ctx.lineTo(cx + width / 2, baseY);
    ctx.closePath();
    ctx.fillStyle = "#241d19";
    ctx.fill();
    ctx.lineWidth = 4;
    ctx.strokeStyle = "#2b211c";
    ctx.stroke();

    // Vat silhouettes behind the windows.
    ellipse(cx, baseY - height * 0.24, width * 0.34, height * 0.19,
            "#996333", "#d0a26a", 2);
    line([[cx - width * 0.34, baseY - height * 0.24],
          [cx - width * 0.34, baseY - height * 0.68]],
         "#bd8b52", 3);
    line([[cx + width * 0.34, baseY - height * 0.24],
          [cx + width * 0.34, baseY - height * 0.68]],
         "#bd8b52", 3);
    line([[cx, baseY - height * 0.42], [cx, baseY - height * 0.78]],
         "#bd8b52", 2);
  }

  function drawHorseshoe(cx, cy) {
    ctx.save();
    ctx.strokeStyle = "#d5b568";
    ctx.lineWidth = 9;
    ctx.beginPath();
    ctx.arc(cx, cy, 34, 0.18, Math.PI - 0.18);
    ctx.stroke();
    line([[cx - 33, cy + 5], [cx - 39, cy + 15]], "#d5b568", 5);
    line([[cx + 33, cy + 5], [cx + 39, cy + 15]], "#d5b568", 5);
    ctx.restore();
  }

  function sceneC(t) {
    // Dusk sky.
    const sky = ctx.createLinearGradient(0, 0, 0, H);
    sky.addColorStop(0, "#312e42");
    sky.addColorStop(0.46, "#87533e");
    sky.addColorStop(1, "#d09558");
    rect(0, 0, W, H, sky, null);

    // Slow crane upward reveals progressively more rooftop and chimney.
    const crane = (t - 7) * 33;
    ctx.save();
    ctx.translate(0, crane);

    // Background rooftops and crowded slum houses.
    drawBrickBuilding(-16, 450, 118, 380, "#584137");
    shape([[-24, 457], [39, 389], [122, 453]], "#41312c", "#241d1b", 5);
    drawBrickBuilding(425, 436, 145, 390, "#634338");
    shape([[415, 446], [478, 371], [552, 445]], "#42302c", "#241d1b", 5);

    for (let i = 0; i < 5; i++) {
      rect(7 + i * 22, 510 + (i % 2) * 63, 14, 33, "#282322", "#2b211c", 2);
      rect(445 + (i % 3) * 29, 504 + (i % 2) * 68, 17, 36, "#2a2220", "#2b211c", 2);
    }

    // Great brewery building.
    drawBrickBuilding(105, 234, 338, 541, "#824632");

    // Roof and gables.
    shape([[93, 244], [157, 174], [223, 240], [283, 169], [350, 239],
           [410, 182], [457, 245]],
          "#46312b", "#241d1b", 5);
    line([[157, 174], [157, 234]], "#9b5a3e", 3);
    line([[283, 169], [283, 234]], "#9b5a3e", 3);
    line([[410, 182], [410, 234]], "#9b5a3e", 3);

    // Tall smoking chimney.
    rect(363, 62, 62, 173, "#713d32", "#2b211c", 5);
    rect(355, 51, 78, 20, "#49312a", "#2b211c", 4);
    for (let i = 0; i < 5; i++) {
      line([[366, 94 + i * 27], [420, 94 + i * 27]], "#b07153", 2, 0.8);
    }
    line([[383, 49], [377, 31]], "#d5b7a0", 5, 0.5);
    line([[399, 44], [411, 21], [402, 4]], "#d5b7a0", 7, 0.42);
    line([[414, 47], [438, 25], [435, 7]], "#d5b7a0", 5, 0.35);

    // Rows of arched brewery windows, each revealing wooden vats.
    for (let row = 0; row < 3; row++) {
      for (let col = 0; col < 4; col++) {
        drawArchedWindow(147 + col * 82, 434 + row * 111, 48, 76);
      }
    }

    // Gate, sign, and entrance.
    rect(188, 635, 170, 140, "#3c2b25", "#261c19", 5);
    shape([[207, 775], [207, 705], [273, 663], [339, 705], [339, 775]],
          "#251d19", "#1b1715", 4);
    drawHorseshoe(273, 277);
    rect(210, 286, 126, 30, "#4e3527", "#2a201b", 3);
    text("HORSE SHOE", 273, 301, 14, "#e4c57e");

    // Foreground slum street and puddles.
    shape([[0, 790], [540, 790], [540, 960], [0, 960]],
          "#41342e", "#29211d", 4);
    for (let i = 0; i < 7; i++) {
      ellipse(35 + i * 77, 860 + (i % 3) * 24, 28 + (i % 2) * 11, 7,
              "#705345", "#332822", 2);
    }

    // Owner tiny by the gate.
    drawOwner(371, 741, 0.38, "smug");
    ctx.restore();

    text("THE MORNING AFTER", 270, 890 - crane * 0.15, 18, "#f0d49f");
  }

  function draw(t) {
    ctx.clearRect(0, 0, W, H);

    if (t < 3) {
      sceneA(t);
    } else if (t < 7) {
      sceneB(t);
    } else {
      sceneC(t);
    }

    paperGrain(t, 0.08);
    vignette();

    // Short, soft dissolves between the three illustrated panels.
    const fade = 0.22;
    let edge = null;
    if (t < fade) edge = 0;
    else if (t > 3 - fade && t < 3 + fade) edge = (t - (3 - fade)) / (2 * fade);
    else if (t > 7 - fade && t < 7 + fade) edge = (t - (7 - fade)) / (2 * fade);
    else if (t > 10 - fade) edge = (t - (10 - fade)) / fade;

    if (edge !== null) {
      ctx.fillStyle = `rgba(22,16,12,${Math.max(0, Math.sin(edge * Math.PI)) * 0.42})`;
      ctx.fillRect(0, 0, W, H);
    }
  }

  function frame(now) {
    if (!lastTime) lastTime = now;
    const delta = Math.min((now - lastTime) / 1000, 0.05);
    lastTime = now;
    elapsed = (elapsed + delta) % 10;
    draw(elapsed);
    requestAnimationFrame(frame);
  }

  requestAnimationFrame(frame);
})();
</script>
</body>
</html>
