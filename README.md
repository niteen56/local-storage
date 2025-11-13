<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>3D PDF Storage Manager</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        :root {
            --primary: #4361ee;
            --secondary: #3a0ca3;
            --accent: #f72585;
            --light: #f8f9fa;
            --dark: #212529;
            --gray: #6c757d;
            --success: #4cc9f0;
            --shadow: 0 10px 20px rgba(0,0,0,0.1);
            --transition: all 0.3s ease;
        }

        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        body {
            background: linear-gradient(135deg, #f5f7fa 0%, #c3cfe2 100%);
            color: var(--dark);
            min-height: 100vh;
            padding: 20px;
        }

        .container {
            max-width: 1400px;
            margin: 0 auto;
        }

        header {
            text-align: center;
            margin-bottom: 30px;
            padding: 30px;
            background: rgba(255, 255, 255, 0.9);
            border-radius: 20px;
            box-shadow: var(--shadow);
            position: relative;
            overflow: hidden;
        }

        header::before {
            content: '';
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 5px;
            background: linear-gradient(90deg, var(--primary), var(--accent), var(--success));
        }

        h1 {
            color: var(--primary);
            margin-bottom: 10px;
            font-size: 2.8rem;
            font-weight: 800;
        }

        .subtitle {
            color: var(--gray);
            font-size: 1.2rem;
            max-width: 600px;
            margin: 0 auto;
        }

        .search-section {
            display: flex;
            gap: 15px;
            margin-bottom: 30px;
            flex-wrap: wrap;
        }

        .search-box {
            flex: 1;
            min-width: 300px;
            position: relative;
        }

        .search-box input {
            width: 100%;
            padding: 15px 20px 15px 50px;
            border: none;
            border-radius: 50px;
            background: white;
            box-shadow: var(--shadow);
            font-size: 1rem;
            transition: var(--transition);
        }

        .search-box input:focus {
            outline: none;
            box-shadow: 0 5px 15px rgba(67, 97, 238, 0.3);
        }

        .search-box i {
            position: absolute;
            left: 20px;
            top: 50%;
            transform: translateY(-50%);
            color: var(--gray);
        }

        .upload-btn {
            background: var(--primary);
            color: white;
            border: none;
            padding: 15px 30px;
            border-radius: 50px;
            cursor: pointer;
            font-size: 1rem;
            font-weight: 600;
            transition: var(--transition);
            display: flex;
            align-items: center;
            gap: 10px;
            box-shadow: var(--shadow);
            white-space: nowrap;
        }

        .upload-btn:hover {
            background: var(--secondary);
            transform: translateY(-3px);
            box-shadow: 0 15px 25px rgba(67, 97, 238, 0.4);
        }

        .dashboard {
            display: grid;
            grid-template-columns: 1fr 1fr 1fr;
            gap: 20px;
            margin-bottom: 30px;
        }

        @media (max-width: 768px) {
            .dashboard {
                grid-template-columns: 1fr;
            }
        }

        .stat-card {
            background: white;
            padding: 25px;
            border-radius: 15px;
            box-shadow: var(--shadow);
            display: flex;
            align-items: center;
            gap: 20px;
        }

        .stat-icon {
            width: 60px;
            height: 60px;
            border-radius: 15px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 24px;
        }

        .stat-1 { background: rgba(67, 97, 238, 0.1); color: var(--primary); }
        .stat-2 { background: rgba(247, 37, 133, 0.1); color: var(--accent); }
        .stat-3 { background: rgba(76, 201, 240, 0.1); color: var(--success); }

        .stat-info h3 {
            font-size: 1.8rem;
            margin-bottom: 5px;
        }

        .stat-info p {
            color: var(--gray);
        }

        .content-area {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 30px;
            margin-bottom: 30px;
        }

        @media (max-width: 992px) {
            .content-area {
                grid-template-columns: 1fr;
            }
        }

        .pdf-list-container, .pdf-preview-container {
            background: white;
            border-radius: 20px;
            box-shadow: var(--shadow);
            overflow: hidden;
        }

        .section-header {
            padding: 20px 25px;
            border-bottom: 1px solid #eee;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .section-header h2 {
            color: var(--primary);
            font-size: 1.5rem;
        }

        .view-toggle {
            display: flex;
            background: #f1f3f9;
            border-radius: 10px;
            padding: 5px;
        }

        .view-btn {
            padding: 8px 15px;
            border: none;
            background: transparent;
            border-radius: 8px;
            cursor: pointer;
            transition: var(--transition);
            color: var(--gray);
        }

        .view-btn.active {
            background: var(--primary);
            color: white;
        }

        .pdf-list {
            padding: 20px;
            max-height: 500px;
            overflow-y: auto;
        }

        .pdf-item {
            display: flex;
            align-items: center;
            padding: 15px;
            border-radius: 10px;
            margin-bottom: 10px;
            transition: var(--transition);
            cursor: pointer;
        }

        .pdf-item:hover {
            background: #f8f9fa;
            transform: translateX(5px);
        }

        .pdf-item.active {
            background: rgba(67, 97, 238, 0.1);
            border-left: 4px solid var(--primary);
        }

        .pdf-icon {
            width: 40px;
            height: 40px;
            background: rgba(67, 97, 238, 0.1);
            border-radius: 8px;
            display: flex;
            align-items: center;
            justify-content: center;
            color: var(--primary);
            margin-right: 15px;
        }

        .pdf-info {
            flex: 1;
        }

        .pdf-name {
            font-weight: 600;
            margin-bottom: 5px;
        }

        .pdf-meta {
            font-size: 0.85rem;
            color: var(--gray);
        }

        .pdf-actions {
            display: flex;
            gap: 10px;
        }

        .action-btn {
            width: 36px;
            height: 36px;
            border-radius: 50%;
            border: none;
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            transition: var(--transition);
            background: #f1f3f9;
            color: var(--dark);
        }

        .action-btn:hover {
            transform: scale(1.1);
        }

        .download-btn:hover {
            background: var(--success);
            color: white;
        }

        .delete-btn:hover {
            background: var(--accent);
            color: white;
        }

        .pdf-preview {
            padding: 25px;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            height: 100%;
            min-height: 500px;
        }

        .pdf-viewer {
            width: 100%;
            height: 400px;
            background: #f8f9fa;
            border-radius: 10px;
            display: flex;
            align-items: center;
            justify-content: center;
            margin-bottom: 20px;
            position: relative;
            overflow: hidden;
            box-shadow: inset 0 0 10px rgba(0,0,0,0.1);
        }

        .pdf-3d {
            width: 200px;
            height: 280px;
            position: relative;
            transform-style: preserve-3d;
            transform: rotateX(15deg) rotateY(25deg);
            transition: transform 0.5s ease;
        }

        .pdf-3d:hover {
            transform: rotateX(15deg) rotateY(5deg);
        }

        .pdf-page {
            position: absolute;
            width: 100%;
            height: 100%;
            background: white;
            border-radius: 5px;
            box-shadow: 0 5px 15px rgba(0,0,0,0.1);
            display: flex;
            flex-direction: column;
            padding: 20px;
            backface-visibility: hidden;
        }

        .page-1 { transform: translateZ(1px); }
        .page-2 { transform: translateZ(2px); background: #f8f9fa; }
        .page-3 { transform: translateZ(3px); background: #f1f3f9; }

        .pdf-content {
            flex: 1;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            text-align: center;
        }

        .pdf-content i {
            font-size: 48px;
            color: var(--primary);
            margin-bottom: 15px;
        }

        .pdf-content h3 {
            margin-bottom: 10px;
            color: var(--dark);
        }

        .pdf-content p {
            color: var(--gray);
            font-size: 0.9rem;
        }

        .pdf-controls {
            display: flex;
            gap: 15px;
            width: 100%;
        }

        .control-btn {
            flex: 1;
            padding: 12px;
            border: none;
            border-radius: 10px;
            background: var(--primary);
            color: white;
            font-weight: 600;
            cursor: pointer;
            transition: var(--transition);
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 8px;
        }

        .control-btn:hover {
            transform: translateY(-3px);
            box-shadow: 0 5px 15px rgba(67, 97, 238, 0.4);
        }

        .control-btn.secondary {
            background: #f1f3f9;
            color: var(--dark);
        }

        .control-btn.secondary:hover {
            background: #e2e6ea;
        }

        .empty-state {
            text-align: center;
            padding: 40px 20px;
            color: var(--gray);
        }

        .empty-state i {
            font-size: 64px;
            margin-bottom: 20px;
            color: #dee2e6;
        }

        footer {
            text-align: center;
            padding: 20px;
            color: var(--gray);
            font-size: 0.9rem;
        }

        /* Animation for new items */
        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(20px); }
            to { opacity: 1; transform: translateY(0); }
        }

        .fade-in {
            animation: fadeIn 0.5s ease;
        }

        /* Scrollbar styling */
        ::-webkit-scrollbar {
            width: 8px;
        }

        ::-webkit-scrollbar-track {
            background: #f1f1f1;
            border-radius: 10px;
        }

        ::-webkit-scrollbar-thumb {
            background: var(--primary);
            border-radius: 10px;
        }

        ::-webkit-scrollbar-thumb:hover {
            background: var(--secondary);
        }
    </style>
</head>
<body>
    <div class="container">
        <header>
            <h1>3D PDF Storage Manager</h1>
            <p class="subtitle">Store, organize, and preview your PDF files with an immersive 3D experience</p>
        </header>

        <div class="search-section">
            <div class="search-box">
                <i class="fas fa-search"></i>
                <input type="text" id="searchInput" placeholder="Search PDF files...">
            </div>
            <button class="upload-btn" id="uploadBtn">
                <i class="fas fa-cloud-upload-alt"></i> Upload PDF
            </button>
            <input type="file" id="fileInput" accept=".pdf" multiple style="display: none;">
        </div>

        <div class="dashboard">
            <div class="stat-card">
                <div class="stat-icon stat-1">
                    <i class="fas fa-file-pdf"></i>
                </div>
                <div class="stat-info">
                    <h3>24</h3>
                    <p>PDF Files</p>
                </div>
            </div>
            <div class="stat-card">
                <div class="stat-icon stat-2">
                    <i class="fas fa-database"></i>
                </div>
                <div class="stat-info">
                    <h3>156 MB</h3>
                    <p>Storage Used</p>
                </div>
            </div>
            <div class="stat-card">
                <div class="stat-icon stat-3">
                    <i class="fas fa-infinity"></i>
                </div>
                <div class="stat-info">
                    <h3>Unlimited</h3>
                    <p>Total Storage</p>
                </div>
            </div>
        </div>

        <div class="content-area">
            <div class="pdf-list-container">
                <div class="section-header">
                    <h2>Your PDF Files</h2>
                    <div class="view-toggle">
                        <button class="view-btn active"><i class="fas fa-th-list"></i></button>
                        <button class="view-btn"><i class="fas fa-th-large"></i></button>
                    </div>
                </div>
                <div class="pdf-list" id="pdfList">
                    <!-- PDF items will be dynamically added here -->
                </div>
            </div>

            <div class="pdf-preview-container">
                <div class="section-header">
                    <h2>PDF Preview</h2>
                    <div class="pdf-actions">
                        <button class="action-btn download-btn" id="downloadCurrent">
                            <i class="fas fa-download"></i>
                        </button>
                        <button class="action-btn delete-btn" id="deleteCurrent">
                            <i class="fas fa-trash"></i>
                        </button>
                    </div>
                </div>
                <div class="pdf-preview" id="pdfPreview">
                    <div class="pdf-viewer">
                        <div class="pdf-3d" id="pdf3d">
                            <div class="pdf-page page-1">
                                <div class="pdf-content">
                                    <i class="fas fa-file-pdf"></i>
                                    <h3>PDF Document</h3>
                                    <p>Select a PDF to preview</p>
                                </div>
                            </div>
                            <div class="pdf-page page-2"></div>
                            <div class="pdf-page page-3"></div>
                        </div>
                    </div>
                    <div class="pdf-controls">
                        <button class="control-btn" id="viewBtn">
                            <i class="fas fa-eye"></i> View Fullscreen
                        </button>
                        <button class="control-btn secondary" id="shareBtn">
                            <i class="fas fa-share-alt"></i> Share
                        </button>
                    </div>
                </div>
            </div>
        </div>

        <footer>
            <p>© 2023 3D PDF Storage Manager | Unlimited Storage | All Rights Reserved</p>
        </footer>
    </div>

    <script>
        // Sample PDF data
        let pdfFiles = [
            { id: 1, name: "Project Proposal.pdf", size: "2.4 MB", date: "2023-05-15", category: "Work" },
            { id: 2, name: "Financial Report Q2.pdf", size: "3.1 MB", date: "2023-06-22", category: "Finance" },
            { id: 3, name: "User Manual.pdf", size: "5.7 MB", date: "2023-04-10", category: "Documentation" },
            { id: 4, name: "Research Paper.pdf", size: "1.8 MB", date: "2023-07-05", category: "Academic" },
            { id: 5, name: "Business Plan.pdf", size: "4.2 MB", date: "2023-03-18", category: "Business" },
            { id: 6, name: "Annual Report.pdf", size: "7.3 MB", date: "2023-01-30", category: "Finance" },
            { id: 7, name: "Product Catalog.pdf", size: "3.5 MB", date: "2023-08-12", category: "Marketing" },
            { id: 8, name: "Meeting Notes.pdf", size: "0.8 MB", date: "2023-09-05", category: "Work" }
        ];

        // DOM Elements
        const pdfList = document.getElementById('pdfList');
        const pdfPreview = document.getElementById('pdfPreview');
        const pdf3d = document.getElementById('pdf3d');
        const searchInput = document.getElementById('searchInput');
        const uploadBtn = document.getElementById('uploadBtn');
        const fileInput = document.getElementById('fileInput');
        const downloadCurrent = document.getElementById('downloadCurrent');
        const deleteCurrent = document.getElementById('deleteCurrent');
        const viewBtn = document.getElementById('viewBtn');
        const shareBtn = document.getElementById('shareBtn');

        // Current selected PDF
        let currentPDF = null;

        // Initialize the app
        function init() {
            renderPDFList();
            setupEventListeners();
        }

        // Render PDF list
        function renderPDFList(filteredFiles = null) {
            const filesToRender = filteredFiles || pdfFiles;
            pdfList.innerHTML = '';

            if (filesToRender.length === 0) {
                pdfList.innerHTML = `
                    <div class="empty-state">
                        <i class="fas fa-file-pdf"></i>
                        <h3>No PDF files found</h3>
                        <p>Try uploading a PDF or adjusting your search</p>
                    </div>
                `;
                return;
            }

            filesToRender.forEach(pdf => {
                const pdfItem = document.createElement('div');
                pdfItem.className = `pdf-item ${currentPDF && currentPDF.id === pdf.id ? 'active' : ''}`;
                pdfItem.dataset.id = pdf.id;
                pdfItem.innerHTML = `
                    <div class="pdf-icon">
                        <i class="fas fa-file-pdf"></i>
                    </div>
                    <div class="pdf-info">
                        <div class="pdf-name">${pdf.name}</div>
                        <div class="pdf-meta">${pdf.size} • ${pdf.date} • ${pdf.category}</div>
                    </div>
                    <div class="pdf-actions">
                        <button class="action-btn download-btn" data-id="${pdf.id}">
                            <i class="fas fa-download"></i>
                        </button>
                        <button class="action-btn delete-btn" data-id="${pdf.id}">
                            <i class="fas fa-trash"></i>
                        </button>
                    </div>
                `;
                pdfList.appendChild(pdfItem);
            });

            // Add animation to new items
            const items = pdfList.querySelectorAll('.pdf-item');
            items.forEach(item => {
                item.classList.add('fade-in');
            });
        }

        // Render 3D PDF preview
        function renderPDFPreview(pdf) {
            if (!pdf) {
                pdf3d.innerHTML = `
                    <div class="pdf-page page-1">
                        <div class="pdf-content">
                            <i class="fas fa-file-pdf"></i>
                            <h3>PDF Document</h3>
                            <p>Select a PDF to preview</p>
                        </div>
                    </div>
                    <div class="pdf-page page-2"></div>
                    <div class="pdf-page page-3"></div>
                `;
                return;
            }

            pdf3d.innerHTML = `
                <div class="pdf-page page-1">
                    <div class="pdf-content">
                        <i class="fas fa-file-pdf"></i>
                        <h3>${pdf.name}</h3>
                        <p>${pdf.size} • ${pdf.date}</p>
                        <p style="margin-top: 10px;">${pdf.category}</p>
                    </div>
                </div>
                <div class="pdf-page page-2">
                    <div class="pdf-content">
                        <h4>Document Preview</h4>
                        <p>This is a 3D preview of your PDF document.</p>
                        <p>In a real application, this would show actual PDF content.</p>
                    </div>
                </div>
                <div class="pdf-page page-3">
                    <div class="pdf-content">
                        <h4>Document Information</h4>
                        <p><strong>Size:</strong> ${pdf.size}</p>
                        <p><strong>Uploaded:</strong> ${pdf.date}</p>
                        <p><strong>Category:</strong> ${pdf.category}</p>
                    </div>
                </div>
            `;
        }

        // Set up event listeners
        function setupEventListeners() {
            // Upload button click
            uploadBtn.addEventListener('click', () => {
                fileInput.click();
            });

            // File input change
            fileInput.addEventListener('change', handleFileUpload);

            // Search input
            searchInput.addEventListener('input', handleSearch);

            // PDF list click (event delegation)
            pdfList.addEventListener('click', (e) => {
                const pdfItem = e.target.closest('.pdf-item');
                const downloadBtn = e.target.closest('.download-btn');
                const deleteBtn = e.target.closest('.delete-btn');

                if (pdfItem) {
                    const id = parseInt(pdfItem.dataset.id);
                    selectPDF(id);
                }

                if (downloadBtn) {
                    const id = parseInt(downloadBtn.dataset.id);
                    downloadPDF(id);
                    e.stopPropagation();
                }

                if (deleteBtn) {
                    const id = parseInt(deleteBtn.dataset.id);
                    deletePDF(id);
                    e.stopPropagation();
                }
            });

            // Download current PDF
            downloadCurrent.addEventListener('click', () => {
                if (currentPDF) {
                    downloadPDF(currentPDF.id);
                }
            });

            // Delete current PDF
            deleteCurrent.addEventListener('click', () => {
                if (currentPDF) {
                    deletePDF(currentPDF.id);
                }
            });

            // View fullscreen
            viewBtn.addEventListener('click', () => {
                if (currentPDF) {
                    alert(`Opening "${currentPDF.name}" in fullscreen viewer...\n\nIn a real application, this would open the PDF in a fullscreen viewer.`);
                } else {
                    alert('Please select a PDF first.');
                }
            });

            // Share PDF
            shareBtn.addEventListener('click', () => {
                if (currentPDF) {
                    alert(`Sharing "${currentPDF.name}"...\n\nIn a real application, this would open sharing options.`);
                } else {
                    alert('Please select a PDF first.');
                }
            });
        }

        // Handle file upload
        function handleFileUpload(e) {
            const files = e.target.files;
            if (files.length === 0) return;

            // In a real app, you would upload files to a server
            // For this demo, we'll just add them to our array
            for (let i = 0; i < files.length; i++) {
                const file = files[i];
                if (file.type === 'application/pdf') {
                    const newPDF = {
                        id: pdfFiles.length + 1,
                        name: file.name,
                        size: (file.size / (1024 * 1024)).toFixed(1) + ' MB',
                        date: new Date().toISOString().split('T')[0],
                        category: 'Uncategorized'
                    };
                    pdfFiles.unshift(newPDF); // Add to beginning
                }
            }

            // Update the UI
            renderPDFList();
            selectPDF(pdfFiles[0].id);

            // Reset the file input
            fileInput.value = '';

            // Show success message
            alert(`Successfully added ${files.length} PDF file(s)`);
        }

        // Handle search
        function handleSearch() {
            const query = searchInput.value.toLowerCase();
            if (query === '') {
                renderPDFList();
                return;
            }

            const filteredFiles = pdfFiles.filter(pdf => 
                pdf.name.toLowerCase().includes(query) || 
                pdf.category.toLowerCase().includes(query)
            );

            renderPDFList(filteredFiles);
        }

        // Select PDF
        function selectPDF(id) {
            currentPDF = pdfFiles.find(pdf => pdf.id === id);
            
            // Update active state in list
            const items = pdfList.querySelectorAll('.pdf-item');
            items.forEach(item => {
                if (parseInt(item.dataset.id) === id) {
                    item.classList.add('active');
                } else {
                    item.classList.remove('active');
                }
            });

            // Update preview
            renderPDFPreview(currentPDF);
        }

        // Download PDF
        function downloadPDF(id) {
            const pdf = pdfFiles.find(p => p.id === id);
            if (!pdf) return;

            // In a real app, this would download the actual file
            // For this demo, we'll just show a message
            alert(`Downloading "${pdf.name}"...\n\nIn a real application, this would download the actual PDF file.`);
        }

        // Delete PDF
        function deletePDF(id) {
            const pdf = pdfFiles.find(p => p.id === id);
            if (!pdf) return;

            if (confirm(`Are you sure you want to delete "${pdf.name}"?`)) {
                pdfFiles = pdfFiles.filter(p => p.id !== id);
                
                // If we deleted the current PDF, clear the preview
                if (currentPDF && currentPDF.id === id) {
                    currentPDF = null;
                    renderPDFPreview(null);
                }
                
                renderPDFList();
            }
        }

        // Initialize the app when DOM is loaded
        document.addEventListener('DOMContentLoaded', init);
    </script>
</body>
</html>
