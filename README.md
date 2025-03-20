### **Deskripsi Lengkap Source Code (SC) Tic-Tac-Toe (XO Game)**
Source code ini adalah implementasi game **Tic-Tac-Toe** atau **XO Game** berbasis web menggunakan **HTML, CSS, dan JavaScript**. Game ini mendukung **dua mode permainan**, yaitu:
1. **VS Pemain** – Dua pemain bergantian bermain di satu perangkat.
2. **VS AI** – Pemain melawan komputer dengan tingkat kesulitan yang bisa dipilih (Mudah atau Sulit).

---

## **1. Struktur Source Code**
SC ini terdiri dari satu file utama `index.html`, yang mencakup semua aspek permainan, termasuk:
- **HTML** → Struktur tampilan halaman.
- **CSS** → Desain dan animasi untuk meningkatkan tampilan game.
- **JavaScript** → Logika permainan, termasuk AI dan interaksi pengguna.

---

## **2. Desain dan Tampilan**
### **a. Warna dan Tema**
Menggunakan konsep warna berbasis variabel CSS:
- **Warna utama (primary)**: `#6a11cb`
- **Warna sekunder**: `#2575fc`
- **Latar belakang (background)**: `#f8f9fa`
- **Warna X**: `#e74c3c`
- **Warna O**: `#2980b9`
- **Efek kemenangan**: `rgba(46, 204, 113, 0.3)`

### **b. Animasi dan Efek**
- Hover efek untuk tombol dan sel game.
- Animasi kemenangan dengan `@keyframes pulse`.
- Animasi **confetti** saat pemain menang.

---

## **3. Elemen HTML**
### **a. Menu Utama**
```html
<div class="main-menu" id="main-menu">
    <h1><i class="fas fa-gamepad"></i> Tic-Tac-Toe</h1>
    <p>Game klasik 2 pemain (atau melawan AI). Siapa yang lebih dulu membentuk garis lurus menang!</p>
    <div>
        <button class="menu-btn" id="vs-player">
            <i class="fas fa-user-friends"></i> VS Pemain
        </button>
        <button class="menu-btn" id="vs-ai">
            <i class="fas fa-robot"></i> VS AI
        </button>
    </div>
</div>
```
- Berisi **judul game**, **deskripsi**, dan **dua tombol** untuk memilih mode permainan.

### **b. Papan Permainan**
```html
<div class="board" id="board">
    <div class="cell" data-index="0"></div>
    <div class="cell" data-index="1"></div>
    <div class="cell" data-index="2"></div>
    <div class="cell" data-index="3"></div>
    <div class="cell" data-index="4"></div>
    <div class="cell" data-index="5"></div>
    <div class="cell" data-index="6"></div>
    <div class="cell" data-index="7"></div>
    <div class="cell" data-index="8"></div>
</div>
```
- **Grid 3x3** untuk papan permainan, di mana setiap sel memiliki `data-index` untuk mengidentifikasi posisi.

### **c. Status dan Skor**
```html
<div class="scoreboard">
    <div class="score x">
        <h3>Player X</h3>
        <p id="score-x">0</p>
    </div>
    <div class="score draw">
        <h3>Draw</h3>
        <p id="score-draw">0</p>
    </div>
    <div class="score o">
        <h3>Player O</h3>
        <p id="score-o">0</p>
    </div>
</div>
```
- Menampilkan **skor pemain X, O, dan jumlah seri**.

---

## **4. Logika Permainan (JavaScript)**
SC ini menggunakan **event listener** untuk menangani klik pada sel papan permainan.

### **a. Struktur Data**
- `gameState`: Array menyimpan status permainan (`['X', '', 'O', ...]`).
- `currentPlayer`: Menyimpan giliran saat ini (`'X'` atau `'O'`).
- `gameMode`: Menentukan mode permainan (`'player'` atau `'ai'`).
- `scores`: Objek untuk menyimpan skor.

```js
let gameState = ['', '', '', '', '', '', '', '', ''];
let currentPlayer = 'X';
let gameMode = ''; 
let scores = { X: 0, O: 0, draw: 0 };
```

### **b. Fungsi Pergantian Pemain**
```js
function changePlayer() {
    currentPlayer = currentPlayer === 'X' ? 'O' : 'X';
    status.textContent = `${currentPlayer}'s Turn`;
}
```
- Mengganti giliran setelah pemain bergerak.

### **c. Pengecekan Kemenangan**
```js
function checkResult() {
    let roundWon = false;
    for (let i = 0; i < winningConditions.length; i++) {
        const [a, b, c] = winningConditions[i];
        if (gameState[a] && gameState[a] === gameState[b] && gameState[a] === gameState[c]) {
            roundWon = true;
            break;
        }
    }

    if (roundWon) {
        status.textContent = `${currentPlayer} Menang!`;
        gameActive = false;
        scores[currentPlayer]++;
        updateScoreboard();
        return;
    }

    let roundDraw = !gameState.includes('');
    if (roundDraw) {
        status.textContent = 'Seri!';
        gameActive = false;
        scores.draw++;
        updateScoreboard();
        return;
    }

    changePlayer();
}
```
- Mengecek kombinasi kemenangan (`winningConditions`).
- Jika ada pemenang, maka permainan berhenti.

### **d. Logika AI (Minimax)**
Untuk mode VS AI, digunakan algoritma **Minimax dengan Alpha-Beta Pruning** agar AI bisa mengambil langkah terbaik.
```js
function minimax(board, depth, isMaximizing, alpha, beta) {
    let result = checkWinner(board);
    if (result !== null) {
        return result === 'O' ? 100 - depth : result === 'X' ? depth - 100 : 0;
    }

    if (isMaximizing) {
        let bestScore = -Infinity;
        for (let i = 0; i < board.length; i++) {
            if (board[i] === '') {
                board[i] = 'O';
                let score = minimax(board, depth + 1, false, alpha, beta);
                board[i] = '';
                bestScore = Math.max(score, bestScore);
                alpha = Math.max(alpha, bestScore);
                if (beta <= alpha) break;
            }
        }
        return bestScore;
    } else {
        let bestScore = Infinity;
        for (let i = 0; i < board.length; i++) {
            if (board[i] === '') {
                board[i] = 'X';
                let score = minimax(board, depth + 1, true, alpha, beta);
                board[i] = '';
                bestScore = Math.min(score, bestScore);
                beta = Math.min(beta, bestScore);
                if (beta <= alpha) break;
            }
        }
        return bestScore;
    }
}
```
- AI akan selalu memilih langkah optimal.
- **Jika AI pertama kali jalan**, ia akan memilih posisi tengah.
- Jika tidak memungkinkan menang langsung, AI akan **menghalangi pemain**.

---

## **5. Fitur Unggulan**
✔ **Tampilan modern** dengan animasi interaktif.  
✔ **Mode VS Pemain dan VS AI** dengan tingkat kesulitan.  
✔ **Papan skor** untuk melacak hasil permainan.  
✔ **Algoritma AI dengan Minimax** untuk tingkat kesulitan tinggi.  
✔ **Efek kemenangan dan confetti** saat ada pemenang.  

---

## **Kesimpulan**
SC ini adalah implementasi **Tic-Tac-Toe berbasis web** yang lengkap dan modern. Menggunakan **Bootstrap, Font Awesome, CSS, dan JavaScript** untuk tampilan yang menarik serta **algoritma Minimax** untuk AI cerdas. Game ini cocok dimainkan sendiri maupun bersama teman.

🚀 **Siap dimainkan di browser tanpa perlu instalasi tambahan!**
