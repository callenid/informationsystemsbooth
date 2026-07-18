<div align="center">
  # ESI Interactive Showcase

  **An interactive booth experience for the UPH Information Systems Program**  
  Faculty of Artificial Intelligence and Data Sciences — Universitas Pelita Harapan
  github page: <a href="https://callenid.github.io/informationsystemsbooth/">https://callenid.github.io/informationsystemsbooth/</a>

  <br>

  <img src="https://img.shields.io/badge/HTML5-E34F26?style=for-the-badge&logo=html5&logoColor=white" alt="HTML5">
  <img src="https://img.shields.io/badge/CSS3-1572B6?style=for-the-badge&logo=css3&logoColor=white" alt="CSS3">
  <img src="https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=111111" alt="JavaScript">
  <img src="https://img.shields.io/badge/No_Framework-4A3F8F?style=for-the-badge" alt="No Framework">
  <img src="https://img.shields.io/badge/Responsive-0FA8A3?style=for-the-badge" alt="Responsive Design">

  <br><br>

  [Features](#-features) · [Getting Started](#-getting-started) · [Game Controls](#-game-controls) · [Deployment](#-deploying-to-github-pages)
</div>

---

## 🌟 Overview

**ESI Interactive Showcase** is a single-page interactive website created for the UPH Information Systems booth. It introduces visitors to the Information Systems program through games, a quiz, program information, and the ESI penguin mascot.

The project is built entirely with native **HTML, CSS, and JavaScript**. It does not require a framework, package manager, database, or build process.

## ✨ Features

| Feature | Description |
|---|---|
| 🎮 **ESI Data Rush** | A three-level canvas game where players catch data, avoid bugs, build combos, and reach target scores. |
| ⌨️ **Typing Challenge** | A three-level typing game featuring short words, Information Systems terminology, and technology phrases. |
| 🏆 **Local Leaderboards** | Stores the five highest scores for both games directly in the browser using `localStorage`. |
| 🧠 **Information Systems Quiz** | Five interactive questions with immediate feedback and a final score. |
| 📚 **Specialization Showcase** | Introduces Data Science & Analytics, Interactive Intelligence Systems, and Digital Technology and Innovation. |
| 🐧 **ESI Mascot Experience** | Uses ESI throughout the page to create a friendly and memorable booth experience. |
| 🔊 **Sound Effects** | Lightweight game sounds generated through the Web Audio API. |
| ⛶ **Fullscreen Mode** | Both games can be played in fullscreen mode for a better booth display experience. |
| 📱 **Responsive Controls** | Supports keyboard, mouse, and touch interaction across desktop, tablet, and mobile devices. |
| ♿ **Accessibility Support** | Includes semantic labels, keyboard focus states, responsive layouts, and reduced-motion support. |

## 🖼️ Experience Flow

1. Meet **ESI**, the UPH Information Systems mascot.
2. Play **ESI Data Rush** and complete all three levels.
3. Test your speed in the **Typing Challenge**.
4. Compare scores on the booth leaderboards.
5. Complete the Information Systems quiz.
6. Explore the program's three specialization paths.
7. Visit the booth and connect with UPH lecturers and students.

## 🧰 Technology Stack

- **HTML5** for the page structure and semantic content
- **CSS3** for the responsive glassmorphism interface and animations
- **Vanilla JavaScript** for game mechanics and interactions
- **Canvas API** for ESI Data Rush and the animated background
- **Web Audio API** for lightweight sound effects
- **Fullscreen API** for immersive gameplay
- **Local Storage API** for persistent browser-based leaderboards
- **Google Fonts**: Baloo 2, Nunito Sans, and JetBrains Mono

## 📁 Project Structure

```text
esi-interactive-showcase/
├── index.html
├── README.md
└── assets/
    ├── esi.png
    ├── si-horizontal.png
    ├── uph-faidas.png
    └── uph-faidas-white.png
```

> For GitHub Pages, make sure the main English HTML file is named `index.html` and the `assets` folder remains in the same directory.

## 🚀 Getting Started

### Option 1 — Open Directly

Clone or download the repository, then open `index.html` in a modern web browser.

```bash
git clone https://github.com/YOUR-USERNAME/YOUR-REPOSITORY.git
cd YOUR-REPOSITORY
```

Then double-click `index.html`.

### Option 2 — Run a Local Server

Running a local server is recommended during development.

#### Using Python

```bash
python3 -m http.server 8000
```

Open:

```text
http://localhost:8000
```

#### Using Visual Studio Code

Install the **Live Server** extension, right-click `index.html`, and select **Open with Live Server**.

## 🎮 Game Controls

### ESI Data Rush

| Device | Controls |
|---|---|
| Keyboard | `←` / `→` or `A` / `D` |
| Mouse | Move the pointer horizontally over the game canvas |
| Touchscreen | Touch and drag ESI horizontally |

Catch beneficial data items and avoid bugs:

| Item | Effect |
|---|---|
| 💾 📊 🧩 💡 🌐 | Base score: `+10` |
| ⭐ | Base score: `+30` |
| 🐛 | `−15` points and resets the combo |

### ESI Typing Challenge

Type the displayed word or phrase exactly as shown. The next challenge appears automatically after a correct answer.

Remaining time becomes a bonus when a level is completed.

## 🏆 Leaderboard Behavior

- Each game stores its top five scores in the player's browser.
- Scores persist after refreshing or reopening the page.
- The leaderboard does not require a server or online database.
- Clearing browser storage will remove saved scores.
- Booth staff can double-click a leaderboard title to reset that game's scores after confirming the action.

Because the leaderboard uses `localStorage`, scores are specific to each browser and device.

## 🎓 Information Systems Specializations

The showcase introduces three specialization paths:

### 1. Data Science & Analytics

Transform data into valuable business insights through machine learning, predictive analytics, process mining, behavioral analytics, and data visualization.

### 2. Interactive Intelligence Systems

Design engaging digital experiences through advanced UI/UX, interactive applications, immersive technology, generative AI applications, and software projects.

### 3. Digital Technology and Innovation

Develop innovative digital products using IoT, mobile applications, recommendation systems, immersive technology, business strategy, and IT risk management.

## 🎨 Design System

The website uses a light glassmorphism interface inspired by UPH Information Systems and FAIDAS visual identities.

Main theme variables are available in the `:root` section of `index.html`:

```css
--indigo: #4A3F8F;
--plum: #A4468A;
--orange: #F0762B;
--cyan: #0FA8A3;
```

The interface includes:

- Gradient accents
- Frosted-glass cards
- Responsive typography
- Animated mascot elements
- An interactive node-network background
- Reduced-motion support based on device preferences

## ⚙️ Customization Guide

The project is contained in one HTML file, making it easy to customize.

| Content | Location in `index.html` |
|---|---|
| Theme colors | CSS variables inside `:root` |
| Navigation and page copy | Main HTML content |
| Data Rush levels | JavaScript `LEVELS` array |
| Typing levels and words | JavaScript `TLEVELS` array |
| Quiz questions | JavaScript `QUESTIONS` array |
| Mascot and logos | Relative paths inside the `assets` folder |
| Leaderboard storage | JavaScript storage helper and leaderboard functions |

Maintain the existing element IDs when editing because the JavaScript uses them to connect the interface with the game logic.

## 🌐 Deploying to GitHub Pages

1. Upload `index.html`, `README.md`, and the complete `assets` folder to the repository.
2. Open the repository's **Settings**.
3. Select **Pages** from the sidebar.
4. Under **Build and deployment**, choose **Deploy from a branch**.
5. Select the `main` branch and the `/ (root)` folder.
6. Click **Save**.

Your website will normally be available at:

```text
https://YOUR-USERNAME.github.io/YOUR-REPOSITORY/
```

Make sure all asset paths remain relative, for example:

```html
<img src="assets/esi.png" alt="ESI mascot">
```

## ✅ Browser Compatibility

The showcase is designed for current versions of:

- Google Chrome
- Microsoft Edge
- Mozilla Firefox
- Safari
- Mobile Chrome and Safari

Fullscreen behavior may vary slightly depending on browser and device permissions.

## 🏫 About the Program

**Information Systems Program**  
Faculty of Artificial Intelligence and Data Sciences  
Universitas Pelita Harapan

The Information Systems program connects business, people, data, and technology to prepare students for roles such as business analyst, data analyst, product manager, software engineer, UI/UX designer, IT consultant, and digital transformation leader.

## 📷 Connect

Follow the UPH Information Systems Program on Instagram:

**[@sisteminformasi.uph](https://www.instagram.com/sisteminformasi.uph/)**
**[@callencode](https://www.instagram.com/callencode/)**

## 📄 Usage Notice

This project was created as an educational and promotional showcase for the UPH Information Systems Program. Institutional names, logos, mascot artwork, and brand assets remain the property of their respective owners.

Please confirm the appropriate institutional permissions before redistributing or commercially reusing branded assets.

---

<div align="center">
  <strong>Built with data, technology, creativity, and ESI 🐧</strong>
  <br><br>
  Information Systems Program · FAIDAS · Universitas Pelita Harapan
</div>
