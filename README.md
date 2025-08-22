<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>One Piece Gallery</title>
  <style>
    :root{--bg:#faf3dd;--ink:#222;--brand:#ffcc00;--card:#fff;--shadow:0 6px 20px rgba(0,0,0,.15)}
    *{box-sizing:border-box}
    body{margin:0;font-family:system-ui,-apple-system,Segoe UI,Roboto;background:var(--bg);color:var(--ink)}
    header{position:sticky;top:0;background:#2d2d2d;color:#fff;padding:14px 16px;text-align:center;font-weight:700;z-index:10}
    .wrap{max-width:1100px;margin:0 auto;padding:18px}
    .controls{display:flex;flex-wrap:wrap;gap:10px;justify-content:center;margin-bottom:14px}
    .controls input[type="text"]{padding:10px 12px;border:1px solid #bbb;border-radius:10px;min-width:200px}
    .controls input[type="file"]{display:none}
    .btn{background:var(--brand);border:none;padding:10px 14px;border-radius:10px;font-weight:700;cursor:pointer}
    .btn:hover{filter:brightness(1.05)}
    .grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(220px,1fr));gap:16px}
    .card{background:var(--card);border-radius:16px;box-shadow:var(--shadow);overflow:hidden;display:flex;flex-direction:column}
    .card img{width:100%;height:220px;object-fit:cover;background:#eee}
    .card h3{margin:10px 12px 0;font-size:1rem}
    .actions{display:flex;gap:10px;justify-content:center;align-items:center;padding:10px 12px 14px}
    .iconbtn{font-size:20px;border:none;background:transparent;cursor:pointer}
    .count{font-size:.95rem;margin-left:4px}
    .podium{margin-top:24px;background:#fff3cd;border:1px solid #f1d384;border-radius:16px;padding:14px;box-shadow:var(--shadow)}
    .podium h2{margin:0 0 10px;text-align:center}
    .toplist{display:flex;flex-wrap:wrap;gap:10px;justify-content:center}
    .topitem{background:#fff;border-radius:12px;padding:8px 10px;box-shadow:0 3px 10px rgba(0,0,0,.1);font-weight:700}
    footer{margin:24px 0;text-align:center;color:#555}
    .hint{font-size:.9rem;color:#666;text-align:center;margin:6px 0 16px}
  </style>
</head>
<body>
  <header>🏴‍☠️ One Piece Gallery 🏴‍☠️</header>

  <div class="wrap">
    <div class="controls">
      <input id="titleInput" type="text" placeholder="Nom du pirate (ex: Luffy)" />
      <input id="fileInput" type="file" accept="image/*" />
      <button class="btn" onclick="document.getElementById('fileInput').click()">➕ Ajouter une photo</button>
    </div>
    <p class="hint">Astuce : ajoute une photo, donne-lui un nom, puis ❤️ ou 💩. Les scores sont sauvegardés (localStorage).</p>

    <section id="gallery" class="grid"></section>

    <section class="podium">
      <h2>🏆 Top 5 des plus aimés</h2>
      <div id="toplist" class="toplist"></div>
    </section>

    <footer>🌊 One Piece Gallery • By Thomas</footer>
  </div>

  <script>
    const LS_KEY = "onepieceGallery";
    const gallery = document.getElementById("gallery");
    const fileInput = document.getElementById("fileInput");
    const titleInput = document.getElementById("titleInput");
    const toplist = document.getElementById("toplist");

    // Charger depuis le stockage
    const load = () => JSON.parse(localStorage.getItem(LS_KEY) || "[]");
    const save = (arr) => localStorage.setItem(LS_KEY, JSON.stringify(arr));
    let items = load();

    // Rendu des cartes
    function render() {
      gallery.innerHTML = "";
      items.forEach((it, i) => {
        const card = document.createElement("div");
        card.className = "card";
        card.innerHTML = `
          <img src="${it.src}" alt="${it.title}">
          <h3>${it.title}</h3>
          <div class="actions">
            <button class="iconbtn like">❤️ <span class="count">${it.likes||0}</span></button>
            <button class="iconbtn dislike">💩 <span class="count">${it.dislikes||0}</span></button>
          </div>
        `;
        const likeBtn = card.querySelector(".like");
        const dislikeBtn = card.querySelector(".dislike");
        likeBtn.onclick = () => { it.likes = (it.likes||0) + 1; save(items); render(); updateTop(); };
        dislikeBtn.onclick = () => { it.dislikes = (it.dislikes||0) + 1; save(items); render(); };
        gallery.appendChild(card);
      });
    }

    // Top 5
    function updateTop() {
      const top = [...items].sort((a,b)=> (b.likes||0)-(a.likes||0)).slice(0,5);
      toplist.innerHTML = "";
      top.forEach((it, idx) => {
        const div = document.createElement("div");
        div.className = "topitem";
        div.textContent = `#${idx+1} ${it.title} (${it.likes||0} ❤️)`;
        toplist.appendChild(div);
      });
    }

    // Ajouter une image
    fileInput.addEventListener("change", (e) => {
      const f = e.target.files[0];
      if(!f) return;
      const reader = new FileReader();
      reader.onload = ev => {
        const title = (titleInput.value || "Inconnu").trim();
        items.push({ src: ev.target.result, title, likes: 0, dislikes: 0 });
        save(items);
        titleInput.value = "";
        render(); updateTop();
      };
      reader.readAsDataURL(f);
    });

    // Premier rendu
    render(); updateTop();
  </script>
</body>
</html>
