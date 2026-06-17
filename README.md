<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Peta Tambang Indonesia — Jelajahi Sumber Daya Mineral</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Big+Shoulders+Display:wght@500;700;800&family=Inter:wght@400;500;600;700&family=JetBrains+Mono:wght@400;500&display=swap" rel="stylesheet">
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.9.4/leaflet.css" />
<style>
  :root{
    --basalt:#15130F;
    --basalt-2:#1B1813;
    --ash:#241F19;
    --ash-2:#2E2820;
    --mineral:#F3EEE4;
    --mineral-dim:#C7BFAF;
    --mineral-faint:#8C8576;
    --copper:#C8743D;
    --gold:#E3B548;
    --nickel:#6F9B79;
    --tin:#8FA8B8;
    --coal:#9C8467;
    --line: rgba(243,238,228,0.10);
    --line-strong: rgba(243,238,228,0.22);
    --shadow: 0 20px 50px rgba(0,0,0,0.45);
    --ease: cubic-bezier(.16,.8,.3,1);
  }

  *{box-sizing:border-box;}
  html{scroll-behavior:smooth;}
  body{
    margin:0;
    background:var(--basalt);
    color:var(--mineral);
    font-family:'Inter',system-ui,sans-serif;
    line-height:1.55;
    -webkit-font-smoothing:antialiased;
  }
  img{max-width:100%;display:block;}
  a{color:inherit;text-decoration:none;}
  h1,h2,h3{
    font-family:'Big Shoulders Display',sans-serif;
    text-transform:uppercase;
    letter-spacing:0.01em;
    margin:0;
    line-height:0.98;
  }
  .mono{font-family:'JetBrains Mono',monospace;}
  .eyebrow{
    font-family:'JetBrains Mono',monospace;
    font-size:0.72rem;
    letter-spacing:0.14em;
    text-transform:uppercase;
    color:var(--copper);
    margin:0 0 0.9rem;
  }
  :focus-visible{outline:2px solid var(--gold);outline-offset:3px;}

  /* ===== NAV ===== */
  .nav{
    position:fixed;top:0;left:0;right:0;z-index:100;
    display:flex;justify-content:center;
    padding:1.1rem 1.5rem;
    transition:padding .35s var(--ease), background .35s var(--ease);
  }
  .nav.is-scrolled{
    padding:0.6rem 1.5rem;
    background:rgba(21,19,15,0.86);
    backdrop-filter:blur(10px);
    border-bottom:1px solid var(--line);
  }
  .nav__inner{
    width:100%;max-width:1280px;
    display:flex;align-items:center;justify-content:space-between;
  }
  .nav__brand{
    font-family:'Big Shoulders Display',sans-serif;
    font-weight:800;font-size:1.25rem;letter-spacing:0.03em;
    display:flex;gap:0.35em;
  }
  .nav__brand span{color:var(--copper);}
  .nav__links{display:flex;gap:2rem;font-size:0.92rem;font-weight:500;color:var(--mineral-dim);}
  .nav__links a{position:relative;padding:0.3rem 0;transition:color .2s;}
  .nav__links a:hover{color:var(--mineral);}
  .nav__links a::after{
    content:"";position:absolute;left:0;bottom:0;width:0;height:1px;background:var(--copper);
    transition:width .25s var(--ease);
  }
  .nav__links a:hover::after{width:100%;}
  .nav__toggle{
    display:none;background:none;border:1px solid var(--line-strong);color:var(--mineral);
    width:38px;height:38px;border-radius:8px;font-size:1.1rem;cursor:pointer;
  }

  /* ===== HERO ===== */
  .hero{
    position:relative;min-height:100vh;display:flex;align-items:flex-end;
    overflow:hidden;
  }
  .hero__media{position:absolute;inset:0;}
  .hero__media img{width:100%;height:100%;object-fit:cover;object-position:center 60%;}
  .hero__scrim{
    position:absolute;inset:0;
    background:
      linear-gradient(180deg, rgba(21,19,15,0.35) 0%, rgba(21,19,15,0.45) 40%, rgba(21,19,15,0.96) 92%),
      linear-gradient(90deg, rgba(21,19,15,0.55) 0%, rgba(21,19,15,0.05) 55%);
  }
  .hero__content{position:relative;z-index:2;width:100%;max-width:1280px;margin:0 auto;padding:0 1.5rem 4.5rem;}
  .hero h1{
    font-size:clamp(2.6rem, 7.5vw, 5.6rem);
    font-weight:800;color:var(--mineral);
    max-width:14ch;
  }
  .hero__sub{
    margin:1.4rem 0 0;max-width:42ch;
    color:var(--mineral-dim);font-size:1.05rem;
  }
  .hero__stats{
    display:flex;gap:2.2rem;flex-wrap:wrap;
    margin:2.6rem 0 2.2rem;padding-top:1.8rem;
    border-top:1px solid var(--line-strong);
  }
  .stat{max-width:15rem;}
  .stat__num{
    font-family:'JetBrains Mono',monospace;font-size:1.9rem;font-weight:500;color:var(--copper);
  }
  .stat__rank{font-family:'JetBrains Mono',monospace;font-size:1.9rem;font-weight:500;color:var(--gold);}
  .stat__unit{font-size:0.85rem;color:var(--mineral-dim);margin-left:0.35rem;}
  .stat__label{display:block;font-size:0.82rem;color:var(--mineral-faint);margin-top:0.3rem;}
  .hero__scroll{
    display:inline-flex;align-items:center;gap:0.6rem;
    font-size:0.85rem;color:var(--mineral-dim);
  }
  .hero__scroll span{
    display:inline-block;animation:bob 1.8s ease-in-out infinite;
  }
  @keyframes bob{0%,100%{transform:translateY(0);}50%{transform:translateY(5px);}}

  /* ===== STRATA SIGNATURE DIVIDER ===== */
  .strata{display:flex;height:46px;}
  .strata--thin{height:22px;}
  .strata__band{
    --c:#888;
    flex:1;background:var(--c);position:relative;
    display:flex;align-items:center;justify-content:center;
    border-right:1px solid rgba(21,19,15,0.5);
  }
  .strata__band:last-child{border-right:none;}
  .strata__band span{
    font-family:'JetBrains Mono',monospace;font-weight:500;font-size:0.78rem;
    color:rgba(21,19,15,0.75);
  }
  .strata--thin .strata__band span{display:none;}
  .strata__caption{
    text-align:center;font-size:0.72rem;letter-spacing:0.08em;
    color:var(--mineral-faint);margin:0.6rem 0 0;text-transform:uppercase;
    font-family:'JetBrains Mono',monospace;
  }

  /* ===== SECTIONS ===== */
  .section{max-width:1280px;margin:0 auto;padding:6.5rem 1.5rem;}
  .section__head{max-width:46rem;margin-bottom:3rem;}
  .section__head h2{font-size:clamp(2rem,4.2vw,3.1rem);}
  .section__head p{color:var(--mineral-dim);margin-top:1rem;font-size:1rem;max-width:38rem;}

  .reveal{opacity:0;transform:translateY(22px);transition:opacity .7s var(--ease), transform .7s var(--ease);}
  .reveal.is-visible{opacity:1;transform:translateY(0);}

  /* ===== MAP TOOLBAR ===== */
  .map-toolbar{
    display:flex;flex-wrap:wrap;gap:1rem;align-items:center;justify-content:space-between;margin-bottom:1.4rem;
  }
  .chips{display:flex;flex-wrap:wrap;gap:0.6rem;}
  .chip{
    --c:#C8743D;
    font-family:'Inter',sans-serif;font-size:0.84rem;font-weight:500;
    padding:0.5rem 1rem;border-radius:100px;cursor:pointer;
    background:transparent;color:var(--mineral-dim);
    border:1px solid var(--line-strong);
    display:flex;align-items:center;gap:0.5rem;
    transition:all .2s var(--ease);
  }
  .chip::before{
    content:"";width:8px;height:8px;border-radius:50%;background:var(--c);
    box-shadow:0 0 0 0 var(--c);
  }
  .chip:hover{border-color:var(--c);color:var(--mineral);}
  .chip.is-active{background:var(--c);color:var(--basalt);border-color:var(--c);font-weight:600;}
  .chip.is-active::before{background:var(--basalt);}

  #siteSearch{
    background:var(--ash);border:1px solid var(--line-strong);color:var(--mineral);
    padding:0.55rem 1rem;border-radius:100px;font-size:0.88rem;font-family:'Inter',sans-serif;
    min-width:220px;
  }
  #siteSearch:focus{border-color:var(--copper);}
  #siteSearch::placeholder{color:var(--mineral-faint);}

  .map-wrap{
    position:relative;border:1px solid var(--line-strong);border-radius:18px;overflow:hidden;
    box-shadow:var(--shadow);
  }
  #map{height:560px;width:100%;background:var(--ash);}
  .legend{
    position:absolute;bottom:1rem;left:1rem;z-index:500;
    background:rgba(21,19,15,0.88);backdrop-filter:blur(6px);
    border:1px solid var(--line-strong);border-radius:12px;
    padding:0.8rem 1rem;font-size:0.78rem;color:var(--mineral-dim);
    display:none;
  }
  .legend.is-visible{display:block;}
  .legend__row{display:flex;align-items:center;gap:0.5rem;margin:0.25rem 0;}
  .legend__dot{width:9px;height:9px;border-radius:50%;flex:none;}
  .leaflet-popup-content-wrapper{
    background:var(--ash-2);color:var(--mineral);border-radius:10px;
  }
  .leaflet-popup-tip{background:var(--ash-2);}
  .popup img{width:100%;border-radius:6px;margin-bottom:0.5rem;height:110px;object-fit:cover;}
  .popup h4{margin:0 0 0.25rem;font-family:'Big Shoulders Display',sans-serif;font-size:1.05rem;text-transform:uppercase;}
  .popup p{margin:0 0 0.4rem;font-size:0.8rem;color:var(--mineral-dim);}
  .popup .mono{font-size:0.72rem;color:var(--mineral-faint);}

  /* ===== CARDS ===== */
  .cards{display:grid;grid-template-columns:repeat(3,1fr);gap:1.6rem;}
  .card{
    background:var(--ash);border:1px solid var(--line);border-radius:16px;overflow:hidden;
    transition:transform .35s var(--ease), border-color .35s var(--ease);
  }
  .card:hover{transform:translateY(-6px);border-color:var(--line-strong);}
  .card__media{height:220px;overflow:hidden;}
  .card__media img{width:100%;height:100%;object-fit:cover;transition:transform .6s var(--ease);}
  .card:hover .card__media img{transform:scale(1.07);}
  .card__body{padding:1.5rem;}
  .card__tag{
    --c:#C8743D;
    display:inline-block;font-family:'JetBrains Mono',monospace;font-size:0.7rem;
    letter-spacing:0.08em;text-transform:uppercase;color:var(--c);
    border:1px solid var(--c);padding:0.2rem 0.6rem;border-radius:100px;margin-bottom:0.8rem;
  }
  .card h3{font-size:1.5rem;margin-bottom:0.2rem;}
  .card__loc{color:var(--mineral-faint);font-size:0.85rem;margin:0 0 0.9rem;}
  .card__body p.desc{font-size:0.9rem;color:var(--mineral-dim);margin:0 0 1.2rem;}
  .card__stats{display:grid;gap:0.55rem;margin:0;padding-top:1rem;border-top:1px solid var(--line);}
  .card__stats div{display:flex;justify-content:space-between;font-size:0.8rem;gap:1rem;}
  .card__stats dt{color:var(--mineral-faint);}
  .card__stats dd{margin:0;text-align:right;}

  /* ===== KOMODITAS ===== */
  .commodity-grid{display:grid;grid-template-columns:repeat(4,1fr);gap:1.2rem;}
  .commodity{
    --c:#C8743D;
    background:var(--ash);border:1px solid var(--line);border-radius:16px;padding:1.6rem;
    transition:border-color .3s, transform .3s var(--ease);
  }
  .commodity:hover{border-color:var(--c);transform:translateY(-4px);}
  .commodity__icon{
    width:46px;height:46px;border-radius:10px;background:var(--c);margin-bottom:1.2rem;
    clip-path:polygon(50% 0%, 100% 25%, 100% 75%, 50% 100%, 0% 75%, 0% 25%);
  }
  .commodity h3{font-size:1.25rem;margin-bottom:0.5rem;}
  .commodity p{font-size:0.85rem;color:var(--mineral-dim);margin:0;}

  /* ===== FOOTER ===== */
  .footer{
    border-top:1px solid var(--line);padding:2.4rem 1.5rem;
    max-width:1280px;margin:0 auto;
    display:flex;justify-content:space-between;gap:1.5rem;flex-wrap:wrap;
  }
  .footer p{font-size:0.78rem;color:var(--mineral-faint);max-width:46rem;margin:0;}
  .footer .mono{color:var(--mineral-faint);font-size:0.78rem;}

  @media (max-width: 880px){
    .nav__links{
      position:absolute;top:100%;right:0;left:0;flex-direction:column;
      background:rgba(21,19,15,0.96);padding:1.2rem 1.5rem;gap:1rem;
      border-bottom:1px solid var(--line);display:none;
    }
    .nav__links.is-open{display:flex;}
    .nav__toggle{display:block;}
    .cards{grid-template-columns:1fr;}
    .commodity-grid{grid-template-columns:repeat(2,1fr);}
    #map{height:420px;}
    .hero__stats{gap:1.5rem;}
  }
  @media (prefers-reduced-motion: reduce){
    *{animation-duration:0.01ms !important;transition-duration:0.01ms !important;}
    html{scroll-behavior:auto;}
  }
