<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>PDF Storage - Made by Nitish</title>
  <style>
    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
      font-family: "Poppins", sans-serif;
    }

    body {
      background: linear-gradient(135deg, #0f172a, #1e293b, #334155);
      color: #fff;
      min-height: 100vh;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: flex-start;
      padding: 20px;
    }

    header {
      text-align: center;
      margin-bottom: 25px;
    }

    header h1 {
      font-size: 2.5rem;
      color: #00bcd4;
      text-shadow: 0 0 10px rgba(0, 188, 212, 0.6);
    }

    .upload-container {
      background: #1e293b;
      padding: 25px;
      border-radius: 15px;
      box-shadow: 0 0 15px rgba(0, 188, 212, 0.4);
      width: 100%;
      max-width: 750px;
      text-align: center;
      margin-bottom: 30px;
    }

    input[type="file"] {
      margin: 15px 0;
      padding: 10px;
      background: #334155;
      border-radius: 8px;
      color: #fff;
      cursor: pointer;
    }

    button {
      background: #00bcd4;
      border: none;
      padding: 10px 18px;
      border-radius: 8px;
      color: white;
      cursor: pointer;
      font-weight: bold;
      transition: all 0.3s ease;
      margin: 5px;
    }

    button:hover {
      background: #0097a7;
      transform: scale(1.05);
    }

    .search-bar {
      margin-top: 15px;
      margin-bottom: 10px;
    }

    .search-bar input {
      width: 90%;
      max-width: 400px;
      padding: 10px;
      border-radius: 8px;
      border: none;
      outline: none;
      font-size: 1rem;
      background: #334155;
      color: #fff;
      box-shadow: 0 0 10px rgba(0, 188, 212, 0.4);
      transition: 0.3s;
    }

    .search-bar input:focus {
      box-shadow: 0 0 15px rgba(0, 188, 212, 0.6);
    }

    .pdf-list {
      width: 100%;
      max-width: 750px;
      margin-top: 10px;
    }

    .pdf-item {
      background: #273449;
      border-radius: 10px;
      padding: 15px;
      margin: 10px 0;
      display: flex;
      flex-wrap: wrap;
      justify-content: space-between;
      align-items: center;
      transition: 0.3s;
    }

    .pdf-item:hover {
      background: #334155;
    }

    .pdf-item p {
      flex: 1;
      color: #e0e0e0;
      font-weight: 500;
    }

    .pdf-buttons {
      display: flex;
      flex-wrap: wrap;
      gap: 8px;
    }

    .tabs {
      display: flex;
      justify-content: center;
      gap: 10px;
      margin-top: 10px;
    }

    .active-tab {
      background: #0097a7;
    }

    iframe {
      width: 100%;
      height: 600px;
      border: none;
      border-radius: 10px;
      margin-top: 20px;
      background: #000;
    }

    footer {
      margin-top: 40px;
      text-align: center;
      color: #aaa;
      font-size: 0.9rem;
    }

    footer span {
      color: #00bcd4;
    }

    @media (max-width: 600px) {
      header h1 {
        font-size: 2rem;
      }
      .upload-container, .pdf-list {
        width: 95%;
      }
      iframe {
        height: 400px;
      }
      .pdf-item {
        flex-direction: column;
        align-items: flex-start;
      }
      .pdf-buttons {
        width: 100%;
        justify-content: space-between;
      }
    }
  </style>
</head>
<body>
  <header>
    <h1>📁 PDF Storage</h1>
  </header>

  <div class="upload-container">
    <h2>Upload Your PDFs</h2>
    <input type="file" id="pdfUpload" multiple accept="application/pdf" />

    <div class="tabs">
      <button id="storageTab" class="active-tab">📂 Storage</button>
      <button id="recycleTab">🗑 Recycle Bin</button>
    </div>

    <div class="search-bar">
      <input type="text" id="searchInput" placeholder="🔍 Search PDFs..." />
    </div>

    <div id="pdfList" class="pdf-list"></div>
  </div>

  <iframe id="pdfViewer" style="display:none;"></iframe>

  <footer>Made by <span>Nitish</span> 💙</footer>

  <script>
    const pdfUpload = document.getElementById("pdfUpload");
    const pdfList = document.getElementById("pdfList");
    const pdfViewer = document.getElementById("pdfViewer");
    const searchInput = document.getElementById("searchInput");
    const storageTab = document.getElementById("storageTab");
    const recycleTab = document.getElementById("recycleTab");

    let pdfFiles = JSON.parse(localStorage.getItem("pdfFiles")) || [];
    let recycleBin = JSON.parse(localStorage.getItem("recycleBin")) || [];
    let currentTab = "storage";

    function saveData() {
      localStorage.setItem("pdfFiles", JSON.stringify(pdfFiles));
      localStorage.setItem("recycleBin", JSON.stringify(recycleBin));
    }

    function renderPDFList(filter = "") {
      pdfList.innerHTML = "";
      let listToRender = currentTab === "storage" ? pdfFiles : recycleBin;

      const filtered = listToRender.filter(pdf =>
        pdf.name.toLowerCase().includes(filter.toLowerCase())
      );

      if (filtered.length === 0) {
        pdfList.innerHTML = `<p style="text-align:center;color:#aaa;">No PDF found.</p>`;
        return;
      }

      filtered.forEach((pdf, index) => {
        const div = document.createElement("div");
        div.className = "pdf-item";
        div.innerHTML = `
          <p>${pdf.name}</p>
          <div class="pdf-buttons">
            ${
              currentTab === "storage"
                ? `
                  <button onclick="viewPDF('${pdf.data}')">👁 View</button>
                  <button onclick="downloadPDF('${pdf.data}','${pdf.name}')">⬇ Download</button>
                  <button onclick="moveToRecycle(${index})">🗑 Delete</button>
                `
                : `
                  <button onclick="restorePDF(${index})">♻ Restore</button>
                  <button onclick="deleteForever(${index})">❌ Delete Forever</button>
                `
            }
          </div>
        `;
        pdfList.appendChild(div);
      });
    }

    pdfUpload.addEventListener("change", (e) => {
      const files = Array.from(e.target.files);
      files.forEach((file) => {
        const reader = new FileReader();
        reader.onload = (event) => {
          const base64Data = event.target.result;
          pdfFiles.push({ name: file.name, data: base64Data });
          saveData();
          renderPDFList(searchInput.value);
        };
        reader.readAsDataURL(file);
      });
    });

    function viewPDF(base64) {
      pdfViewer.src = base64;
      pdfViewer.style.display = "block";
      window.scrollTo({ top: document.body.scrollHeight, behavior: "smooth" });
    }

    function downloadPDF(base64, name) {
      const a = document.createElement("a");
      a.href = base64;
      a.download = name;
      a.click();
    }

    function moveToRecycle(index) {
      recycleBin.push(pdfFiles[index]);
      pdfFiles.splice(index, 1);
      saveData();
      renderPDFList(searchInput.value);
    }

    function restorePDF(index) {
      pdfFiles.push(recycleBin[index]);
      recycleBin.splice(index, 1);
      saveData();
      renderPDFList(searchInput.value);
    }

    function deleteForever(index) {
      if (confirm("Delete this PDF permanently?")) {
        recycleBin.splice(index, 1);
        saveData();
        renderPDFList(searchInput.value);
      }
    }

    storageTab.addEventListener("click", () => {
      currentTab = "storage";
      storageTab.classList.add("active-tab");
      recycleTab.classList.remove("active-tab");
      renderPDFList(searchInput.value);
    });

    recycleTab.addEventListener("click", () => {
      currentTab = "recycle";
      recycleTab.classList.add("active-tab");
      storageTab.classList.remove("active-tab");
      renderPDFList(searchInput.value);
    });

    searchInput.addEventListener("input", () => {
      renderPDFList(searchInput.value);
    });

    renderPDFList();
  </script>
</body>
</html>
