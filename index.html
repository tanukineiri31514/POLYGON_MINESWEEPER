
(() => {
  "use strict";

  const DIFFICULTIES = {
    beginner: { name: "初級", h: 8, w: 12, count: 96, cell: 50 },
    intermediate: { name: "中級", h: 16, w: 30, count: 480, cell: 30 },
    advanced: { name: "上級", h: 25, w: 40, count: 1000, cell: 25 },
    expert: { name: "最上級", h: 40, w: 60, count: 2400, cell: 22 }
  };

  const SHAPES = {
    pentagon: {
      label: "五角形",
      maxNeighbors: 7,
      bombs: { beginner: 15, intermediate: 67, advanced: 165, expert: 360 },
      width: { beginner: 55, intermediate: 40, advanced: 32, expert: 26 },
      wall: { beginner: 38, intermediate: 29, advanced: 24, expert: 20 },
      roof: { beginner: 23, intermediate: 18, advanced: 15, expert: 13 }
    },
    hexagon: {
      label: "六角形",
      maxNeighbors: 6,
      bombs: { beginner: 15, intermediate: 67, advanced: 165, expert: 360 },
      radius: { beginner: 31, intermediate: 22, advanced: 20, expert: 16 }
    }
  };

  let selectedShape = "pentagon";

  const $ = (id) => document.getElementById(id);
  const menuScreen = $("menu-screen");
  const gameScreen = $("game-screen");
  const rulesScreen = $("rules-screen");
  const canvas = $("board");
  const ctx = canvas.getContext("2d");
  const viewport = $("board-viewport");
  const clearOverlay = $("clear-overlay");

  let game = null;
  let timerId = null;
  let clearOverlayTimerId = null;
  let currentTab = "game";
  let actionMode = "open";
  let audioContext = null;

  const MIN_ZOOM = 0.5;
  const MAX_ZOOM = 2;
  const ZOOM_STEP = 0.1;

  const COLORS = {
    closed: "#718096",
    closedLight: "#8795a8",
    closedDark: "#4b586b",
    opened: "#dce3eb",
    openedEdge: "#aab6c4",
    grid: "#596579",
    bomb: "#df5353",
    flag: "#f06b69",
    question: "#ffe38a",
    text: "#172334"
  };

  // マスの形状を生成する
  function makePolygon(shape, row, col, level) {
    if (shape === "pentagon") {
      const width = SHAPES.pentagon.width[level];
      const wall = SHAPES.pentagon.wall[level];
      const roof = SHAPES.pentagon.roof[level];

      const offset = (row % 4 === 1 || row % 4 === 2)
        ? width / 2 : 0;

      const x = col * width + offset;
      const pair = Math.floor(row / 2);

      if (row % 2 === 0) {
        const y = pair * (2 * wall + roof);
        return {
          vertices: [
            [x, y],
            [x + width, y],
            [x + width, y + wall],
            [x + width / 2, y + wall + roof],
            [x, y + wall]
          ],
          unit: Math.min(width, wall + roof) * 0.63
        };
      }

      const peakY = pair * (2 * wall + roof) + wall;
      const shoulderY = peakY + roof;

      return {
        vertices: [
          [x, shoulderY],
          [x + width / 2, peakY],
          [x + width, shoulderY],
          [x + width, shoulderY + wall],
          [x, shoulderY + wall]
        ],
        unit: Math.min(width, wall + roof) * 0.63
      };
    }

    const radius = SHAPES.hexagon.radius[level];
    const cx = radius + col * radius * 1.5;
    const cy = Math.sqrt(3) * radius *
      (row + (col % 2) / 2) +
      Math.sqrt(3) * radius / 2;

    const vertices = Array.from({ length: 6 }, (_, i) => {
      const angle = i * Math.PI / 3;
      return [
        cx + Math.cos(angle) * radius,
        cy + Math.sin(angle) * radius
      ];
    });

    return { vertices, unit: radius * 1.25 };
  }

  function vertexKey(point) {
    return `${Math.round(point[0] * 10000)},${Math.round(point[1] * 10000)}`;
  }

  function edgeKey(a, b) {
    const p = vertexKey(a);
    const q = vertexKey(b);
    return p < q ? `${p}|${q}` : `${q}|${p}`;
  }

  // 図形生成と隣接関係の構築
  function createCells(shape, level) {
    const { h: rows, w: cols } = DIFFICULTIES[level];
    const cells = [];

    let minX = Infinity;
    let minY = Infinity;
    let maxX = -Infinity;
    let maxY = -Infinity;

    for (let r = 0; r < rows; r++) {
      for (let c = 0; c < cols; c++) {
        const poly = makePolygon(shape, r, c, level);
        const vertices = poly.vertices;

        for (const [x, y] of vertices) {
          minX = Math.min(minX, x);
          minY = Math.min(minY, y);
          maxX = Math.max(maxX, x);
          maxY = Math.max(maxY, y);
        }

        cells.push({
          row: r,
          col: c,
          vertices,
          unit: poly.unit,
          neighbors: [],
          cx: 0,
          cy: 0,
          box: null
        });
      }
    }

    const padding = 12;

    for (const cell of cells) {
      cell.vertices = cell.vertices.map(
        ([x, y]) => [x - minX + padding, y - minY + padding]
      );

      const xs = cell.vertices.map(v => v[0]);
      const ys = cell.vertices.map(v => v[1]);

      cell.cx = xs.reduce((a, b) => a + b, 0) / xs.length;
      cell.cy = ys.reduce((a, b) => a + b, 0) / ys.length;

      cell.box = {
        minX: Math.min(...xs),
        maxX: Math.max(...xs),
        minY: Math.min(...ys),
        maxY: Math.max(...ys)
      };
    }

    const neighborSets = cells.map(() => new Set());

    const addNeighbor = (a, b) => {
      if (a === b) return;
      neighborSets[a].add(b);
      neighborSets[b].add(a);
    };

    const edges = new Map();
    const verticesToCells = new Map();

    for (let i = 0; i < cells.length; i++) {
      const vertices = cells[i].vertices;

      for (let j = 0; j < vertices.length; j++) {
        const vertex = vertices[j];
        const vKey = vertexKey(vertex);

        if (!verticesToCells.has(vKey)) {
          verticesToCells.set(vKey, []);
        }
        verticesToCells.get(vKey).push(i);

        const eKey = edgeKey(
          vertex,
          vertices[(j + 1) % vertices.length]
        );

        if (edges.has(eKey)) {
          addNeighbor(i, edges.get(eKey));
        } else {
          edges.set(eKey, i);
        }
      }
    }

    // 五角形は辺・頂点の両方を隣接扱い
    if (shape === "pentagon") {
      for (const owners of verticesToCells.values()) {
        for (let a = 0; a < owners.length; a++) {
          for (let b = a + 1; b < owners.length; b++) {
            addNeighbor(owners[a], owners[b]);
          }
        }
      }
    }

    for (let i = 0; i < cells.length; i++) {
      cells[i].neighbors = [...neighborSets[i]];
    }

    let width = Math.ceil(maxX - minX + padding * 2);
    let height = Math.ceil(maxY - minY + padding * 2);

    // 縦長の盤面を横長に回転する
    if (height > width) {
      for (const cell of cells) {
        cell.vertices = cell.vertices.map(
          ([x, y]) => [height - y, x]
        );

        const {
          minX: left,
          maxX: right,
          minY: top,
          maxY: bottom
        } = cell.box;

        const cx = cell.cx;
        cell.cx = height - cell.cy;
        cell.cy = cx;

        cell.box = {
          minX: height - bottom,
          maxX: height - top,
          minY: left,
          maxY: right
        };
      }

      [width, height] = [height, width];
    }

    return { cells, width, height };
  }

  // 隣接マスを取得する
  function getNeighbors(y, x) {
    return game.cells[y * game.w + x].neighbors.map(index => {
      const cell = game.cells[index];
      return [cell.row, cell.col];
    });
  }

  function updateDifficultyInfo() {
    for (const [mode] of Object.entries(DIFFICULTIES)) {
      const bombs = SHAPES[selectedShape].bombs[mode];

      document.querySelectorAll(
        `[data-bomb-count="${mode}"]`
      ).forEach(element => {
        element.textContent = bombs;
      });

      document.querySelectorAll(
        `[data-rule-bombs="${mode}"]`
      ).forEach(element => {
        element.textContent = bombs;
      });
    }
  }

  function selectShape(shape) {
    if (!Object.hasOwn(SHAPES, shape)) return;

    selectedShape = shape;

    document.querySelectorAll("[data-shape]").forEach(button => {
      const selected = button.dataset.shape === shape;
      button.classList.toggle("selected", selected);
      button.setAttribute("aria-pressed", String(selected));
    });

    updateDifficultyInfo();
  }

  function hideClearOverlay() {
    if (clearOverlayTimerId !== null) {
      window.clearTimeout(clearOverlayTimerId);
      clearOverlayTimerId = null;
    }
    clearOverlay.classList.add("hidden");
  }

  function showClearOverlay() {
    hideClearOverlay();
    clearOverlay.classList.remove("hidden");
    clearOverlayTimerId = window.setTimeout(hideClearOverlay, 5000);
  }

  function switchTab(tab) {
    if (tab !== "game") hideClearOverlay();
    currentTab = tab;

    document.querySelectorAll("[data-tab]").forEach(button => {
      button.classList.toggle("active", button.dataset.tab === tab);
    });

    const showGame = tab === "game";
    menuScreen.classList.toggle("hidden", !showGame || game !== null);
    gameScreen.classList.toggle("hidden", !showGame || game === null);
    rulesScreen.classList.toggle("hidden", showGame);
  }

  function setActionMode(mode) {
    actionMode = mode;

    document.querySelectorAll("[data-action-mode]").forEach(button => {
      const selected = button.dataset.actionMode === mode;
      button.classList.toggle("active", selected);
      button.setAttribute("aria-pressed", String(selected));
    });

    const hints = {
      open: "マスをタップすると開きます（？マークも開けられます）",
      flag: "マスをタップすると旗を立てる・外します",
      question: "マスをタップすると？マークを仮置き・解除します"
    };

    $("action-mode-hint").textContent = hints[mode];
  }

  function createGame(mode) {
    hideClearOverlay();

    const cfg = DIFFICULTIES[mode];
    const geometry = createCells(selectedShape, mode);

    game = {
      ...cfg,
      shape: selectedShape,
      cells: geometry.cells,
      width: geometry.width,
      height: geometry.height,
      bombs: SHAPES[selectedShape].bombs[mode],
      board: Array.from(
        { length: cfg.h }, () => Array(cfg.w).fill(0)
      ),
      opened: Array.from(
        { length: cfg.h }, () => Array(cfg.w).fill(false)
      ),
      flags: Array.from(
        { length: cfg.h }, () => Array(cfg.w).fill(false)
      ),
      questions: Array.from(
        { length: cfg.h }, () => Array(cfg.w).fill(false)
      ),
      generated: false,
      over: false,
      won: false,
      startedAt: null,
      endedAt: null,
      openedCount: 0,
      zoom: 1,
      mode
    };

    switchTab("game");
    setActionMode("open");

    $("current-mode").textContent =
      `${SHAPES[selectedShape].label} / ${cfg.name}`;

    $("board-size").textContent = `${cfg.count} マス`;

    $("neighbor-hint").textContent =
      game.shape === "pentagon"
        ? "数字は辺・角で接するマスの爆弾数（最大7）"
        : "数字は辺を共有するマスの爆弾数（最大6）";

    $("bomb-count").textContent =
      String(game.bombs).padStart(3, "0");

    $("flag-count").textContent = "000";
    $("timer").textContent = "000";

    updateZoomControls();
    setMessage("最初のマスを選んでスタート！");
    stopTimer();

    viewport.scrollLeft = 0;
    viewport.scrollTop = 0;

    renderBoard();
    updateHud();

    window.requestAnimationFrame(() => {
      if (game && game.mode === mode && !game.generated) {
        renderBoard();
      }
    });
  }

  // 初手で8マスの安全領域を確保
  function createInitialSafeCells(firstY, firstX) {
    const targetCount = 8;

    const queue = [[firstY, firstX]];
    const visited = new Set([firstY * game.w + firstX]);
    const safeCells = [[firstY, firstX]];
    let head = 0;

    while (head < queue.length && safeCells.length < targetCount) {
      const [y, x] = queue[head++];

      const neighbors = getNeighbors(y, x).filter(
        ([ny, nx]) =>
          !visited.has(ny * game.w + nx) &&
          !game.flags[ny][nx]
      );

      for (let i = neighbors.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [neighbors[i], neighbors[j]] = [neighbors[j], neighbors[i]];
      }

      for (const [ny, nx] of neighbors) {
        if (safeCells.length >= targetCount) break;

        const key = ny * game.w + nx;
        if (visited.has(key)) continue;

        visited.add(key);
        safeCells.push([ny, nx]);
        queue.push([ny, nx]);
      }
    }

    return safeCells;
  }

  function placeBombs(safeCells) {
    const safeSet = new Set(
      safeCells.map(([y, x]) => y * game.w + x)
    );

    const candidates = [];

    for (let y = 0; y < game.h; y++) {
      for (let x = 0; x < game.w; x++) {
        if (!safeSet.has(y * game.w + x)) {
          candidates.push([y, x]);
        }
      }
    }

    for (let i = candidates.length - 1; i > 0; i--) {
      const j = Math.floor(Math.random() * (i + 1));
      [candidates[i], candidates[j]] =
        [candidates[j], candidates[i]];
    }

    for (let i = 0; i < game.bombs; i++) {
      const [y, x] = candidates[i];
      game.board[y][x] = -1;
    }

    // 各マスの数字を計算
    for (let y = 0; y < game.h; y++) {
      for (let x = 0; x < game.w; x++) {
        if (game.board[y][x] !== -1) continue;

        for (const [ny, nx] of getNeighbors(y, x)) {
          if (game.board[ny][nx] !== -1) {
            game.board[ny][nx]++;
          }
        }
      }
    }
  }

  function inside(y, x) {
    return y >= 0 && y < game.h &&
      x >= 0 && x < game.w;
  }

  function reveal(y, x) {
    if (
      !inside(y, x) ||
      game.over ||
      game.opened[y][x] ||
      game.flags[y][x]
    ) return;

    let initialSafeCells = null;

    if (!game.generated) {
      initialSafeCells = createInitialSafeCells(y, x);
      placeBombs(initialSafeCells);

      game.generated = true;
      game.startedAt = Date.now();
      startTimer();
    }

    if (game.board[y][x] === -1) {
      game.questions[y][x] = false;
      game.opened[y][x] = true;
      playSound("bomb");
      finishGame(false);
      return;
    }

    const queue = initialSafeCells ?? [[y, x]];
    const queued = new Set(
      queue.map(([qy, qx]) => qy * game.w + qx)
    );

    let head = 0;

    while (head < queue.length) {
      const [cy, cx] = queue[head++];

      if (game.opened[cy][cx] || game.flags[cy][cx]) continue;
      if (game.board[cy][cx] === -1) continue;

      game.questions[cy][cx] = false;
      game.opened[cy][cx] = true;
      game.openedCount++;

      if (game.board[cy][cx] !== 0) continue;

      for (const [ny, nx] of getNeighbors(cy, cx)) {
        const key = ny * game.w + nx;

        if (
          !game.opened[ny][nx] &&
          !game.flags[ny][nx] &&
          game.board[ny][nx] !== -1 &&
          !queued.has(key)
        ) {
          queued.add(key);
          queue.push([ny, nx]);
        }
      }
    }

    if (game.openedCount === game.h * game.w - game.bombs) {
      finishGame(true);
    } else {
      setMessage("爆弾を避けて進もう！");
      playSound("open");
    }

    renderBoard();
    updateHud();
  }

  function toggleFlag(y, x) {
    if (!inside(y, x) || game.over || game.opened[y][x]) return;

    game.flags[y][x] = !game.flags[y][x];

    if (game.flags[y][x]) {
      game.questions[y][x] = false;
    }

    renderBoard();
    updateHud();
  }

  function toggleQuestion(y, x) {
    if (!inside(y, x) || game.over || game.opened[y][x]) return;

    game.questions[y][x] = !game.questions[y][x];

    if (game.questions[y][x]) {
      game.flags[y][x] = false;
    }

    renderBoard();
    updateHud();
  }

  function finishGame(won) {
    game.over = true;
    game.won = won;
    game.endedAt = Date.now();

    stopTimer();

    if (!won) {
      for (let y = 0; y < game.h; y++) {
        for (let x = 0; x < game.w; x++) {
          if (game.board[y][x] === -1) {
            game.opened[y][x] = true;
          }
        }
      }

      setMessage("ゲームオーバー — もう一度挑戦しよう");
    } else {
      setMessage("クリア！おめでとう！！");
      showClearOverlay();
      playSound("clear");
    }

    renderBoard();
    updateHud();
  }

  function updateZoomControls() {
    if (!game) return;

    $("zoom-reset-button").textContent =
      `${Math.round(game.zoom * 100)}%`;

    $("zoom-out-button").disabled =
      game.zoom <= MIN_ZOOM + 0.000001;

    $("zoom-in-button").disabled =
      game.zoom >= MAX_ZOOM - 0.000001;
  }

  function applyZoomSize() {
    if (!game) return;

    canvas.style.width = `${game.width * game.zoom}px`;
    canvas.style.height = `${game.height * game.zoom}px`;
  }

  function setZoom(nextZoom, clientX, clientY) {
    if (!game) return;

    const value = Math.max(
      MIN_ZOOM,
      Math.min(MAX_ZOOM, Math.round(nextZoom * 100) / 100)
    );

    if (Math.abs(value - game.zoom) < 0.00001) return;

    const vp = viewport.getBoundingClientRect();

    const x = Number.isFinite(clientX)
      ? clientX : vp.left + vp.width / 2;

    const y = Number.isFinite(clientY)
      ? clientY : vp.top + vp.height / 2;

    const before = canvas.getBoundingClientRect();

    const u = (x - before.left) / before.width;
    const v = (y - before.top) / before.height;

    game.zoom = value;

    applyZoomSize();
    updateZoomControls();

    const after = canvas.getBoundingClientRect();

    viewport.scrollLeft +=
      after.left + u * after.width - x;

    viewport.scrollTop +=
      after.top + v * after.height - y;
  }

  // Web Audio APIによるSE
  function playSound(type) {
    try {
      const AudioContextClass =
        window.AudioContext || window.webkitAudioContext;

      if (!AudioContextClass) return;

      if (!audioContext) {
        audioContext = new AudioContextClass();
      }

      if (audioContext.state === "suspended") {
        audioContext.resume();
      }

      const now = audioContext.currentTime;
      const master = audioContext.createGain();
      master.connect(audioContext.destination);

      master.gain.setValueAtTime(0.0001, now);
      master.gain.exponentialRampToValueAtTime(
        type === "clear" ? 0.16 : 0.09,
        now + 0.015
      );
      master.gain.exponentialRampToValueAtTime(
        0.0001,
        now + (type === "clear" ? 0.9 : 0.22)
      );

      if (type === "clear") {
        [523.25, 659.25, 783.99, 1046.5].forEach(
          (frequency, index) => {
            const oscillator = audioContext.createOscillator();
            const gain = audioContext.createGain();

            oscillator.type = "sine";
            oscillator.frequency.setValueAtTime(
              frequency, now + index * 0.13
            );

            gain.gain.setValueAtTime(0.0001, now);
            gain.gain.setValueAtTime(
              0.55, now + index * 0.13
            );
            gain.gain.exponentialRampToValueAtTime(
              0.0001,
              now + index * 0.13 + 0.24
            );

            oscillator.connect(gain);
            gain.connect(master);
            oscillator.start(now + index * 0.13);
            oscillator.stop(now + index * 0.13 + 0.26);
          }
        );
      } else {
        const oscillator = audioContext.createOscillator();
        oscillator.connect(master);

        if (type === "bomb") {
          oscillator.type = "sawtooth";
          oscillator.frequency.setValueAtTime(150, now);
          oscillator.frequency.exponentialRampToValueAtTime(
            38, now + 0.2
          );
        } else {
          oscillator.type = "sine";
          oscillator.frequency.setValueAtTime(660, now);
          oscillator.frequency.exponentialRampToValueAtTime(
            440, now + 0.09
          );
        }

        oscillator.start(now);
        oscillator.stop(now + 0.22);
      }
    } catch (error) {
      // 音声非対応でもゲームを続行する
    }
  }

  function setMessage(message) {
    $("game-message").textContent = message;
  }

  function updateHud() {
    if (!game) return;

    let flags = 0;

    for (const row of game.flags) {
      for (const value of row) {
        if (value) flags++;
      }
    }

    $("flag-count").textContent =
      String(flags).padStart(3, "0");

    $("bomb-count").textContent =
      String(game.bombs).padStart(3, "0");

    $("timer").textContent =
      String(elapsedSeconds()).padStart(3, "0");
  }

  function elapsedSeconds() {
    if (!game || game.startedAt === null) return 0;

    const end = game.endedAt ?? Date.now();
    return Math.floor((end - game.startedAt) / 1000);
  }

  function startTimer() {
    stopTimer();
    timerId = window.setInterval(updateHud, 250);
  }

  function stopTimer() {
    if (timerId !== null) {
      window.clearInterval(timerId);
    }
    timerId = null;
  }

  function drawBomb(x, y, size) {
    ctx.fillStyle = COLORS.bomb;
    ctx.beginPath();
    ctx.arc(
      x + size / 2,
      y + size / 2,
      size * 0.25,
      0,
      Math.PI * 2
    );
    ctx.fill();

    ctx.strokeStyle = "#9d3038";
    ctx.lineWidth = Math.max(1, size * 0.035);
    ctx.stroke();

    for (let i = 0; i < 4; i++) {
      const a = i * Math.PI / 2;

      ctx.beginPath();
      ctx.moveTo(
        x + size / 2 + Math.cos(a) * size * 0.27,
        y + size / 2 + Math.sin(a) * size * 0.27
      );
      ctx.lineTo(
        x + size / 2 + Math.cos(a) * size * 0.36,
        y + size / 2 + Math.sin(a) * size * 0.36
      );
      ctx.stroke();
    }
  }

  function drawFlag(x, y, size) {
    ctx.fillStyle = COLORS.flag;
    ctx.beginPath();
    ctx.moveTo(x + size * 0.30, y + size * 0.20);
    ctx.lineTo(x + size * 0.72, y + size * 0.36);
    ctx.lineTo(x + size * 0.30, y + size * 0.53);
    ctx.closePath();
    ctx.fill();

    ctx.strokeStyle = "#f8b1a8";
    ctx.lineWidth = Math.max(1, size * 0.045);

    ctx.beginPath();
    ctx.moveTo(x + size * 0.29, y + size * 0.18);
    ctx.lineTo(x + size * 0.29, y + size * 0.78);
    ctx.lineTo(x + size * 0.72, y + size * 0.78);
    ctx.stroke();
  }

  function drawQuestion(x, y, size) {
    ctx.font =
      `800 ${Math.max(12, Math.floor(size * 0.72))}px "Barlow Condensed", sans-serif`;

    ctx.textAlign = "center";
    ctx.textBaseline = "middle";
    ctx.lineWidth = Math.max(2, size * 0.09);
    ctx.strokeStyle = "#4b586b";

    ctx.strokeText(
      "?", x + size / 2,
      y + size / 2 + size * 0.035
    );

    ctx.fillStyle = COLORS.question;
    ctx.fillText(
      "?", x + size / 2,
      y + size / 2 + size * 0.035
    );
  }

  function pathPolygon(vertices) {
    ctx.beginPath();

    vertices.forEach(([x, y], i) => {
      if (i === 0) ctx.moveTo(x, y);
      else ctx.lineTo(x, y);
    });

    ctx.closePath();
  }

  function renderBoard() {
    if (!game) return;

    const dpr = Math.max(
      1, Math.min(2, window.devicePixelRatio || 1)
    );

    canvas.width = Math.round(game.width * dpr);
    canvas.height = Math.round(game.height * dpr);

    applyZoomSize();

    ctx.setTransform(dpr, 0, 0, dpr, 0, 0);
    ctx.clearRect(0, 0, game.width, game.height);

    const numberColors = [
      "",
      "#2464d8",
      "#21834a",
      "#d83d42",
      "#7650bd",
      "#a45c32",
      "#168b9b",
      "#27313e",
      "#687386"
    ];

    for (const cell of game.cells) {
      const y = cell.row;
      const x = cell.col;
      const opened = game.opened[y][x];
      const size = cell.unit;

      pathPolygon(cell.vertices);

      ctx.fillStyle = opened
        ? COLORS.opened : COLORS.closed;

      ctx.fill();

      if (!opened) {
        ctx.save();
        pathPolygon(cell.vertices);
        ctx.clip();

        ctx.fillStyle = COLORS.closedLight;
        ctx.globalAlpha = 0.13;

        ctx.fillRect(
          cell.box.minX,
          cell.box.minY,
          cell.box.maxX - cell.box.minX,
          (cell.box.maxY - cell.box.minY) * 0.28
        );

        ctx.restore();
      }

      if (opened && game.board[y][x] === -1) {
        drawBomb(
          cell.cx - size / 2,
          cell.cy - size / 2,
          size
        );
      } else if (opened && game.board[y][x] > 0) {
        const value = game.board[y][x];

        ctx.fillStyle =
          numberColors[value] || "#182333";

        ctx.font =
          `700 ${Math.max(10, Math.floor(size * 0.48))}px "Noto Sans JP", sans-serif`;

        ctx.textAlign = "center";
        ctx.textBaseline = "middle";
        ctx.fillText(String(value), cell.cx, cell.cy + 1);
      } else if (!opened && game.flags[y][x]) {
        drawFlag(
          cell.cx - size / 2,
          cell.cy - size / 2,
          size
        );
      } else if (!opened && game.questions[y][x]) {
        drawQuestion(
          cell.cx - size / 2,
          cell.cy - size / 2,
          size
        );
      }

      pathPolygon(cell.vertices);

      ctx.strokeStyle = opened
        ? COLORS.openedEdge : COLORS.grid;

      ctx.lineWidth = Math.max(
        1, size >= 30 ? 1.5 : 1
      );

      ctx.stroke();
    }
  }

  // マスのクリック判定
  function isInsidePolygon(x, y, vertices) {
    let insidePoly = false;

    for (
      let i = 0, j = vertices.length - 1;
      i < vertices.length;
      j = i++
    ) {
      const [xi, yi] = vertices[i];
      const [xj, yj] = vertices[j];

      const cross =
        (x - xi) * (yj - yi) -
        (y - yi) * (xj - xi);

      if (
        Math.abs(cross) < 0.001 &&
        x >= Math.min(xi, xj) - 0.001 &&
        x <= Math.max(xi, xj) + 0.001 &&
        y >= Math.min(yi, yj) - 0.001 &&
        y <= Math.max(yi, yj) + 0.001
      ) {
        return true;
      }

      if (
        (yi > y) !== (yj > y) &&
        x < (xj - xi) * (y - yi) / (yj - yi) + xi
      ) {
        insidePoly = !insidePoly;
      }
    }

    return insidePoly;
  }

  function canvasCellFromEvent(event) {
    if (!game) return null;

    const rect = canvas.getBoundingClientRect();

    const px =
      (event.clientX - rect.left) *
      game.width / rect.width;

    const py =
      (event.clientY - rect.top) *
      game.height / rect.height;

    for (const cell of game.cells) {
      if (
        px < cell.box.minX ||
        px > cell.box.maxX ||
        py < cell.box.minY ||
        py > cell.box.maxY
      ) {
        continue;
      }

      if (isInsidePolygon(px, py, cell.vertices)) {
        return { x: cell.col, y: cell.row };
      }
    }

    return null;
  }

  function handleCell(event, mode) {
    if (!game || game.over) return;

    const pos = canvasCellFromEvent(event);
    if (!pos) return;

    const { x, y } = pos;

    if (!inside(y, x)) return;

    if (mode === "flag") {
      toggleFlag(y, x);
    } else if (mode === "question") {
      toggleQuestion(y, x);
    } else {
      reveal(y, x);
    }
  }

  document.addEventListener("click", () => {
    if (!clearOverlay.classList.contains("hidden")) {
      hideClearOverlay();
    }
  }, true);

  // Ctrl + ホイールで拡大縮小
  viewport.addEventListener("wheel", event => {
    if (!game || !event.ctrlKey) return;

    event.preventDefault();

    setZoom(
      game.zoom * (event.deltaY < 0 ? 1.12 : 1 / 1.12),
      event.clientX,
      event.clientY
    );
  }, { passive: false });

  // スマホのドラッグ・ピンチ操作
  const touchPoints = new Map();

  let pinchStartDistance = 0;
  let pinchStartZoom = 1;
  let suppressCurrentTouchClick = false;

  const touchDistance = (a, b) =>
    Math.hypot(a.x - b.x, a.y - b.y);

  canvas.addEventListener("pointerdown", event => {
    if (event.pointerType !== "touch" || !game) return;

    if (touchPoints.size === 0) {
      suppressCurrentTouchClick = false;
    }

    touchPoints.set(event.pointerId, {
      x: event.clientX,
      y: event.clientY,
      startX: event.clientX,
      startY: event.clientY,
      moved: false,
      hadPinch: touchPoints.size > 0
    });

    canvas.setPointerCapture(event.pointerId);

    if (touchPoints.size >= 2) {
      for (const p of touchPoints.values()) {
        p.hadPinch = true;
      }

      const [a, b] = [...touchPoints.values()];
      pinchStartDistance = Math.max(1, touchDistance(a, b));
      pinchStartZoom = game.zoom;
      suppressCurrentTouchClick = true;
    }
  });

  canvas.addEventListener("pointermove", event => {
    const p = touchPoints.get(event.pointerId);
    if (!p) return;

    const dx = event.clientX - p.x;
    const dy = event.clientY - p.y;

    p.x = event.clientX;
    p.y = event.clientY;

    if (touchPoints.size >= 2) {
      const [a, b] = [...touchPoints.values()];

      setZoom(
        pinchStartZoom *
          touchDistance(a, b) / pinchStartDistance,
        (a.x + b.x) / 2,
        (a.y + b.y) / 2
      );

      suppressCurrentTouchClick = true;
    } else if (!p.hadPinch) {
      if (
        Math.hypot(
          p.x - p.startX,
          p.y - p.startY
        ) > 7
      ) {
        p.moved = true;
      }

      if (p.moved) {
        viewport.scrollLeft -= dx;
        viewport.scrollTop -= dy;
        suppressCurrentTouchClick = true;
      }
    }
  });

  function finishTouch(event) {
    const p = touchPoints.get(event.pointerId);
    if (!p) return;

    if (p.moved || p.hadPinch) {
      suppressCurrentTouchClick = true;
    }

    touchPoints.delete(event.pointerId);

    if (touchPoints.size < 2) {
      pinchStartDistance = 0;
    }
  }

  canvas.addEventListener("pointerup", finishTouch);
  canvas.addEventListener("pointercancel", finishTouch);

  // 左クリック・タップ
  canvas.addEventListener("click", event => {
    if (event.button !== 0) return;

    const wasTouch =
      event.pointerType === "touch" ||
      event.sourceCapabilities?.firesTouchEvents;

    if (wasTouch && suppressCurrentTouchClick) return;

    handleCell(event, actionMode);
  });

  // 右クリックで旗、Shift + 右クリックで「？」
  canvas.addEventListener("contextmenu", event => {
    event.preventDefault();

    handleCell(
      event,
      event.shiftKey ? "question" : "flag"
    );
  });

  // 操作モード変更
  document.querySelectorAll("[data-action-mode]").forEach(button => {
    button.addEventListener("click", () => {
      setActionMode(button.dataset.actionMode);
    });
  });

  // タブ変更
  document.querySelectorAll("[data-tab]").forEach(button => {
    button.addEventListener("click", () => {
      switchTab(button.dataset.tab);
    });
  });

  // 図形変更
  document.querySelectorAll("[data-shape]").forEach(button => {
    button.addEventListener("click", () => {
      selectShape(button.dataset.shape);
    });
  });

  // 難易度変更
  document.querySelectorAll("[data-difficulty]").forEach(button => {
    button.addEventListener("click", () => {
      createGame(button.dataset.difficulty);
    });
  });

  $("zoom-out-button").addEventListener("click", () => {
    if (game) setZoom(game.zoom - ZOOM_STEP);
  });

  $("zoom-in-button").addEventListener("click", () => {
    if (game) setZoom(game.zoom + ZOOM_STEP);
  });

  $("zoom-reset-button").addEventListener("click", () => {
    if (game) setZoom(1);
  });

  $("restart-button").addEventListener("click", () => {
    if (game) createGame(game.mode);
  });

  $("menu-button").addEventListener("click", () => {
    hideClearOverlay();
    stopTimer();
    game = null;
    switchTab("game");
  });

  $("help-button").addEventListener("click", () => {
    $("help-dialog").showModal();
  });

  // 初期化
  selectShape(selectedShape);
  switchTab("game");
  setActionMode("open");

  window.addEventListener("resize", () => {
    if (game) renderBoard();
  });

  // キーボード操作
  window.addEventListener("keydown", event => {
    if (!game || $("help-dialog").open) return;

    if (event.key.toLowerCase() === "r") {
      createGame(game.mode);
    }

    if (event.key === "Escape") {
      $("menu-button").click();
    }
  });
})();
