<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>管理画面 — Pocket Board</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Noto+Sans+JP:wght@400;500;700&family=Inter:wght@400;500;600&display=swap" rel="stylesheet">
<style>
  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
  :root {
    --blue: #378ADD; --blue-dark: #185FA5; --blue-light: #E6F1FB;
    --red: #E24B4A; --red-light: #FCEBEB;
    --green: #1D9E75; --green-light: #E1F5EE;
    --bg: #F5F5F0; --surface: #fff; --border: rgba(0,0,0,0.09);
    --text-primary: #1A1A1A; --text-secondary: #555; --text-muted: #999;
    --radius: 10px; --radius-lg: 16px;
  }
  body { font-family: 'Noto Sans JP', sans-serif; background: var(--bg); color: var(--text-primary); min-height: 100vh; }

  .auth-screen { min-height: 100vh; display: flex; align-items: center; justify-content: center; padding: 20px; }
  .auth-card { background: var(--surface); border-radius: var(--radius-lg); border: 1px solid var(--border); padding: 40px 36px; max-width: 380px; width: 100%; text-align: center; }
  .auth-logo { font-family: 'Inter', sans-serif; font-size: 24px; font-weight: 700; margin-bottom: 4px; }
  .auth-sub { font-size: 13px; color: var(--text-muted); margin-bottom: 32px; }
  .auth-card label { display: block; text-align: left; font-size: 12px; font-weight: 500; color: var(--text-secondary); margin-bottom: 6px; }
  .auth-card input[type="password"] { width: 100%; padding: 10px 14px; border: 1px solid var(--border); border-radius: var(--radius); font-size: 14px; font-family: inherit; margin-bottom: 16px; outline: none; transition: border-color 0.15s; background: var(--bg); }
  .auth-card input[type="password"]:focus { border-color: var(--blue); background: #fff; }
  .btn-primary { width: 100%; padding: 12px; background: var(--blue); color: #fff; border: none; border-radius: var(--radius); font-size: 14px; font-weight: 500; cursor: pointer; font-family: inherit; transition: background 0.15s; }
  .btn-primary:hover { background: var(--blue-dark); }
  .auth-error { font-size: 12px; color: var(--red); margin-top: 10px; min-height: 16px; }

  .admin-layout { display: none; }
  .admin-layout.visible { display: block; }

  .admin-header { background: var(--surface); border-bottom: 1px solid var(--border); padding: 14px 24px; display: flex; align-items: center; justify-content: space-between; position: sticky; top: 0; z-index: 50; }
  .admin-header-left { display: flex; align-items: center; gap: 16px; }
  .admin-logo { font-family: 'Inter', sans-serif; font-size: 18px; font-weight: 700; }
  .admin-badge { font-size: 11px; background: var(--blue-light); color: var(--blue-dark); padding: 3px 10px; border-radius: 100px; font-weight: 500; }
  .back-link { font-size: 13px; color: var(--text-muted); text-decoration: none; }
  .back-link:hover { color: var(--blue); }
  .logout-btn { font-size: 12px; color: var(--text-muted); background: none; border: 1px solid var(--border); padding: 6px 14px; border-radius: 100px; cursor: pointer; font-family: inherit; transition: all 0.15s; }
  .logout-btn:hover { color: var(--red); border-color: var(--red); }

  .admin-main { max-width: 900px; margin: 0 auto; padding: 32px 20px 80px; }
  .section-title { font-size: 11px; font-weight: 700; letter-spacing: 0.1em; text-transform: uppercase; color: var(--text-muted); font-family: 'Inter', sans-serif; margin-bottom: 16px; }

  .form-card { background: var(--surface); border-radius: var(--radius-lg); border: 1px solid var(--border); padding: 24px; margin-bottom: 32px; }
  .form-card h2 { font-size: 17px; font-weight: 700; margin-bottom: 20px; }
  .form-row { margin-bottom: 16px; }
  .form-row label { display: block; font-size: 12px; font-weight: 500; color: var(--text-secondary); margin-bottom: 6px; }
  .form-row input, .form-row select, .form-row textarea { width: 100%; padding: 10px 14px; border: 1px solid var(--border); border-radius: var(--radius); font-size: 14px; font-family: inherit; outline: none; background: var(--bg); transition: border-color 0.15s, background 0.15s; }
  .form-row input:focus, .form-row select:focus, .form-row textarea:focus { border-color: var(--blue); background: #fff; }
  .form-row textarea { resize: vertical; min-height: 90px; }
  .form-2col { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; }
  .form-hint { font-size: 11px; color: var(--text-muted); margin-top: 5px; }

  /* URL input area */
  .url-input-area { display: flex; gap: 8px; align-items: flex-start; flex-wrap: wrap; }
  .url-input-area input { flex: 1; min-width: 200px; }
  .url-preview { margin-top: 8px; }
  .url-preview img { max-width: 100%; max-height: 160px; object-fit: cover; border-radius: 8px; border: 1px solid var(--border); display: block; }

  /* photos list */
  .photos-list { display: flex; flex-wrap: wrap; gap: 8px; margin-top: 8px; }
  .photo-thumb { position: relative; width: 80px; height: 80px; }
  .photo-thumb img { width: 100%; height: 100%; object-fit: cover; border-radius: 8px; border: 1px solid var(--border); }
  .photo-remove { position: absolute; top: -5px; right: -5px; width: 18px; height: 18px; background: var(--red); color: #fff; border: none; border-radius: 50%; font-size: 11px; cursor: pointer; display: flex; align-items: center; justify-content: center; line-height: 1; }
  .photo-add-row { display: flex; gap: 8px; }
  .photo-add-row input { flex: 1; }
  .btn-add-photo { padding: 10px 16px; background: none; border: 1px solid var(--border); border-radius: var(--radius); font-size: 13px; cursor: pointer; font-family: inherit; white-space: nowrap; transition: all 0.15s; }
  .btn-add-photo:hover { border-color: var(--blue); color: var(--blue); }

  .submit-row { display: flex; gap: 10px; justify-content: flex-end; margin-top: 4px; }
  .btn-secondary { padding: 10px 20px; background: none; border: 1px solid var(--border); border-radius: var(--radius); font-size: 13px; cursor: pointer; font-family: inherit; transition: background 0.15s; }
  .btn-secondary:hover { background: var(--bg); }
  .btn-submit { padding: 10px 24px; background: var(--blue); color: #fff; border: none; border-radius: var(--radius); font-size: 13px; font-weight: 500; cursor: pointer; font-family: inherit; transition: background 0.15s; }
  .btn-submit:hover { background: var(--blue-dark); }

  .posts-list { display: flex; flex-direction: column; gap: 10px; }
  .post-item { background: var(--surface); border: 1px solid var(--border); border-radius: var(--radius-lg); padding: 16px 18px; display: flex; align-items: flex-start; gap: 14px; }
  .post-item-color { width: 6px; height: 48px; border-radius: 3px; flex-shrink: 0; }
  .post-item-body { flex: 1; min-width: 0; }
  .post-item-header { display: flex; align-items: center; gap: 8px; margin-bottom: 4px; flex-wrap: wrap; }
  .post-item-title { font-size: 14px; font-weight: 700; }
  .tag { font-size: 10px; padding: 2px 8px; border-radius: 100px; font-weight: 500; flex-shrink: 0; }
  .tag-event { background: #E6F1FB; color: #185FA5; }
  .tag-circle { background: #FBEAF0; color: #993556; }
  .tag-has-pdf { background: #FEE2E2; color: #991B1B; }
  .tag-has-img { background: #E1F5EE; color: #085041; }
  .post-item-meta { font-size: 11px; color: var(--text-muted); }
  .post-item-actions { display: flex; gap: 6px; flex-shrink: 0; }
  .btn-edit { padding: 6px 12px; font-size: 12px; background: none; border: 1px solid var(--border); border-radius: 8px; cursor: pointer; font-family: inherit; transition: all 0.15s; }
  .btn-edit:hover { border-color: var(--blue); color: var(--blue); }
  .btn-delete { padding: 6px 12px; font-size: 12px; background: none; border: 1px solid var(--border); border-radius: 8px; cursor: pointer; font-family: inherit; color: var(--text-muted); transition: all 0.15s; }
  .btn-delete:hover { border-color: var(--red); color: var(--red); background: var(--red-light); }

  .toast { position: fixed; bottom: 24px; right: 24px; background: #1A1A1A; color: #fff; padding: 12px 20px; border-radius: var(--radius); font-size: 13px; transform: translateY(80px); opacity: 0; transition: all 0.25s; z-index: 999; pointer-events: none; }
  .toast.show { transform: translateY(0); opacity: 1; }
  .toast.success { background: var(--green); }
  .toast.error { background: var(--red); }

  .notice { background: #FFF9E6; border: 1px solid #F0D070; border-radius: var(--radius); padding: 14px 16px; font-size: 13px; color: #7A5C00; margin-bottom: 24px; line-height: 1.7; }
  .notice strong { font-weight: 700; }

  .divider { border: none; border-top: 1px solid var(--border); margin: 20px 0; }

  @media (max-width: 600px) {
    .form-2col { grid-template-columns: 1fr; }
    .admin-header { padding: 12px 14px; }
    .admin-main { padding: 20px 12px 60px; }
    .post-item { flex-wrap: wrap; }
    .post-item-actions { width: 100%; justify-content: flex-end; }
  }
</style>
</head>
<body>

<div class="auth-screen" id="auth-screen">
  <div class="auth-card">
    <div class="auth-logo">Pocket Board</div>
    <div class="auth-sub">管理者ページ</div>
    <label for="pw-input">パスワード</label>
    <input type="password" id="pw-input" placeholder="パスワードを入力" onkeydown="if(event.key==='Enter')login()">
    <button class="btn-primary" onclick="login()">ログイン</button>
    <div class="auth-error" id="auth-error"></div>
  </div>
</div>

<div class="admin-layout" id="admin-layout">
  <header class="admin-header">
    <div class="admin-header-left">
      <span class="admin-logo">Pocket Board</span>
      <span class="admin-badge">管理画面</span>
      <a href="/" class="back-link">← サイトを見る</a>
    </div>
    <button class="logout-btn" onclick="logout()">ログアウト</button>
  </header>

  <main class="admin-main">
    <div class="notice">
      <strong>📌 使い方：</strong>フォームで追加・編集後、「posts.json をダウンロード」→ GitHubの <code>posts/</code> フォルダに上書き → GitHub Desktop でプッシュするとサイトに反映されます。<br>
      <strong>🖼 画像・PDF：</strong>GitHubの <code>posts/</code> フォルダにファイルをアップロードし、URL欄に <code>/posts/ファイル名.jpg</code> と入力してください。
    </div>

    <div class="form-card" id="form-card">
      <h2 id="form-title">✏️ 新しいお知らせを追加</h2>
      <input type="hidden" id="edit-id">

      <div class="form-2col">
        <div class="form-row">
          <label>カテゴリ *</label>
          <select id="f-category">
            <option value="event">📅 イベント・行事</option>
            <option value="circle">🌸 サークル・学生発信</option>
          </select>
        </div>
        <div class="form-row">
          <label>期限（任意）</label>
          <input type="date" id="f-deadline">
        </div>
      </div>

      <div class="form-row">
        <label>タイトル *</label>
        <input type="text" id="f-title" placeholder="例：軽音楽部 新入部員募集！">
      </div>

      <div class="form-2col">
        <div class="form-row">
          <label>サブタイトル（バナーに表示）</label>
          <input type="text" id="f-eyebrow" placeholder="例：サークル募集">
        </div>
        <div class="form-row">
          <label>主催・団体名</label>
          <input type="text" id="f-org" placeholder="例：軽音楽部">
        </div>
      </div>

      <div class="form-row">
        <label>詳細説明</label>
        <textarea id="f-description" placeholder="お知らせの詳細を入力してください…"></textarea>
      </div>

      <hr class="divider">

      <!-- メイン画像 -->
      <div class="form-row">
        <label>🖼 メイン画像URL（バナー・詳細の上部に大きく表示）</label>
        <div class="url-input-area">
          <input type="text" id="f-image" placeholder="/posts/image.jpg または https://…" oninput="previewImage(this.value)">
        </div>
        <div class="url-preview" id="image-preview"></div>
        <p class="form-hint">GitHubの posts/ フォルダにアップした画像は <code>/posts/ファイル名.jpg</code> で参照できます</p>
      </div>

      <!-- PDF -->
      <div class="form-row">
        <label>📄 PDFビラURL</label>
        <input type="text" id="f-pdf" placeholder="/posts/flyer.pdf または https://…">
        <p class="form-hint">GitHubの posts/ フォルダにPDFをアップして <code>/posts/ファイル名.pdf</code> と入力</p>
      </div>

      <!-- 追加写真 -->
      <div class="form-row">
        <label>📷 追加写真（複数枚・詳細画面にギャラリー表示）</label>
        <div class="photos-list" id="photos-list"></div>
        <div class="photo-add-row" style="margin-top:8px">
          <input type="text" id="f-photo-url" placeholder="/posts/photo1.jpg または https://…">
          <button class="btn-add-photo" onclick="addPhoto()">＋ 追加</button>
        </div>
        <p class="form-hint">写真を複数枚載せられます。1枚ずつURLを追加してください</p>
      </div>

      <div class="submit-row">
        <button class="btn-secondary" onclick="resetForm()">キャンセル</button>
        <button class="btn-submit" onclick="savePost()">保存する</button>
      </div>
    </div>

    <div class="section-title">現在の投稿一覧</div>
    <div class="posts-list" id="posts-list"></div>

    <div style="margin-top: 28px; text-align: right;">
      <button class="btn-submit" onclick="exportJSON()" style="background:#1A1A1A">
        📥 posts.json をダウンロード
      </button>
    </div>
  </main>
</div>

<div class="toast" id="toast"></div>

<script>
const ADMIN_PASSWORD = 'pocketboard2025';
let posts = [];
let currentPhotos = [];

function login() {
  const pw = document.getElementById('pw-input').value;
  if (pw === ADMIN_PASSWORD) {
    sessionStorage.setItem('pb_auth', '1');
    document.getElementById('auth-screen').style.display = 'none';
    document.getElementById('admin-layout').classList.add('visible');
    loadPosts();
  } else {
    document.getElementById('auth-error').textContent = 'パスワードが違います';
  }
}
function logout() { sessionStorage.removeItem('pb_auth'); location.reload(); }
if (sessionStorage.getItem('pb_auth') === '1') {
  document.getElementById('auth-screen').style.display = 'none';
  document.getElementById('admin-layout').classList.add('visible');
}

async function loadPosts() {
  try {
    const res = await fetch('/posts/posts.json?t=' + Date.now());
    const data = await res.json();
    posts = data.posts || [];
  } catch(e) { posts = []; }
  renderList();
}

function renderList() {
  const el = document.getElementById('posts-list');
  if (!posts.length) { el.innerHTML = '<p style="color:var(--text-muted);font-size:14px;padding:20px 0">投稿がありません</p>'; return; }
  const sorted = [...posts].sort((a,b) => new Date(b.date||0) - new Date(a.date||0));
  const COLORS = ['#534AB7','#0F6E56','#993C1D','#185FA5','#993556','#854F0B','#3B6D11'];
  el.innerHTML = sorted.map((p, i) => {
    const hasPdf = p.pdf ? '<span class="tag tag-has-pdf">PDF</span>' : '';
    const hasImg = p.image ? '<span class="tag tag-has-img">画像</span>' : '';
    const hasPhotos = (p.photos && p.photos.length) ? `<span class="tag tag-has-img">写真${p.photos.length}枚</span>` : '';
    return `
    <div class="post-item">
      <div class="post-item-color" style="background:${COLORS[i % COLORS.length]}"></div>
      <div class="post-item-body">
        <div class="post-item-header">
          <span class="post-item-title">${p.title}</span>
          <span class="tag ${p.category === 'event' ? 'tag-event' : 'tag-circle'}">${p.category === 'event' ? 'イベント' : 'サークル'}</span>
          ${hasPdf}${hasImg}${hasPhotos}
        </div>
        <div class="post-item-meta">${p.org || '—'} ${p.deadline ? '｜期限 ' + p.deadline : ''}</div>
      </div>
      <div class="post-item-actions">
        <button class="btn-edit" onclick="editPost(${p.id})">編集</button>
        <button class="btn-delete" onclick="deletePost(${p.id})">削除</button>
      </div>
    </div>`;
  }).join('');
}

function previewImage(url) {
  const el = document.getElementById('image-preview');
  if (url) { el.innerHTML = `<img src="${url}" alt="プレビュー" onerror="this.style.display='none'">`; }
  else { el.innerHTML = ''; }
}

function addPhoto() {
  const url = document.getElementById('f-photo-url').value.trim();
  if (!url) return;
  currentPhotos.push(url);
  document.getElementById('f-photo-url').value = '';
  renderPhotosList();
}

function removePhoto(idx) {
  currentPhotos.splice(idx, 1);
  renderPhotosList();
}

function renderPhotosList() {
  const el = document.getElementById('photos-list');
  el.innerHTML = currentPhotos.map((url, i) => `
    <div class="photo-thumb">
      <img src="${url}" alt="写真" onerror="this.style.opacity=0.3">
      <button class="photo-remove" onclick="removePhoto(${i})">×</button>
    </div>`).join('');
}

function resetForm() {
  document.getElementById('edit-id').value = '';
  ['f-title','f-eyebrow','f-org','f-description','f-image','f-pdf','f-photo-url'].forEach(id => {
    const el = document.getElementById(id);
    if (el) el.value = '';
  });
  document.getElementById('f-deadline').value = '';
  document.getElementById('f-category').value = 'event';
  document.getElementById('form-title').textContent = '✏️ 新しいお知らせを追加';
  document.getElementById('image-preview').innerHTML = '';
  currentPhotos = [];
  renderPhotosList();
}

function editPost(id) {
  const p = posts.find(x => x.id == id);
  if (!p) return;
  document.getElementById('edit-id').value = p.id;
  document.getElementById('f-title').value = p.title || '';
  document.getElementById('f-eyebrow').value = p.eyebrow || '';
  document.getElementById('f-org').value = p.org || '';
  document.getElementById('f-description').value = p.description || '';
  document.getElementById('f-deadline').value = p.deadline || '';
  document.getElementById('f-category').value = p.category || 'event';
  document.getElementById('f-image').value = p.image || '';
  document.getElementById('f-pdf').value = p.pdf || '';
  currentPhotos = [...(p.photos || [])];
  previewImage(p.image || '');
  renderPhotosList();
  document.getElementById('form-title').textContent = '✏️ 投稿を編集';
  document.getElementById('form-card').scrollIntoView({ behavior: 'smooth' });
}

function savePost() {
  const title = document.getElementById('f-title').value.trim();
  if (!title) { showToast('タイトルを入力してください', 'error'); return; }
  const editId = document.getElementById('edit-id').value;
  const postData = {
    id: editId ? parseInt(editId) : Date.now(),
    title,
    category: document.getElementById('f-category').value,
    eyebrow: document.getElementById('f-eyebrow').value.trim(),
    org: document.getElementById('f-org').value.trim(),
    description: document.getElementById('f-description').value.trim(),
    deadline: document.getElementById('f-deadline').value,
    date: new Date().toISOString().split('T')[0],
    image: document.getElementById('f-image').value.trim(),
    pdf: document.getElementById('f-pdf').value.trim(),
    photos: [...currentPhotos]
  };
  if (editId) { posts = posts.map(p => p.id == editId ? postData : p); }
  else { posts.unshift(postData); }
  renderList();
  resetForm();
  showToast('保存しました ✓ posts.json をダウンロードしてGitHubへ', 'success');
}

function deletePost(id) {
  if (!confirm('この投稿を削除しますか？')) return;
  posts = posts.filter(p => p.id != id);
  renderList();
  showToast('削除しました', 'success');
}

function exportJSON() {
  const json = JSON.stringify({ posts }, null, 2);
  const blob = new Blob([json], { type: 'application/json' });
  const a = document.createElement('a');
  a.href = URL.createObjectURL(blob);
  a.download = 'posts.json';
  a.click();
  showToast('posts.json をダウンロードしました', 'success');
}

function showToast(msg, type = '') {
  const t = document.getElementById('toast');
  t.textContent = msg;
  t.className = 'toast show ' + type;
  setTimeout(() => { t.className = 'toast'; }, 4000);
}

if (sessionStorage.getItem('pb_auth') === '1') loadPosts();
</script>
</body>
</html>
