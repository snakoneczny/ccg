<!DOCTYPE html>
<html lang="en" class="dark">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  <!-- SEO -->
  <title>Computational Cosmology Group</title>
  <meta name="description" content="Computational Cosmology Group working on AI, cosmology and large-scale structure.">

  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600;700&display=swap" rel="stylesheet">
  <script src="https://cdn.tailwindcss.com"></script>
  <script src="https://cdn.jsdelivr.net/npm/marked/marked.min.js"></script>

  <script>
    tailwind.config = { darkMode: 'class' }
  </script>
</head>
<body class="font-[Inter] bg-gray-50 dark:bg-gray-900 text-gray-800 dark:text-gray-200">

<!-- NAV -->
<nav class="fixed top-0 left-0 w-full bg-white dark:bg-gray-900 shadow z-50 p-4 flex justify-between">
  <span class="font-bold">CCG</span>
  <div class="space-x-4">
    <a href="index.html">Home</a>
    <a href="news.html">News</a>
    <a href="projects.html">Projects</a>
    <a href="publications.html">Publications</a>
  </div>
</nav>

<div class="fixed top-4 right-4 mt-12">
  <button onclick="document.documentElement.classList.toggle('dark')" class="px-4 py-2 bg-gray-200 dark:bg-gray-700 rounded-xl">
    Toggle Mode
  </button>
</div>

<!-- HERO -->
<section class="min-h-screen flex flex-col justify-center items-center text-center px-6 bg-gradient-to-br from-indigo-900 to-black text-white">
  <h1 class="text-5xl font-bold mb-6">Computational Cosmology Group</h1>
  <p class="max-w-2xl text-lg text-gray-300">
    We explore the Universe using advanced computational methods.
  </p>
</section>

<!-- MEMBERS -->
<section class="py-20 px-6 max-w-6xl mx-auto">
  <h2 class="text-3xl font-semibold mb-12 text-center">Our Team</h2>
  <div id="members-container" class="grid md:grid-cols-3 gap-8"></div>
</section>

<!-- NEWS PREVIEW -->
<section class="py-20 px-6 max-w-6xl mx-auto">
  <h2 class="text-3xl font-semibold mb-8 text-center">Latest News</h2>
  <div id="news-preview" class="grid md:grid-cols-3 gap-6"></div>
</section>

<!-- PUBLICATIONS PREVIEW -->
<section class="py-20 px-6 max-w-5xl mx-auto">
  <h2 class="text-3xl font-semibold mb-8 text-center">Latest Publications</h2>
  <div id="publications-preview" class="space-y-4"></div>
</section>

<!-- PROJECTS PREVIEW -->
<section class="py-20 px-6 max-w-5xl mx-auto">
  <h2 class="text-3xl font-semibold mb-8 text-center">Student Projects</h2>
  <div id="projects-preview" class="space-y-4"></div>
</section>

<footer class="bg-black text-gray-400 text-center py-6">
  <p>© 2026 Computational Cosmology Group</p>
</footer>

<script>
function stripHTML(html) {
  const tmp = document.createElement("div");
  tmp.innerHTML = html;
  return tmp.textContent || "";
}

// ===== NEWS =====
fetch('data/posts.json')
  .then(res => res.json())
  .then(posts => {
    const container = document.getElementById('news-preview');
    posts.slice(0,3).forEach(post => {
      fetch(post.file)
        .then(res => res.text())
        .then(md => {
          const html = marked.parse(md || "");
          const text = stripHTML(html).substring(0,150);
          container.innerHTML += `<div class="bg-white dark:bg-gray-800 p-4 rounded">${post.title}<br>${text}...</div>`;
        });
    });
  });

// ===== PROJECTS =====
fetch('data/projects.json')
  .then(res => res.json())
  .then(projects => {
    const container = document.getElementById('projects-preview');
    projects.slice(0,3).forEach(p => {
      container.innerHTML += `<div class="bg-white dark:bg-gray-800 p-4 rounded">${p.title}</div>`;
    });
  });

// ===== ADS CACHE =====
const CACHE_KEY = "ads_cache";
const CACHE_TIME = 24 * 60 * 60 * 1000; // 24h

function loadADS() {
  const cached = localStorage.getItem(CACHE_KEY);

  if (cached) {
    const parsed = JSON.parse(cached);
    if (Date.now() - parsed.timestamp < CACHE_TIME) {
      renderADS(parsed.data);
      return;
    }
  }

  fetchADS();
}

function fetchADS() {
  const orcids = ["0000-0000-0000-0000"];
  const query = orcids.map(o => `orcid:${o}`).join(' OR ');

  fetch('https://api.adsabs.harvard.edu/v1/search/query?q=' + encodeURIComponent(query) + '&fl=title,year&rows=5', {
    headers: { 'Authorization': 'Bearer YOUR_ADS_API_KEY' }
  })
  .then(res => res.json())
  .then(data => {
    localStorage.setItem(CACHE_KEY, JSON.stringify({
      timestamp: Date.now(),
      data: data
    }));
    renderADS(data);
  })
  .catch(() => {
    document.getElementById('publications-preview').innerHTML = "ADS unavailable";
  });
}

function renderADS(data) {
  const container = document.getElementById('publications-preview');
  if (!data.response) return;

  data.response.docs.slice(0,3).forEach(p => {
    container.innerHTML += `<div class="bg-white dark:bg-gray-800 p-4 rounded">${p.title?.[0] || 'No title'} (${p.year || ''})</div>`;
  });
}

loadADS();

// ===== MEMBERS (DATA-DRIVEN) =====
fetch('data/members.json')
  .then(res => {
    if (!res.ok) throw new Error('members.json not found');
    return res.json();
  })
  .then(members => {
    const container = document.getElementById('members-container');
    members.forEach(m => {
      container.innerHTML += `
      <div class="bg-white dark:bg-gray-800 rounded-2xl shadow p-6 text-center">
        <img src="${m.image}" class="w-24 h-24 mx-auto mb-4 rounded-full object-cover">
        <h3 class="font-semibold">${m.name}</h3>
        <p class="text-sm text-gray-500">${m.role}</p>
        <p class="text-sm mt-2">${m.description || ''}</p>
        ${m.orcid ? `<a href="${m.orcid}" class="text-indigo-500 text-sm">ORCID</a>` : ''}
      </div>`;
    });
  })
  .catch(err => console.error('Members error:', err));

</script>


<!--
========================
MEMBERS DATA STRUCTURE
========================

/data/members.json

[
  {
    "name": "Prof. Wojciech Hellwing",
    "role": "Group Lead",
    "image": "assets/members/hellwing.jpg",
    "orcid": "https://orcid.org/XXXX",
    "description": "Cosmology, large-scale structure, simulations"
  }
]

========================
FOLDER STRUCTURE UPDATE
========================

/data/
  members.json
  posts.json
  projects.json

/assets/members/
  *.jpg

========================
BENEFITS
========================

✔ łatwe dodawanie członków
✔ brak edycji HTML
✔ możliwość rozbudowy (np. publikacje per osoba)
✔ kompatybilne z CMS / pipeline

-->

</body>
</html>