</style>
</head>
<body>

<header class="nav" id="nav">
  <div class="nav__inner">
    <a class="nav__brand" href="#top">PETA<span>TAMBANG</span></a>
    <nav class="nav__links" id="navLinks">
      <a href="#peta">Peta</a>
      <a href="#profil">Profil Tambang</a>
      <a href="#komoditas">Komoditas</a>
    </nav>
    <button class="nav__toggle" id="navToggle" aria-label="Buka menu navigasi">☰</button>
  </div>
</header>

<main id="top">

  <section class="hero">
    <div class="hero__media">
      <img src="https://commons.wikimedia.org/wiki/Special:FilePath/37%20Moths%20of%20Grasberg%20Grasberg%20Mine%20Papua-Indonesia.jpg?width=1920"
           alt="Pemandangan udara tambang terbuka Grasberg di Papua, memperlihatkan terasering tambang berskala besar">
    </div>
    <div class="hero__scrim"></div>
    <div class="hero__content">
      <p class="eyebrow">Peta Sumber Daya Mineral Indonesia</p>
      <h1>Memetakan Kekayaan Bumi Indonesia</h1>
      <p class="hero__sub">Dari dataran tinggi Papua hingga perairan Bangka — jelajahi titik-titik tambang yang menggerakkan ekonomi sumber daya alam Indonesia.</p>

      <div class="hero__stats">
        <div class="stat"><span class="stat__num" data-count="1.6">0</span><span class="stat__unit">juta ton</span>
          <span class="stat__label">Produksi nikel 2022 — terbesar di dunia</span>
        </div>
        <div class="stat"><span class="stat__rank">#1</span>
          <span class="stat__label">Eksportir timah terbesar di dunia</span>
        </div>
        <div class="stat"><span class="stat__rank">Au</span>
          <span class="stat__label">Cadangan emas tunggal terbesar dunia ada di Tambang Grasberg</span>
        </div>
      </div>

      <a class="hero__scroll" href="#peta">Gulir untuk menjelajah peta <span>↓</span></a>
    </div>
  </section>

  <div class="strata" aria-hidden="true">
    <div class="strata__band" style="--c:#C8743D"><span>Cu · Au</span></div>
    <div class="strata__band" style="--c:#E3B548"><span>Au</span></div>
    <div class="strata__band" style="--c:#6F9B79"><span>Ni</span></div>
    <div class="strata__band" style="--c:#8FA8B8"><span>Sn</span></div>
    <div class="strata__band" style="--c:#9C8467"><span>C</span></div>
  </div>
  <p class="strata__caption">Tembaga &amp; Emas — Emas — Nikel — Timah — Batu Bara</p>

  <section class="section reveal" id="peta">
    <div class="section__head">
      <p class="eyebrow">01 — Jelajahi</p>
      <h2>Peta Persebaran Tambang</h2>
      <p>Tujuh kawasan tambang utama ditandai pada peta. Saring berdasarkan komoditas atau cari nama tambang, lalu klik penanda untuk melihat profil singkatnya.</p>
    </div>

    <div class="map-toolbar">
      <div class="chips" id="chips">
        <button class="chip is-active" data-filter="all" style="--c:#F3EEE4">Semua</button>
        <button class="chip" data-filter="cu-au" style="--c:#C8743D">Tembaga &amp; Emas</button>
        <button class="chip" data-filter="au" style="--c:#E3B548">Emas</button>
        <button class="chip" data-filter="ni" style="--c:#6F9B79">Nikel</button>
        <button class="chip" data-filter="sn" style="--c:#8FA8B8">Timah</button>
        <button class="chip" data-filter="coal" style="--c:#9C8467">Batu Bara</button>
      </div>
      <input type="search" id="siteSearch" placeholder="Cari nama tambang…" aria-label="Cari nama tambang">
    </div>

    <div class="map-wrap">
      <div id="map"></div>
      <div class="legend" id="legend">
        <div class="legend__row"><span class="legend__dot" style="background:#C8743D"></span>Tembaga &amp; Emas</div>
        <div class="legend__row"><span class="legend__dot" style="background:#E3B548"></span>Emas</div>
        <div class="legend__row"><span class="legend__dot" style="background:#6F9B79"></span>Nikel</div>
        <div class="legend__row"><span class="legend__dot" style="background:#8FA8B8"></span>Timah</div>
        <div class="legend__row"><span class="legend__dot" style="background:#9C8467"></span>Batu Bara</div>
      </div>
    </div>
  </section>

  <div class="strata strata--thin" aria-hidden="true">
    <div class="strata__band" style="--c:#1B1813"></div>
    <div class="strata__band" style="--c:#241F19"></div>
    <div class="strata__band" style="--c:#2E2820"></div>
    <div class="strata__band" style="--c:#241F19"></div>
    <div class="strata__band" style="--c:#1B1813"></div>
  </div>

  <section class="section reveal" id="profil">
    <div class="section__head">
      <p class="eyebrow">02 — Sorotan</p>
      <h2>Profil Tambang Utama</h2>
      <p>Tiga kawasan tambang berskala besar yang membentuk lanskap industri ekstraktif Indonesia, lengkap dengan citra dan data operasional ringkas.</p>
    </div>

    <div class="cards">
      <article class="card">
        <div class="card__media">
          <img src="https://commons.wikimedia.org/wiki/Special:FilePath/37%20Moths%20of%20Grasberg%20Grasberg%20Mine%20Papua-Indonesia.jpg?width=900" loading="lazy" alt="Tambang terbuka Grasberg, Papua">
        </div>
        <div class="card__body">
          <span class="card__tag" style="--c:#C8743D">Tembaga · Emas · Perak</span>
          <h3>Tambang Grasberg</h3>
          <p class="card__loc">Mimika, Papua Tengah</p>
          <p class="desc">Memiliki cadangan emas tunggal terbesar dan cadangan tembaga terbesar kedua di dunia. Beroperasi sebagai tambang terbuka dan bawah tanah sejak 1972.</p>
          <dl class="card__stats">
            <div><dt>Operator</dt><dd>PT Freeport Indonesia</dd></div>
            <div><dt>Produksi 2023</dt><dd>680.000 t Cu · 52,9 t Au</dd></div>
            <div><dt>Koordinat</dt><dd class="mono">4°03'S 137°07'E</dd></div>
          </dl>
        </div>
      </article>

      <article class="card">
        <div class="card__media">
          <img src="https://commons.wikimedia.org/wiki/Special:FilePath/Batu%20Hijau%20mine%20ore%20trucks.jpg?width=900" loading="lazy" alt="Truk pengangkut bijih di Tambang Batu Hijau, Sumbawa">
        </div>
        <div class="card__body">
          <span class="card__tag" style="--c:#C8743D">Tembaga · Emas · Perak</span>
          <h3>Tambang Batu Hijau</h3>
          <p class="card__loc">Sumbawa Barat, Nusa Tenggara Barat</p>
          <p class="desc">Tambang tembaga-emas porfiri terbuka terbesar kedua di Indonesia setelah Grasberg, beroperasi dengan metode truck-and-shovel sejak tahun 2000.</p>
          <dl class="card__stats">
            <div><dt>Operator</dt><dd>PT Amman Mineral N.T.</dd></div>
            <div><dt>Produksi 2022</dt><dd>463,9 juta lb Cu</dd></div>
            <div><dt>Koordinat</dt><dd class="mono">8°58'S 116°52'E</dd></div>
          </dl>
        </div>
      </article>

      <article class="card">
        <div class="card__media">
          <img src="https://commons.wikimedia.org/wiki/Special:FilePath/Coal%20Mining%20in%20East%20Kutai%2C%20East%20Kalimantan.jpg?width=900" loading="lazy" alt="Aktivitas tambang batu bara terbuka di Kutai Timur, Kalimantan Timur">
        </div>
        <div class="card__body">
          <span class="card__tag" style="--c:#9C8467">Batu Bara</span>
          <h3>Kawasan Tambang Sangatta</h3>
          <p class="card__loc">Kutai Timur, Kalimantan Timur</p>
          <p class="desc">Salah satu kawasan tambang batu bara terbuka utama di Kalimantan Timur, provinsi yang menjadi lumbung ekspor batu bara nasional Indonesia.</p>
          <dl class="card__stats">
            <div><dt>Komoditas</dt><dd>Batu bara termal</dd></div>
            <div><dt>Metode</dt><dd>Tambang terbuka</dd></div>
            <div><dt>Koordinat</dt><dd class="mono">≈0°30'S 117°33'E</dd></div>
          </dl>
        </div>
      </article>
    </div>
  </section>

  <section class="section reveal" id="komoditas">
    <div class="section__head">
      <p class="eyebrow">03 — Komoditas</p>
      <h2>Empat Mineral, Satu Peta</h2>
      <p>Setiap komoditas punya jejak geografisnya sendiri di Nusantara — berikut gambaran singkatnya.</p>
    </div>
    <div class="commodity-grid">
      <div class="commodity" style="--c:#C8743D">
        <div class="commodity__icon"></div>
        <h3>Tembaga &amp; Emas</h3>
        <p>Terkonsentrasi pada deposit porfiri di Papua dan Sumbawa — termasuk cadangan tembaga dan emas terbesar di Indonesia.</p>
      </div>
      <div class="commodity" style="--c:#6F9B79">
        <div class="commodity__icon"></div>
        <h3>Nikel</h3>
        <p>Indonesia memproduksi sekitar 1,6 juta ton nikel pada 2022, terbesar di dunia, dengan pusat tambang di Sulawesi dan Maluku Utara.</p>
      </div>
      <div class="commodity" style="--c:#8FA8B8">
        <div class="commodity__icon"></div>
        <h3>Timah</h3>
        <p>Produsen terbesar kedua dan eksportir terbesar dunia, dengan aktivitas tambang terkonsentrasi di Kepulauan Bangka Belitung.</p>
      </div>
      <div class="commodity" style="--c:#9C8467">
        <div class="commodity__icon"></div>
        <h3>Batu Bara</h3>
        <p>Tambang terbuka di Kalimantan dan Sumatra menjadikan batu bara salah satu komoditas ekspor utama Indonesia.</p>
      </div>
    </div>
  </section>

