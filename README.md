<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Us, so far</title>
<script>document.documentElement.classList.add("js");</script>
<!-- FONTS: If you want to use different Google fonts, change these links -->
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Caveat:wght@500;700&family=Young+Serif&display=swap" rel="stylesheet">
<style>
  :root {
    /* --- CUSTOMIZE COLORS HERE --- */
    --linen: #F3EFF6;   /* Main background color at the top of the page */
    --ink: #2B1D3C;     /* Main text color (dark purple/black) */
    --thread: #D7263D;  /* The main red color for the timeline thread, hearts, and highlights */
    --blush: #F4A0B4;   /* Light pink used for gradients */
    --gold: #F2B134;    /* Accent color (used for outline when clicking elements) */
    --mist: #6F6784;    /* Muted text color for dates and footer */
    --paper: #FFFFFF;   /* Background color for the polaroid photos and the final note */
    
    /* --- CUSTOMIZE FONTS HERE --- */
    --serif: "Young Serif", Georgia, "Times New Roman", serif; /* Used for headings and regular text */
    --hand: "Caveat", "Segoe Script", "Bradley Hand", cursive; /* Used for dates, the letter, and signatures */
  }

  *, *::before, *::after { box-sizing: border-box; }
  html { scroll-behavior: smooth; }
  body {
    margin: 0;
    background: var(--linen);
    color: var(--ink);
    font-family: var(--serif);
    line-height: 1.6;
    -webkit-font-smoothing: antialiased;
  }
  header, main, footer { position: relative; z-index: 1; }

  :focus-visible { outline: 3px solid var(--gold); outline-offset: 3px; }

  /* --- PETAL CANVAS (The falling petals effect happens in this invisible box behind everything) --- */
  #petals {
    position: fixed;
    inset: 0;
    width: 100%;
    height: 100%;
    z-index: 0;
    pointer-events: none;
  }

  /* ---------- Opening Header Section ---------- */
  .opening {
    min-height: 88vh;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    text-align: center;
    padding: 4rem 1.5rem 3rem;
  }
  .opening h1 { font-size: clamp(3rem, 11vw, 7rem); line-height: 1; margin: 0 0 1.25rem; letter-spacing: -0.01em; }
  .opening .for { font-family: var(--hand); font-size: clamp(1.6rem, 4vw, 2.2rem); color: var(--thread); margin: 0 0 1.5rem; }
  .opening .days { max-width: 30rem; margin: 0; color: var(--mist); font-size: 1.1rem; }
  .opening .days strong { color: var(--ink); font-weight: 400; }

  /* ---------- Timeline Thread ---------- */
  .timeline { position: relative; max-width: 62rem; margin: 0 auto; padding: 2rem 1.5rem 4rem; }
  .thread, .thread-fill { position: absolute; top: 0; left: 50%; width: 4px; margin-left: -2px; border-radius: 2px; }
  .thread { bottom: 0; background: rgba(215, 38, 61, 0.18); /* Faint track line */ }
  .thread-fill { height: 0; background: linear-gradient(var(--blush), var(--thread) 30%); /* Bright colored line that fills up */ }

  /* The glowing heart icon that rides down the thread */
  .thread-tip {
    position: absolute; left: 50%; top: 0; translate: -50% -50%;
    z-index: 2; font-size: 1.5rem; line-height: 1; color: var(--thread);
    text-shadow: 0 0 10px rgba(215, 38, 61, 0.7), 0 0 22px rgba(244, 160, 180, 0.9);
    pointer-events: none; opacity: 0; transition: opacity 0.4s;
    animation: heartbeat 1.6s ease-in-out infinite;
  }
  .thread-tip.on { opacity: 1; }
  @keyframes heartbeat { 0%, 60%, 100% { scale: 1; } 15% { scale: 1.3; } 30% { scale: 1; } 42% { scale: 1.18; } }

  /* ---------- Layout for each Timeline Moment ---------- */
  .moment { position: relative; display: grid; grid-template-columns: 1fr 56px 1fr; align-items: start; margin-bottom: 5rem; }
  .moment .knot { grid-column: 2; grid-row: 1; justify-self: center; margin-top: 1.4rem; }
  .moment .entry { grid-row: 1; }
  
  /* Alters sides (left/right) for odd vs even items */
  .moment:nth-child(odd) .entry { grid-column: 1; text-align: right; }
  .moment:nth-child(even) .entry { grid-column: 3; text-align: left; }

  /* The little circles (knots) on the thread */
  .knot {
    position: relative; z-index: 1; width: 22px; height: 22px; border-radius: 50%;
    border: 4px solid var(--thread); background: var(--linen);
    transition: background-color 0.35s, transform 0.35s, box-shadow 0.6s;
  }
  .knot.tied {
    background: var(--thread); transform: scale(1.25);
    box-shadow: 0 0 0 6px rgba(215, 38, 61, 0.14), 0 0 20px rgba(215, 38, 61, 0.55);
  }

  .entry .when { font-family: var(--hand); font-size: 1.8rem; line-height: 1.1; color: var(--thread); margin: 0 0 0.25rem; }
  .entry h2 { font-size: 1.7rem; line-height: 1.2; margin: 0 0 0.6rem; font-weight: 400; }
  .entry .story { margin: 0 0 1.25rem; max-width: 30ch; color: rgba(43, 29, 60, 0.85); }
  .moment:nth-child(odd) .story { margin-left: auto; }

  /* Animation for text fading in */
  .js .reveal { opacity: 0; transition: opacity 1.1s ease; }
  .js .moment.seen .reveal { opacity: 1; }
  .js .moment.seen .when { transition-delay: 0s; }
  .js .moment.seen h2 { transition-delay: 0.15s; }
  .js .moment.seen .story { transition-delay: 0.3s; }

  /* --- VISUALS CONTAINER --- */
  .visual-container {
    display: flex;
    flex-direction: column;
    gap: 1.5rem; /* Space between multiple photos if they are stacked */
  }

  /* --- SHARED STYLES FOR PHOTOS & LETTERS --- */
  .photo, .letter-card {
    display: inline-block; width: min(100%, 22rem); margin: 0;
    box-shadow: 0 6px 18px rgba(43, 29, 60, 0.16);
    rotate: -2deg; translate: 0 var(--py, 0px);
    transition: opacity 1.2s ease, filter 2.2s ease, scale 1.4s cubic-bezier(0.2, 0.7, 0.2, 1), rotate 0.5s ease, box-shadow 0.5s;
  }
  
  /* Give stacked photos slightly messy, varied rotations so they look like a real pile */
  .photo:nth-child(2) { rotate: 1.5deg; }
  .photo:nth-child(3) { rotate: -1deg; }
  .moment:nth-child(even) .photo:nth-child(1), .moment:nth-child(even) .letter-card { rotate: 1.8deg; }
  .moment:nth-child(even) .photo:nth-child(2) { rotate: -1.2deg; }
  .moment:nth-child(even) .photo:nth-child(3) { rotate: 2deg; }
  
  /* Straighten out whichever photo you hover over */
  .photo:hover, .letter-card:hover { rotate: 0deg !important; box-shadow: 0 12px 28px rgba(215, 38, 61, 0.22); z-index: 2; position: relative; }

  .js .photo, .js .letter-card { opacity: 0; scale: 0.93; filter: sepia(1) saturate(1.6) blur(7px) brightness(1.25); }
  .js .moment.seen .photo, .js .moment.seen .letter-card { opacity: 1; scale: 1; filter: none; transition-delay: 0.55s, 0.55s, 0.55s, 0s, 0s; }

  /* --- SPECIFIC STYLES FOR POLAROID PHOTOS --- */
  .photo { background: var(--paper); padding: 10px 10px 40px; }
  .photo img, .photo .blank { display: block; width: 100%; aspect-ratio: 4 / 3; object-fit: cover; background: #E6E0EE; }
  .photo .blank {
    display: grid; place-items: center; padding: 1rem; text-align: center;
    border: 2px dashed rgba(111, 103, 132, 0.5); font-family: var(--hand); font-size: 1.3rem; color: var(--mist); line-height: 1.2;
  }

  /* --- SPECIFIC STYLES FOR HANDWRITTEN LETTER CARDS --- */
  .letter-card {
    background: #fdfbf7; 
    /* This gradient creates the notebook paper lines */
    background-image: repeating-linear-gradient(transparent, transparent 1.6rem, rgba(111, 103, 132, 0.15) 1.6rem, rgba(111, 103, 132, 0.15) 1.65rem);
    padding: 1.8rem 1.5rem; font-family: var(--hand); font-size: 1.4rem; color: var(--ink); line-height: 1.6rem;
    white-space: pre-line; text-align: left; border-radius: 3px;
  }

  /* Floating hearts animation (pops out when knot turns red) */
  .pop {
    position: fixed; z-index: 5; translate: -50% -50%;
    font-size: var(--fs, 1rem); color: var(--c, var(--thread)); pointer-events: none;
    animation: pop 1.6s ease-out forwards;
  }
  @keyframes pop {
    from { opacity: 1; transform: translate(0, 0) scale(0.5); }
    to   { opacity: 0; transform: translate(var(--dx), var(--dy)) scale(var(--s)); }
  }

  /* ---------- Finale Section (The button and the final letter) ---------- */
  .finale { text-align: center; padding-top: 1rem; }
  .finale .knot { margin: 0 auto 1.5rem; }
  
  /* This prevents the red line from overlapping the title */
  .finale h2 { 
    display: inline-block; 
    position: relative; 
    z-index: 10; 
    background: var(--linen); /* This matches the scrolling background to perfectly hide the line */
    padding: 0 1.5rem; 
    font-size: clamp(2rem, 6vw, 3.2rem); 
    font-weight: 400; 
    line-height: 1.1; 
    margin: 0 0 1.25rem; 
  }
  
  /* This brings the button in front of the red line */
  .finale button {
    position: relative;
    z-index: 10;
    font: inherit; font-size: 1.1rem; background: var(--ink); color: #F3EFF6; border: 0; border-radius: 999px;
    padding: 0.8rem 1.8rem; cursor: pointer; transition: background-color 0.2s;
  }
  .finale button:hover { background: var(--thread); }
  
  /* This brings the letter in front of the red line */
  .note {
    position: relative;
    z-index: 10;
    max-width: 34rem; margin: 2rem auto 0; padding: 2rem 1.75rem; background: var(--paper); border-radius: 6px;
    box-shadow: 0 6px 18px rgba(43, 29, 60, 0.14); text-align: left; font-size: 1.15rem; white-space: pre-line;
  }
  
  .note[hidden] { display: none; }
  .note .sign { display: block; margin-top: 1.25rem; font-family: var(--hand); font-size: 1.9rem; color: var(--thread); }
  .note.open { animation: unfold 0.6s ease-out; }
  @keyframes unfold { from { opacity: 0; transform: translateY(-10px) scale(0.98); } to { opacity: 1; transform: none; } }

  footer { text-align: center; padding: 2rem 1rem 4rem; color: var(--mist); font-family: var(--hand); font-size: 1.4rem; }

  /* Responsive layout for mobile phones */
  @media (max-width: 720px) {
    .thread, .thread-fill { left: 2.2rem; }
    .thread-tip { left: 2.2rem; }
    .timeline { padding-left: 1rem; padding-right: 1rem; }
    .moment { grid-template-columns: 40px 1fr; margin-bottom: 3.5rem; }
    .moment .knot { grid-column: 1; margin-left: 0.5rem; justify-self: start; }
    .moment:nth-child(odd) .entry, .moment:nth-child(even) .entry { grid-column: 2; text-align: left; }
    .moment:nth-child(odd) .story { margin-left: 0; }
    .finale .knot { margin-left: auto; }
  }

  /* Accessibility feature for users who prefer less animation */
  @media (prefers-reduced-motion: reduce) {
    html { scroll-behavior: auto; }
    .js .reveal, .js .photo, .js .letter-card { opacity: 1; scale: 1; filter: none; transition: none; }
    .thread-tip { animation: none; }
    .knot, .finale button { transition: none; }
    .note.open { animation: none; }
  }
</style>
</head>
<body>

<!-- HTML Structure -->
<header class="opening">
  <p class="for" id="forLine"></p>
  <h1 id="title"></h1>
  <p class="days" id="daysLine"></p>
</header>

<main>
  <section class="timeline" id="timeline" aria-label="Our timeline">
    <div class="thread" aria-hidden="true"></div>
    <div class="thread-fill" id="fill" aria-hidden="true"></div>
    <div class="thread-tip" id="tip" aria-hidden="true">&#9829;</div>
    
    <!-- JavaScript injects the timeline moments into this div -->
    <div id="moments"></div>

    <div class="finale" id="finale">
      <div class="knot" aria-hidden="true"></div>
      <h2 id="finaleTitle"></h2>
      <button type="button" id="noteBtn" aria-expanded="false" aria-controls="note"></button>
      <div class="note" id="note" hidden></div>
    </div>
  </section>
</main>

<footer id="footer"></footer>

<script>
/* =====================================================================
   EDIT THIS PART — MAIN STORY CONTENT
   ===================================================================== */
const STORY = {
  title: "Us, so far",
  herName: "Kate Ashley Chin",     // CHANGE THIS to her name
  yourName: "Jusip",   // CHANGE THIS to your name
  together: "2023-10-01",  // CHANGE THIS to your anniversary date (YYYY-MM-DD)
  finaleTitle: "Year four starts here", 
  noteButton: "Read the last page",     
  note: "Love thank you ulit for this past 3 years wala na end game na tayo tsaka madami pa tayo gagalaan mag around the world pa tayo kaya di na pwede mag hiwalay iiyak ako HAHHAAHHA.\n\n. Di talaga ko magaling sa word pero alam ko alam mo nmn na Mahal mahal kita sobra sobra Mwua Ilovee you soo much lovieee my one and only ChinChin Mwuaa",
  footer: "Happy 3rd anniversary",

  // ADD, REMOVE, OR EDIT MOMENTS IN THIS LIST
  moments: [
    { 
      date: "The beginning",  
      title: "The day we start talking",
      text: "Do you remember when we start talking it was super late and you have a gala in the morning yet we talk so long that you barely have any sleep.",
      letter: "Dear [Isay],\n\nThis is where it all began. The start of our story where the 2 person from far away and suddenly started to love each other, Thank you for staying And thank you for everything I love you" 
    },
    { 
      date: "First date",     
      title: "Our first date",
      text: "First date natin na tinaguan pa kita dahil sa hiya ko AHAHAH. ",
      // Notice 'photos' is plural here! Put as many as you want in the brackets.
      photos: [
        "1st_image.jpg", 
        "1st_image2.jpg", 
        "1st_image3.jpg"
      ]
    },
    { 
      date: "Second date",    
      title: "The day I knew for sure",
      text: "Second date yung kala natin last na yung first nasundan pa HAHAHAHA. ",
      photos: [
        "2nd_image.jpg", 
        "2nd_image2.jpg", 
        "2nd_image3.jpg"
      ] 
    },
    { 
      date: "Three years later",     
      title: "My appreciation for you",           
      text: "Everything you've brought into my life over these past three years.",
      // This creates the final paper letter just before the ending button
      letter: "My Love,\n\nLooking back at these past three years, I so greatful for everything. Thank you po sa pag intindi sakin sa pagmamahal at sa lahat ng ginawa mo para sakin \n\nThank you for every laugh, every quiet moment, and every memory we've created. You are my favorite person in the world.\n\nForever yours,\n[Your Owtett]"
    }
  ]
};
/* ===================== END OF THE PART TO EDIT ===================== */

(function () {
  const $ = (id) => document.getElementById(id);
  const reduce = window.matchMedia("(prefers-reduced-motion: reduce)").matches;

  // Set the text on the page based on your STORY object
  document.title = STORY.title;
  $("title").textContent = STORY.title;
  $("forLine").textContent = "For " + STORY.herName + ", from " + STORY.yourName;
  $("footer").textContent = STORY.footer;
  $("finaleTitle").textContent = STORY.finaleTitle;

  // Calculate "Days together" logic
  const start = new Date(STORY.together + "T00:00:00");
  const days = Math.floor((Date.now() - start.getTime()) / 86400000);
  const dateText = isNaN(start) ? "" : start.toLocaleDateString(undefined, { year: "numeric", month: "long", day: "numeric" });
  const daysEl = $("daysLine");
  if (!isNaN(start) && days > 0) {
    daysEl.append("It's been ");
    const strong = document.createElement("strong");
    strong.textContent = days.toLocaleString() + " days";
    daysEl.append(strong, " since " + dateText + ". Scroll down and follow the red thread.");
  } else {
    daysEl.textContent = "Scroll down and follow the red thread.";
  }

  // Build HTML for each moment in the timeline
  const list = $("moments");
  const items = [];
  const momentElements = [];

  function placeholder(txt) {
    const blank = document.createElement("div");
    blank.className = "blank";
    blank.textContent = txt ? "Add photo: " + txt : "Add a photo here";
    return blank;
  }

  STORY.moments.forEach(function (m) {
    const item = document.createElement("article");
    item.className = "moment";

    const knot = document.createElement("div");
    knot.className = "knot";
    knot.setAttribute("aria-hidden", "true");

    const entry = document.createElement("div");
    entry.className = "entry";

    const when = document.createElement("p");
    when.className = "when reveal";
    when.textContent = m.date;

    const h2 = document.createElement("h2");
    h2.className = "reveal";
    h2.textContent = m.title;

    const story = document.createElement("p");
    story.className = "story reveal";
    story.textContent = m.text;

    let visuals = [];

    // 1. Check if the moment uses a 'letter'
    if (m.letter) {
      let card = document.createElement("div");
      card.className = "letter-card";
      card.textContent = m.letter;
      visuals.push(card);
    } 
    // 2. Check if the moment uses an array of 'photos'
    else if (m.photos && m.photos.length > 0) {
      m.photos.forEach(function(src) {
        let fig = document.createElement("figure");
        fig.className = "photo";
        if (src) {
          let img = document.createElement("img");
          img.src = src;
          img.alt = m.title;
          img.loading = "lazy";
          img.addEventListener("error", function () { img.replaceWith(placeholder(src)); });
          fig.append(img);
        } else {
          fig.append(placeholder(""));
        }
        visuals.push(fig);
      });
    } 
    // 3. Fallback for a single 'photo'
    else if (m.photo !== undefined) {
      let fig = document.createElement("figure");
      fig.className = "photo";
      if (m.photo) {
        let img = document.createElement("img");
        img.src = m.photo;
        img.alt = m.title;
        img.loading = "lazy";
        img.addEventListener("error", function () { img.replaceWith(placeholder(m.photo)); });
        fig.append(img);
      } else {
        fig.append(placeholder(""));
      }
      visuals.push(fig);
    }

    const vContainer = document.createElement("div");
    vContainer.className = "visual-container";
    visuals.forEach(v => vContainer.append(v));

    entry.append(when, h2, story, vContainer);
    item.append(knot, entry);
    list.append(item);
    
    momentElements.push(item);
    visuals.forEach(v => items.push({ el: item, visual: v }));
  });

  // Reveal each moment as it scrolls into view (IntersectionObserver)
  if ("IntersectionObserver" in window && !reduce) {
    const io = new IntersectionObserver(function (entries) {
      entries.forEach(function (e) {
        if (e.isIntersecting) { e.target.classList.add("seen"); io.unobserve(e.target); }
      });
    }, { threshold: 0.2 });
    momentElements.forEach(function (el) { io.observe(el); });
  } else {
    momentElements.forEach(function (el) { el.classList.add("seen"); });
  }

  // --- CUSTOMIZE ANIMATION COLORS HERE ---
  const heartColors = ["#D7263D", "#F4A0B4", "#FF7A93", "#F2B134"]; 
  
  function burst(x, y, count) {
    if (reduce) return;
    for (let i = 0; i < count; i++) {
      const h = document.createElement("span");
      h.className = "pop";
      h.textContent = "\u2665";
      const angle = -Math.PI / 2 + (Math.random() - 0.5) * Math.PI * 1.1;
      const dist = 50 + Math.random() * 100;
      h.style.left = x + "px";
      h.style.top = y + "px";
      h.style.setProperty("--dx", Math.cos(angle) * dist + "px");
      h.style.setProperty("--dy", Math.sin(angle) * dist - Math.random() * 30 + "px");
      h.style.setProperty("--s", (0.9 + Math.random() * 0.9).toFixed(2));
      h.style.setProperty("--fs", (0.8 + Math.random() * 1.1).toFixed(2) + "rem");
      h.style.setProperty("--c", heartColors[Math.floor(Math.random() * heartColors.length)]);
      h.style.animationDelay = (Math.random() * 0.25).toFixed(2) + "s";
      h.addEventListener("animationend", function () { h.remove(); });
      document.body.append(h);
    }
  }

  // Final page button logic
  const btn = $("noteBtn"), note = $("note");
  btn.textContent = STORY.noteButton;
  const sign = document.createElement("span");
  sign.className = "sign";
  sign.textContent = "\u2014 " + STORY.yourName;
  note.append(document.createTextNode(STORY.note), sign);
  btn.addEventListener("click", function () {
    const open = note.hidden;
    note.hidden = !open;
    note.classList.toggle("open", open);
    btn.setAttribute("aria-expanded", String(open));
    btn.textContent = open ? "Close" : STORY.noteButton;
    if (open) {
      const r = btn.getBoundingClientRect();
      burst(r.left + r.width / 2, r.top, 18);
      note.scrollIntoView({ behavior: reduce ? "auto" : "smooth", block: "center" });
    }
  });

  // --- BACKGROUND SCROLLING COLORS ---
  const timeline = $("timeline"), fill = $("fill"), tip = $("tip");
  const knots = Array.prototype.slice.call(document.querySelectorAll(".knot"));
  const from = [243, 239, 246]; // RGB for Lilac (Top of page)
  const to = [252, 231, 237];   // RGB for Blush (Bottom of page)

  function update() {
    const vh = window.innerHeight;
    const r = timeline.getBoundingClientRect();
    const trigger = vh * 0.6;
    const filled = Math.max(0, Math.min(r.height, trigger - r.top));
    fill.style.height = filled + "px";
    tip.style.top = filled + "px";
    tip.classList.toggle("on", filled > 8 && filled < r.height - 4);

    // Tie knots as thread reaches them
    knots.forEach(function (k) {
      const kr = k.getBoundingClientRect();
      const mid = kr.top + kr.height / 2;
      const shouldTie = mid < trigger;
      if (shouldTie && !k.classList.contains("tied")) {
        k.classList.add("tied");
        if (mid > 0 && mid < vh) burst(kr.left + kr.width / 2, mid, 9);
      } else if (!shouldTie && k.classList.contains("tied")) {
        k.classList.remove("tied");
      }
    });

    // Parallax effect and Background Color Transition
    if (!reduce) {
      items.forEach(function (it) {
        const mr = it.el.getBoundingClientRect();
        const d = (mr.top + mr.height / 2 - vh / 2) / vh;
        const py = -Math.max(-1, Math.min(1, d)) * 16;
        it.visual.style.setProperty("--py", py.toFixed(1) + "px");
      });
      const max = Math.max(1, document.documentElement.scrollHeight - vh);
      const p = Math.max(0, Math.min(1, window.scrollY / max));
      const c = from.map(function (v, i) { return Math.round(v + (to[i] - v) * p); });
      document.documentElement.style.setProperty("--linen", "rgb(" + c.join(",") + ")");
      
      /* Also update the finale h2 background so it continues to hide the line smoothly */
      document.querySelector('.finale h2').style.background = "rgb(" + c.join(",") + ")";
    }
  }

  let ticking = false;
  function onScroll() {
    if (ticking) return;
    ticking = true;
    requestAnimationFrame(function () { update(); ticking = false; });
  }
  window.addEventListener("scroll", onScroll, { passive: true });
  window.addEventListener("resize", onScroll);
  update();

  // --- CUSTOMIZE FALLING PETAL COLORS HERE ---
  if (!reduce) {
    const canvas = document.createElement("canvas");
    canvas.id = "petals";
    canvas.setAttribute("aria-hidden", "true");
    document.body.prepend(canvas);
    const ctx = canvas.getContext("2d");
    
    const colors = ["rgba(215,38,61,0.5)", "rgba(244,160,180,0.65)", "rgba(255,205,215,0.75)", "rgba(242,177,52,0.4)"];
    let w = 0, h = 0, petals = [];

    function size() {
      const dpr = Math.min(window.devicePixelRatio || 1, 2);
      w = window.innerWidth; h = window.innerHeight;
      canvas.width = w * dpr; canvas.height = h * dpr;
      ctx.setTransform(dpr, 0, 0, dpr, 0, 0);
    }
    function make(initial) {
      return {
        x: Math.random() * w,
        y: initial ? Math.random() * h : -20,
        r: 6 + Math.random() * 8,
        vy: 0.3 + Math.random() * 0.6,
        vx: (Math.random() - 0.5) * 0.3,
        sway: Math.random() * 6.28,
        swaySpeed: 0.005 + Math.random() * 0.01,
        rot: Math.random() * 6.28,
        vr: (Math.random() - 0.5) * 0.03,
        c: colors[Math.floor(Math.random() * colors.length)]
      };
    }
    function init() {
      size();
      const n = w < 720 ? 16 : 30;
      petals = [];
      for (let i = 0; i < n; i++) petals.push(make(true));
    }
    init();
    window.addEventListener("resize", init);

    let boost = 0, lastY = window.scrollY;
    window.addEventListener("scroll", function () {
      const y = window.scrollY;
      boost = Math.min(4, boost + Math.abs(y - lastY) * 0.04);
      lastY = y;
    }, { passive: true });

    function frame() {
      ctx.clearRect(0, 0, w, h);
      boost *= 0.94;
      petals.forEach(function (p, i) {
        p.sway += p.swaySpeed;
        p.x += p.vx + Math.sin(p.sway) * 0.6;
        p.y += p.vy * (1 + boost);
        p.rot += p.vr;
        if (p.y > h + 20) { petals[i] = make(false); return; }
        if (p.x < -20) p.x = w + 20;
        if (p.x > w + 20) p.x = -20;
        ctx.save();
        ctx.translate(p.x, p.y);
        ctx.rotate(p.rot);
        ctx.fillStyle = p.c;
        ctx.beginPath();
        ctx.moveTo(0, -p.r);
        ctx.bezierCurveTo(p.r, -p.r * 0.6, p.r * 0.8, p.r * 0.6, 0, p.r);
        ctx.bezierCurveTo(-p.r * 0.8, p.r * 0.6, -p.r, -p.r * 0.6, 0, -p.r);
        ctx.fill();
        ctx.restore();
      });
      requestAnimationFrame(frame);
    }
    requestAnimationFrame(frame);
  }
})();
</script>
</body>
</html>
