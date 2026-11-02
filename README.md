<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>プライベートギャラリー</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- FontAwesome Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        /* タッチ・スワイプ操作時のスクロール・選択防止 */
        .touch-none { touch-action: none; }
        .no-select { user-select: none; -webkit-user-select: none; }
    </style>
</head>
<body class="bg-gray-950 text-gray-100 min-h-screen font-sans antialiased no-select">

    <!-- ==================== PIN認証画面 ==================== -->
    <div id="pinScreen" class="fixed inset-0 z-50 bg-gray-950 flex flex-col justify-center items-center p-4">
        <div class="w-full max-w-xs text-center space-y-6">
            <div class="w-16 h-16 bg-gray-800 rounded-full flex items-center justify-center mx-auto text-2xl text-blue-400 border border-gray-700">
                <i class="fa-solid fa-lock"></i>
            </div>
            <div>
                <h1 id="pinTitle" class="text-xl font-bold tracking-wide">PINコードを入力</h1>
                <p id="pinSub" class="text-xs text-gray-400 mt-1">4桁の暗証番号を入力してください</p>
            </div>
            
            <!-- ドット表示 -->
            <div class="flex justify-center space-x-4 my-4" id="pinDots">
                <div class="w-4 h-4 rounded-full border-2 border-gray-600 transition-all duration-200"></div>
                <div class="w-4 h-4 rounded-full border-2 border-gray-600 transition-all duration-200"></div>
                <div class="w-4 h-4 rounded-full border-2 border-gray-600 transition-all duration-200"></div>
                <div class="w-4 h-4 rounded-full border-2 border-gray-600 transition-all duration-200"></div>
            </div>

            <!-- キーパッド -->
            <div class="grid grid-cols-3 gap-3 pt-2">
                <button onclick="pressKey('1')" class="h-14 rounded-2xl bg-gray-900 border border-gray-800 text-xl font-medium active:bg-gray-800 transition">1</button>
                <button onclick="pressKey('2')" class="h-14 rounded-2xl bg-gray-900 border border-gray-800 text-xl font-medium active:bg-gray-800 transition">2</button>
                <button onclick="pressKey('3')" class="h-14 rounded-2xl bg-gray-900 border border-gray-800 text-xl font-medium active:bg-gray-800 transition">3</button>
                <button onclick="pressKey('4')" class="h-14 rounded-2xl bg-gray-900 border border-gray-800 text-xl font-medium active:bg-gray-800 transition">4</button>
                <button onclick="pressKey('5')" class="h-14 rounded-2xl bg-gray-900 border border-gray-800 text-xl font-medium active:bg-gray-800 transition">5</button>
                <button onclick="pressKey('6')" class="h-14 rounded-2xl bg-gray-900 border border-gray-800 text-xl font-medium active:bg-gray-800 transition">6</button>
                <button onclick="pressKey('7')" class="h-14 rounded-2xl bg-gray-900 border border-gray-800 text-xl font-medium active:bg-gray-800 transition">7</button>
                <button onclick="pressKey('8')" class="h-14 rounded-2xl bg-gray-900 border border-gray-800 text-xl font-medium active:bg-gray-800 transition">8</button>
                <button onclick="pressKey('9')" class="h-14 rounded-2xl bg-gray-900 border border-gray-800 text-xl font-medium active:bg-gray-800 transition">9</button>
                <div></div>
                <button onclick="pressKey('0')" class="h-14 rounded-2xl bg-gray-900 border border-gray-800 text-xl font-medium active:bg-gray-800 transition">0</button>
                <button onclick="deleteKey()" class="h-14 rounded-2xl bg-gray-900/50 text-gray-400 active:bg-gray-800 transition flex items-center justify-center"><i class="fa-solid fa-backspace"></i></button>
            </div>
        </div>
    </div>

    <!-- ==================== メインアプリ画面 ==================== -->
    <div id="mainApp" class="hidden min-h-screen flex flex-col">
        <!-- ナビゲーションヘッダー -->
        <header class="sticky top-0 z-30 bg-gray-950/80 backdrop-blur-md border-b border-gray-800 px-4 py-3 flex items-center justify-between">
            <h1 class="font-bold text-lg tracking-wide flex items-center gap-2">
                <i class="fa-solid fa-user-shield text-blue-500"></i>
                ギャラリー
            </h1>
            <div class="flex items-center gap-2">
                <!-- レイアウト切替 -->
                <button onclick="toggleLayout()" id="layoutBtn" class="px-3 py-1.5 rounded-lg bg-gray-900 border border-gray-800 text-xs font-medium text-gray-300 hover:bg-gray-800 transition flex items-center gap-1.5">
                    <i class="fa-solid fa-grip-vertical" id="layoutIcon"></i>
                    <span id="layoutText">ペア</span>
                </button>
                <!-- 追加ボタン -->
                <label class="px-3 py-1.5 rounded-lg bg-blue-600 hover:bg-blue-500 text-xs font-semibold text-white transition cursor-pointer flex items-center gap-1.5 shadow-lg shadow-blue-950">
                    <i class="fa-solid fa-plus"></i> 追加
                    <input type="file" id="imageInput" accept="image/*" multiple class="hidden" onchange="handleFileUpload(event)">
                </label>
            </div>
        </header>

        <!-- 画像ギャラリー領域 -->
        <main class="flex-1 p-3 max-w-4xl mx-auto w-full">
            <div id="galleryContainer" class="grid grid-cols-2 gap-2.5 transition-all">
                <!-- JSで動的に追加されます -->
            </div>
            <!-- Empty state -->
            <div id="emptyState" class="hidden py-24 text-center text-gray-500 space-y-3">
                <i class="fa-regular fa-images text-5xl opacity-40"></i>
                <p class="text-sm">写真が登録されていません</p>
                <p class="text-xs text-gray-600">右上「追加」から写真を選択してください</p>
            </div>
        </main>
    </div>

    <!-- ==================== ライトボックス（拡大表示モーダル） ==================== -->
    <div id="lightbox" class="fixed inset-0 z-50 bg-black/95 hidden flex flex-col justify-between items-center transition-opacity duration-300 opacity-0 overflow-hidden select-none">
        <!-- 閉じるボタン＆ヘッダー -->
        <div class="w-full flex justify-between items-center p-4 z-10 bg-gradient-to-b from-black/80 to-transparent">
            <span class="text-xs text-gray-400">ダブルタップでズーム</span>
            <button onclick="closeLightbox()" class="w-10 h-10 rounded-full bg-gray-900/80 border border-gray-700 text-white flex items-center justify-center text-lg active:scale-95 transition">
                <i class="fa-solid fa-xmark"></i>
            </button>
        </div>
        
        <!-- 中央拡大画像表示領域 -->
        <div class="flex-1 w-full flex items-center justify-center relative overflow-hidden touch-none" id="lightboxContainer">
            <img id="lightboxImage" src="" alt="拡大表示" class="max-w-full max-h-[85vh] object-contain rounded-lg shadow-2xl transition-transform duration-200 cursor-grab active:cursor-grabbing">
        </div>

        <!-- 下部ガイドバー -->
        <div class="w-full p-4 text-center text-xs text-gray-500 bg-gradient-to-t from-black/80 to-transparent z-10">
            ダブルタップで拡大・縮小 | ドラッグで移動
        </div>
    </div>

    <!-- JavaScript ロジック -->
    <script>
        // IndexedDB 設定
        let db;
        const DB_NAME = 'PrivateGalleryDB';
        const STORE_NAME = 'images';
        
        // アプリ状態
        let currentPin = '';
        let inputPin = '';
        let layoutMode = 'pair'; // 'pair' | 'single'
        let imagesData = [];

        // データベース初期化
        function initDB() {
            return new Promise((resolve, reject) => {
                const request = indexedDB.open(DB_NAME, 1);
                request.onupgradeneeded = (e) => {
                    const db = e.target.result;
                    if (!db.objectStoreNames.contains(STORE_NAME)) {
                        db.createObjectStore(STORE_NAME, { keyPath: 'id' });
                    }
                };
                request.onsuccess = (e) => {
                    db = e.target.result;
                    resolve();
                };
                request.onerror = (e) => reject(e);
            });
        }

        // 初期ロード処理
        window.onload = async () => {
            await initDB();
            currentPin = localStorage.getItem('gallery_pin') || '';
            
            if (!currentPin) {
                document.getElementById('pinTitle').textContent = 'PINコードを設定';
                document.getElementById('pinSub').textContent = '新しい4桁の暗証番号を決めてください';
            }
        };

        // PIN入力処理
        function pressKey(num) {
            if (inputPin.length < 4) {
                inputPin += num;
                updateDots();
                if (inputPin.length === 4) {
                    setTimeout(checkPin, 150);
                }
            }
        }

        function deleteKey() {
            if (inputPin.length > 0) {
                inputPin = inputPin.slice(0, -1);
                updateDots();
            }
        }

        function updateDots() {
            const dots = document.getElementById('pinDots').children;
            for (let i = 0; i < 4; i++) {
                if (i < inputPin.length) {
                    dots[i].className = 'w-4 h-4 rounded-full bg-blue-500 border-2 border-blue-500 scale-110 transition-all duration-200';
                } else {
                    dots[i].className = 'w-4 h-4 rounded-full border-2 border-gray-600 transition-all duration-200';
                }
            }
        }

        function checkPin() {
            if (!currentPin) {
                // 初回PIN設定
                currentPin = inputPin;
                localStorage.setItem('gallery_pin', currentPin);
                unlockGallery();
            } else if (inputPin === currentPin) {
                // PIN一致
                unlockGallery();
            } else {
                // PIN不一致
                inputPin = '';
                updateDots();
                const pinScreen = document.getElementById('pinScreen');
                pinScreen.classList.add('animate-bounce');
                setTimeout(() => pinScreen.classList.remove('animate-bounce'), 500);
            }
        }

        async function unlockGallery() {
            document.getElementById('pinScreen').classList.add('hidden');
            document.getElementById('mainApp').classList.remove('hidden');
            await loadImages();
        }

        // ギャラリー画像読み込み・描画
        async function loadImages() {
            const tx = db.transaction(STORE_NAME, 'readonly');
            const store = tx.objectStore(STORE_NAME);
            const request = store.getAll();

            request.onsuccess = () => {
                imagesData = request.result || [];
                renderGallery();
            };
        }

        function renderGallery() {
            const container = document.getElementById('galleryContainer');
            const emptyState = document.getElementById('emptyState');
            container.innerHTML = '';

            if (imagesData.length === 0) {
                emptyState.classList.remove('hidden');
                return;
            } else {
                emptyState.classList.add('hidden');
            }

            // レイアウト設定
            if (layoutMode === 'pair') {
                container.className = 'grid grid-cols-2 gap-2.5';
            } else {
                container.className = 'grid grid-cols-1 gap-4';
            }

            imagesData.forEach((img) => {
                const card = document.createElement('div');
                card.className = 'relative group bg-gray-900 border border-gray-800 rounded-xl overflow-hidden shadow-md flex flex-col';
                
                // アスペクト比スタイル
                const aspectClass = img.aspect === 'portrait' ? 'aspect-[3/4]' : 'aspect-[4/3]';

                card.innerHTML = `
                    <div class="relative w-full ${aspectClass} overflow-hidden bg-black cursor-pointer" onclick="openLightbox('${img.src}')">
                        <img src="${img.src}" class="w-full h-full object-cover transition duration-300 group-hover:scale-105" alt="photo">
                    </div>
                    <div class="p-2 flex justify-between items-center bg-gray-900/90 border-t border-gray-800/80 text-xs">
                        <button onclick="toggleAspect('${img.id}')" class="px-2 py-1 rounded bg-gray-800 hover:bg-gray-700 text-gray-300 transition">
                            <i class="fa-solid fa-crop-simple mr-1"></i>${img.aspect === 'portrait' ? '縦' : '横'}
                        </button>
                        <button onclick="deleteImage('${img.id}')" class="p-1 text-gray-500 hover:text-red-400 transition">
                            <i class="fa-solid fa-trash-can"></i>
                        </button>
                    </div>
                `;
                container.appendChild(card);
            });
        }

        // 写真追加処理
        async function handleFileUpload(event) {
            const files = event.target.files;
            if (!files || files.length === 0) return;

            for (let file of files) {
                const src = await readFileAsDataURL(file);
                const aspect = await detectAspectRatio(src);
                
                const imageData = {
                    id: 'img_' + Date.now() + '_' + Math.random().toString(36).substr(2, 9),
                    src: src,
                    aspect: aspect,
                    createdAt: Date.now()
                };

                const tx = db.transaction(STORE_NAME, 'readwrite');
                tx.objectStore(STORE_NAME).add(imageData);
            }

            event.target.value = '';
            await loadImages();
        }

        function readFileAsDataURL(file) {
            return new Promise((resolve) => {
                const reader = new FileReader();
                reader.onload = (e) => resolve(e.target.result);
                reader.readAsDataURL(file);
            });
        }

        function detectAspectRatio(src) {
            return new Promise((resolve) => {
                const img = new Image();
                img.onload = () => {
                    resolve(img.height > img.width ? 'portrait' : 'landscape');
                };
                img.src = src;
            });
        }

        // 手動縦横切替
        async function toggleAspect(id) {
            const img = imagesData.find(i => i.id === id);
            if (!img) return;

            img.aspect = img.aspect === 'portrait' ? 'landscape' : 'portrait';

            const tx = db.transaction(STORE_NAME, 'readwrite');
            tx.objectStore(STORE_NAME).put(img);
            await loadImages();
        }

        // 写真削除
        async function deleteImage(id) {
            const tx = db.transaction(STORE_NAME, 'readwrite');
            tx.objectStore(STORE_NAME).delete(id);
            await loadImages();
        }

        // シングル／ペア切替
        function toggleLayout() {
            layoutMode = layoutMode === 'pair' ? 'single' : 'pair';
            document.getElementById('layoutText').textContent = layoutMode === 'pair' ? 'ペア' : 'シングル';
            document.getElementById('layoutIcon').className = layoutMode === 'pair' ? 'fa-solid fa-grip-vertical' : 'fa-solid fa-square';
            renderGallery();
        }

        // --- ライトボックス（拡大表示）機能 & ダブルタップズーム ---
        let currentScale = 1;
        let lastTapTime = 0;
        let isDragging = false;
        let startX = 0, startY = 0;
        let translateX = 0, translateY = 0;

        const lightboxImg = document.getElementById('lightboxImage');
        const lightboxContainer = document.getElementById('lightboxContainer');

        function openLightbox(src) {
            const lightbox = document.getElementById('lightbox');
            lightboxImg.src = src;
            resetZoom();
            lightbox.classList.remove('hidden');
            setTimeout(() => {
                lightbox.classList.remove('opacity-0');
            }, 10);
        }

        function closeLightbox() {
            const lightbox = document.getElementById('lightbox');
            lightbox.classList.add('opacity-0');
            setTimeout(() => {
                lightbox.classList.add('hidden');
                lightboxImg.src = '';
                resetZoom();
            }, 300);
        }

        function resetZoom() {
            currentScale = 1;
            translateX = 0;
            translateY = 0;
            updateImageTransform();
        }

        function updateImageTransform() {
            if (currentScale === 1) {
                translateX = 0;
                translateY = 0;
                lightboxImg.style.transition = 'transform 0.2s ease-out';
            } else {
                lightboxImg.style.transition = isDragging ? 'none' : 'transform 0.2s ease-out';
            }
            lightboxImg.style.transform = `translate(${translateX}px, ${translateY}px) scale(${currentScale})`;
        }

        // ダブルタップ / ダブルクリック 判定
        lightboxContainer.addEventListener('click', (e) => {
            const currentTime = new Date().getTime();
            const tapLength = currentTime - lastTapTime;
            if (tapLength < 300 && tapLength > 0) {
                // ダブルタップ動作
                if (currentScale === 1) {
                    currentScale = 2.5;
                } else {
                    resetZoom();
                }
                updateImageTransform();
                e.preventDefault();
            }
            lastTapTime = currentTime;
        });

        // ドラッグ移動処理（PC）
        lightboxContainer.addEventListener('mousedown', (e) => {
            if (currentScale <= 1) return;
            isDragging = true;
            startX = e.clientX - translateX;
            startY = e.clientY - translateY;
        });

        window.addEventListener('mousemove', (e) => {
            if (!isDragging || currentScale <= 1) return;
            translateX = e.clientX - startX;
            translateY = e.clientY - startY;
            updateImageTransform();
        });

        window.addEventListener('mouseup', () => {
            isDragging = false;
        });

        // タッチ操作（スマホ）
        lightboxContainer.addEventListener('touchstart', (e) => {
            if (currentScale <= 1) return;
            if (e.touches.length === 1) {
                isDragging = true;
                startX = e.touches[0].clientX - translateX;
                startY = e.touches[0].clientY - translateY;
            }
        });

        lightboxContainer.addEventListener('touchmove', (e) => {
            if (!isDragging || currentScale <= 1) return;
            if (e.touches.length === 1) {
                translateX = e.touches[0].clientX - startX;
                translateY = e.touches[0].clientY - startY;
                updateImageTransform();
            }
        });

        lightboxContainer.addEventListener('touchend', () => {
            isDragging = false;
        });
    </script>
</body>
</html>