</main>

<footer class="footer">
  <p>Data dirangkum dari sumber publik (Wikipedia, laporan perusahaan tambang) per pertengahan 2026, untuk tujuan edukasi dan ilustrasi peta. Sejumlah koordinat bersifat perkiraan area, bukan titik presisi survei.</p>
  <span class="mono">PETA TAMBANG — demo halaman web</span>
</footer>

<script src="https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.9.4/leaflet.js"></script>
<script>
  // ---------- Nav scroll + mobile toggle ----------
  const nav = document.getElementById('nav');
  window.addEventListener('scroll', () => {
    nav.classList.toggle('is-scrolled', window.scrollY > 40);
  });
  const navToggle = document.getElementById('navToggle');
  const navLinks = document.getElementById('navLinks');
  navToggle.addEventListener('click', () => navLinks.classList.toggle('is-open'));
  navLinks.querySelectorAll('a').forEach(a => a.addEventListener('click', () => navLinks.classList.remove('is-open')));

  // ---------- Scroll reveal ----------
  const reveals = document.querySelectorAll('.reveal');
  const io = new IntersectionObserver((entries) => {
    entries.forEach(e => { if (e.isIntersecting) e.target.classList.add('is-visible'); });
  }, { threshold: 0.15 });
  reveals.forEach(el => io.observe(el));

  // ---------- Hero count-up ----------
  const countEl = document.querySelector('.stat__num');
  if (countEl) {
    const target = parseFloat(countEl.dataset.count);
    let current = 0;
    const step = target / 40;
    const tick = () => {
      current += step;
      if (current >= target) { countEl.textContent = target.toFixed(1); return; }
      countEl.textContent = current.toFixed(1);
      requestAnimationFrame(tick);
    };
    setTimeout(tick, 500);
  }

  // ---------- Site data ----------
  const sites = [
    {
      name: "Tambang Grasberg", loc: "Mimika, Papua Tengah", filter: "cu-au",
      lat: -4.0528, lng: 137.1158, color: "#C8743D",
      img: "https://commons.wikimedia.org/wiki/Special:FilePath/37%20Moths%20of%20Grasberg%20Grasberg%20Mine%20Papua-Indonesia.jpg?width=400",
      desc: "Cadangan emas tunggal & tembaga terbesar kedua dunia.", op: "PT Freeport Indonesia", coord: "4°03'S 137°07'E"
    },
    {
      name: "Tambang Batu Hijau", loc: "Sumbawa Barat, NTB", filter: "cu-au",
      lat: -8.967, lng: 116.867, color: "#C8743D",
      img: "https://commons.wikimedia.org/wiki/Special:FilePath/Batu%20Hijau%20mine%20ore%20trucks.jpg?width=400",
      desc: "Tambang tembaga-emas porfiri terbesar kedua RI.", op: "PT Amman Mineral N.T.", coord: "8°58'S 116°52'E"
    },
    {
      name: "Tambang Emas Martabe", loc: "Batangtoru, Tapanuli Selatan", filter: "au",
      lat: 1.75, lng: 99.25, color: "#E3B548",
      img: null,
      desc: "Tambang emas-perak epithermal di pegunungan Sumatra Utara.", op: "PT Agincourt Resources", coord: "≈1°45'N 99°15'E"
    },
    {
      name: "Tambang Nikel Sorowako", loc: "Luwu Timur, Sulawesi Selatan", filter: "ni",
      lat: -2.5736, lng: 121.3742, color: "#6F9B79",
      img: null,
      desc: "Tambang nikel laterit terbuka, beroperasi sejak 1978.", op: "PT Vale Indonesia", coord: "2°34'S 121°22'E"
    },
    {
      name: "Kawasan Tambang Timah Bangka", loc: "Pulau Bangka, Kep. Bangka Belitung", filter: "sn",
      lat: -2.1316, lng: 106.1169, color: "#8FA8B8",
      img: null,
      desc: "Pusat industri timah RI, eksportir terbesar dunia.", op: "PT Timah & tambang rakyat", coord: "≈2°08'S 106°07'E"
    },
    {
      name: "Tambang Batu Bara Sangatta", loc: "Kutai Timur, Kalimantan Timur", filter: "coal",
      lat: -0.5, lng: 117.55, color: "#9C8467",
      img: "https://commons.wikimedia.org/wiki/Special:FilePath/Coal%20Mining%20in%20East%20Kutai%2C%20East%20Kalimantan.jpg?width=400",
      desc: "Kawasan tambang batu bara terbuka utama Kaltim.", op: "Berbagai operator tambang", coord: "≈0°30'S 117°33'E"
    },
    {
      name: "Tambang Batu Bara Tanjung Enim", loc: "Muara Enim, Sumatra Selatan", filter: "coal",
      lat: -3.8, lng: 103.8, color: "#9C8467",
      img: null,
      desc: "Salah satu tambang batu bara tertua & terbesar di Sumatra.", op: "PT Bukit Asam (PTBA)", coord: "≈3°48'S 103°48'E"
    }
  ];

  // ---------- Leaflet map ----------
  const map = L.map('map', { scrollWheelZoom: false, zoomControl: true }).setView([-2.5, 117], 5);
  L.tileLayer('https://{s}.basemaps.cartocdn.com/dark_all/{z}/{x}/{y}{r}.png', {
    attribution: '&copy; OpenStreetMap, &copy; CARTO',
    maxZoom: 18
  }).addTo(map);
  map.scrollWheelZoom.disable();
  map.getContainer().addEventListener('mouseenter', () => map.scrollWheelZoom.enable());
  map.getContainer().addEventListener('mouseleave', () => map.scrollWheelZoom.disable());

  function makeIcon(color){
    return L.divIcon({
      className: '',
      html: `<div style="width:16px;height:16px;background:${color};border:2px solid #15130F;
              clip-path:polygon(50% 0%,100% 25%,100% 75%,50% 100%,0% 75%,0% 25%);
              box-shadow:0 0 0 4px rgba(243,238,228,0.12);"></div>`,
      iconSize: [16,16], iconAnchor: [8,8]
    });
  }

  const markers = sites.map(site => {
    const marker = L.marker([site.lat, site.lng], { icon: makeIcon(site.color) }).addTo(map);
    const imgHtml = site.img ? `<img src="${site.img}" alt="${site.name}">` : '';
    marker.bindPopup(`
      <div class="popup">
        ${imgHtml}
        <h4>${site.name}</h4>
        <p>${site.loc}<br>${site.desc}</p>
        <p class="mono">${site.op} · ${site.coord}</p>
      </div>
    `);
    marker._filter = site.filter;
    marker._name = site.name.toLowerCase() + ' ' + site.loc.toLowerCase();
    return marker;
  });

  // ---------- Filter + search (syncs map + cards + legend) ----------
  let activeFilter = 'all';
  const chips = document.querySelectorAll('.chip');
  const legend = document.getElementById('legend');
  const searchInput = document.getElementById('siteSearch');

  function applyFilters(){
    const q = searchInput.value.trim().toLowerCase();
    markers.forEach(m => {
      const matchFilter = activeFilter === 'all' || m._filter === activeFilter;
      const matchSearch = q === '' || m._name.includes(q);
      if (matchFilter && matchSearch){
        if (!map.hasLayer(m)) m.addTo(map);
      } else {
        if (map.hasLayer(m)) map.removeLayer(m);
      }
    });
  }

  chips.forEach(chip => {
    chip.addEventListener('click', () => {
      chips.forEach(c => c.classList.remove('is-active'));
      chip.classList.add('is-active');
      activeFilter = chip.dataset.filter;
      legend.classList.toggle('is-visible', activeFilter !== 'all');
      applyFilters();
    });
  });
  searchInput.addEventListener('input', applyFilters);
</script>

</body>
</html>
