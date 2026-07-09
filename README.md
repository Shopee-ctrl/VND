<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MTO Picking Processor - Mã Vạch To Cân Đối</title>
    <style>
        :root {
            --gradient-banner: linear-gradient(90deg, #1499ca 0%, #1da1f2 50%, #7834bc 100%);
            --border-ui: #e2e8f0;
        }
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: #f3f7fa;
            margin: 0;
            padding: 24px;
            color: #1e293b;
        }
        .container {
            max-width: 1280px;
            margin: 0 auto;
            background: #ffffff;
            border-radius: 16px;
            box-shadow: 0 10px 25px rgba(0,0,0,0.05);
            overflow: hidden;
            border: 1px solid var(--border-ui);
        }
        .header-top {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 18px 32px;
            background: #ffffff;
            border-bottom: 1px solid var(--border-ui);
        }
        .logo-area {
            display: flex;
            align-items: center;
            gap: 12px;
        }
        .logo-icon {
            background: #2563eb;
            color: white;
            width: 38px;
            height: 38px;
            border-radius: 10px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-weight: bold;
            font-size: 18px;
        }
        .logo-text h2 { margin: 0; font-size: 18px; color: #0f172a; font-weight: 700; }
        .logo-text span { font-size: 12px; color: #64748b; }
        .status-ready { color: #10b981; font-weight: 600; }

        .upload-section {
            padding: 24px 32px;
            background: #f8fafc;
            border-bottom: 1px solid var(--border-ui);
        }
        .paste-zone {
            width: 100%;
            height: 100px;
            border: 2px dashed #3b82f6;
            background: #fff;
            border-radius: 10px;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            color: #475569;
            cursor: text;
            font-size: 14px;
            font-weight: 500;
            text-align: center;
            box-sizing: border-box;
        }
        .paste-zone:focus { outline: none; background: #f0f7ff; border-color: #2563eb; }

        .picking-banner {
            background: var(--gradient-banner);
            color: white;
            padding: 24px 32px;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }
        .banner-left h3 { margin: 0; font-size: 24px; font-weight: 700; }
        .banner-left p { margin: 4px 0 0 0; font-size: 13px; opacity: 0.9; }
        .banner-right { display: flex; align-items: center; gap: 32px; }
        .total-box { text-align: right; }
        .total-label { font-size: 10px; font-weight: 700; text-transform: uppercase; opacity: 0.8; letter-spacing: 0.5px; }
        .total-count { font-size: 36px; font-weight: 800; line-height: 1; margin-top: 4px; }
        
        .btn-download-pdf {
            background: #ffffff;
            color: #1d4ed8;
            padding: 12px 24px;
            border-radius: 8px;
            font-weight: 700;
            font-size: 14px;
            border: none;
            cursor: pointer;
            box-shadow: 0 4px 12px rgba(0,0,0,0.1);
        }

        .table-responsive { overflow-x: auto; }
        table { width: 100%; border-collapse: collapse; text-align: left; font-size: 13.5px; }
        th { background-color: #fafbfc; color: #64748b; padding: 16px 20px; font-weight: 700; border-bottom: 2px solid #edf2f7; text-transform: uppercase; font-size: 11px; }
        td { padding: 16px 20px; border-bottom: 1px solid #edf2f7; color: #334155; }
        .qty-badge { background-color: #e0f2fe; color: #0369a1; padding: 4px 12px; border-radius: 6px; font-weight: bold; display: inline-block; }

        /* ================= KHU VỰC IN: CÂN ĐỐI MÃ VẠCH LỚN THEO MẪU ================= */
        #printArea { display: none; }

        @media print {
            @page {
                size: landscape;
                margin: 0mm;
            }
            html, body {
                margin: 0 !important;
                padding: 0 !important;
                background: #fff;
                -webkit-print-color-adjust: exact;
            }
            .container { display: none !important; }
            #printArea { 
                display: block !important; 
                width: auto;
                height: auto;
            }
            
            .print-page {
                page-break-after: always;
                page-break-inside: avoid;
                box-sizing: border-box;
                width: 100vw;
                height: 100vh;
                display: flex;
                align-items: center;
                justify-content: center;
                overflow: hidden;
            }

            .label-card {
                border: 2px solid #000;
                box-sizing: border-box;
                background: #fff;
                width: 96vw;  
                height: 94vh;
                display: flex;
                flex-direction: row;
                padding: 12px;
                overflow: hidden;
            }

            /* CỘT TRÁI - PHÂN BỔ 52% ĐỂ MÃ VẠCH SPAN RỘNG RÃI */
            .left-column {
                width: 53%;
                display: flex;
                flex-direction: column;
                justify-content: space-between;
                border-right: 2px solid #000;
                padding-right: 14px;
                box-sizing: border-box;
            }

            .left-header {
                display: flex;
                align-items: center;
                gap: 16px;
                height: 32px;
            }
            .index-num { font-size: 28pt; font-weight: 900; color: #000; font-family: Arial, sans-serif; }
            .header-mid-text { font-size: 13.5pt; font-weight: bold; font-family: Arial, sans-serif; }

            .barcode-container {
                width: 100%;
                text-align: center;
                margin: 4px 0;
            }
            /* Phóng to và dàn trải mã vạch */
            .barcode-image {
                width: 100% !important; /* Buộc SVG nhận 100% chiều rộng cột */
                height: 95px;          /* Tăng chiều cao lên cực đại giống mẫu */
                display: block;
            }
            
            .horizontal-divider {
                border-top: 2px solid #000;
                margin: 4px 0;
            }

            .bottom-left-area {
                display: flex;
                flex-direction: column;
                align-items: center;
                width: 100%;
            }
            .barcode-image.small {
                height: 65px;          /* Tăng chiều cao mã vạch phía dưới */
            }
            .print-time {
                align-self: flex-start;
                font-size: 8pt;
                color: #000;
                font-weight: bold;
                margin-top: 4px;
            }

            /* CỘT PHẢI - THÔNG TIN CHI TIẾT CHỮ */
            .right-column {
                width: 47%;
                display: flex;
                flex-direction: column;
                justify-content: space-between;
                padding-left: 14px;
                box-sizing: border-box;
            }

            .sku-name {
                font-size: 12pt;
                font-weight: bold;
                line-height: 1.3;
                color: #000;
                display: -webkit-box;
                -webkit-line-clamp: 3;
                -webkit-box-orient: vertical;
                overflow: hidden;
                height: 3.9em;
                font-family: Arial, sans-serif;
            }

            .sl-box {
                display: flex;
                align-items: baseline;
                justify-content: flex-start;
                gap: 8px;
                margin-top: -5px;
            }
            .sl-label { font-size: 36pt; font-weight: 800; color: #000; font-family: Arial, sans-serif; }
            .sl-value { font-size: 58pt; font-weight: 900; line-height: 0.8; color: #000; font-family: Arial, sans-serif; }

            .location-id {
                font-size: 18pt;
                font-weight: 800;
                font-family: monospace;
                color: #000;
                margin-top: 2px;
            }

            .sku-code-text {
                font-size: 11pt;
                font-weight: bold;
                color: #000;
                word-break: break-all;
                font-family: Arial, sans-serif;
                margin-top: auto;
            }
        }
    </style>
    <script src="https://cdn.jsdelivr.net/npm/jsbarcode@3.11.5/dist/JsBarcode.all.min.js"></script>
</head>
<body>

<div class="container">
    <div class="header-top">
        <div class="logo-area">
            <div class="logo-icon">📦</div>
            <div class="logo-text">
                <h2>MTO Picking Processor</h2>
                <span>Perfect Barcode Size Template</span>
            </div>
        </div>
        <div>Status: <span class="status-ready">● Ready</span></div>
    </div>

    <div class="upload-section">
        <div id="pasteZone" class="paste-zone" contenteditable="true">
            Bấm chuột vào đây rồi nhấn nút [Ctrl + V] để dán data copy từ Excel
        </div>
    </div>

    <div class="picking-banner">
        <div class="banner-left">
            <h3>Picking List</h3>
            <p>Ready for warehouse processing</p>
        </div>
        <div class="banner-right">
            <div class="total-box">
                <div class="total-label">Total Items</div>
                <div class="total-count" id="totalItems">0</div>
            </div>
            <button class="btn-download-pdf" onclick="window.print()">📄 In Toàn Bộ Lô Tem (Khổ Ngang Chuẩn Mẫu)</button>
        </div>
    </div>

    <div class="table-responsive">
        <table>
            <thead>
                <tr>
                    <th style="width: 50px;"># No.</th>
                    <th>Picking ID</th>
                    <th>SKU ID</th>
                    <th>SKU Name</th>
                    <th>Location ID</th>
                    <th>Quantity</th>
                    <th>Device ID</th>
                </tr>
            </thead>
            <tbody id="tableBody">
                <tr>
                    <td colspan="7" style="text-align: center; color: #94a3b8; padding: 40px; font-size: 14px;">Chưa có dữ liệu. Hãy quét chọn bảng trong Excel, sao chép rồi bấm dán vào ô phía trên.</td>
                </tr>
            </tbody>
        </table>
    </div>
</div>

<div id="printArea"></div>

<script>
const pasteZone = document.getElementById('pasteZone');
let globalItems = [];

pasteZone.addEventListener('focus', function() {
    if (this.innerText.includes("Bấm chuột vào đây")) this.innerHTML = "";
});

pasteZone.addEventListener('paste', function(e) {
    e.preventDefault();
    let rawText = (e.clipboardData || window.clipboardData).getData('text');
    if (!rawText.trim()) return;

    pasteZone.innerText = "Đã nhận dữ liệu thành công!";
    let rows = rawText.split(/\r?\n/);
    globalItems = [];
    let totalQty = 0;
    let skuCounts = {};

    rows.forEach((row) => {
        if (!row.trim()) return;
        let cols = row.split('\t');

        if (String(cols[0]).toLowerCase().includes("picking") || String(cols[1]).toLowerCase().includes("sku")) return;

        let startIdx = 0;
        if (cols[0] !== "" && !isNaN(cols[0]) && String(cols[0]).length <= 3 && cols[1] !== undefined && cols[1] !== "") {
            startIdx = 1;
        }

        let pickingId = cols[startIdx] ? String(cols[startIdx]).trim() : '';
        let skuId = cols[startIdx+1] ? String(cols[startIdx+1]).trim() : '';
        let skuName = cols[startIdx+2] ? String(cols[startIdx+2]).trim() : '';
        let locationId = cols[startIdx+3] ? String(cols[startIdx+3]).trim() : '';
        let quantity = cols[startIdx+4] ? parseInt(String(cols[startIdx+4]).replace(/[^0-9]/g, '')) || 0 : 0;
        let deviceId = cols[startIdx+5] ? String(cols[startIdx+5]).trim() : '';

        if (pickingId || skuId) {
            globalItems.push({ pickingId, skuId, skuName, locationId, quantity, deviceId });
            if (skuId) skuCounts[skuId] = (skuCounts[skuId] || 0) + 1;
        }
    });

    globalItems.sort((a, b) => {
        let countA = skuCounts[a.skuId] || 0;
        let countB = skuCounts[b.skuId] || 0;
        if (countB !== countA) return countB - countA;
        return a.skuId.localeCompare(b.skuId); 
    });

    const tbody = document.getElementById('tableBody');
    tbody.innerHTML = '';
    globalItems.forEach((item, index) => {
        totalQty += item.quantity;
        tbody.innerHTML += `
            <tr>
                <td>${index + 1}</td>
                <td>${item.pickingId}</td>
                <td style="font-weight:700;">${item.skuId}</td>
                <td>${item.skuName}</td>
                <td>${item.locationId}</td>
                <td><span class="qty-badge">${item.quantity}</span></td>
                <td>${item.deviceId}</td>
            </tr>
        `;
    });
    document.getElementById('totalItems').innerText = totalQty;

    renderLandscapeLabels();
});

function renderLandscapeLabels() {
    const printArea = document.getElementById('printArea');
    printArea.innerHTML = '';
    
    globalItems.forEach((item, index) => {
        let globalIndex = index + 1;
        
        let pageDiv = document.createElement('div');
        pageDiv.className = 'print-page';
        
        pageDiv.innerHTML = `
            <div class="label-card">
                <div class="left-column">
                    <div class="left-header">
                        <div class="index-num">${globalIndex}</div>
                        <div class="header-mid-text">MTVN2607080005 &nbsp;&nbsp; A</div>
                    </div>
                    
                    <div class="barcode-container">
                        <svg class="barcode-image" id="bc-pick-${globalIndex}"></svg>
                    </div>
                    
                    <div class="horizontal-divider"></div>
                    
                    <div class="bottom-left-area">
                        <svg class="barcode-image small" id="bc-dev-${globalIndex}"></svg>
                        <div class="print-time">Thời gian in: 2026-07-08 22:33:29</div>
                    </div>
                </div>
                
                <div class="right-column">
                    <div class="sku-name">${item.skuName}</div>
                    
                    <div class="sl-box">
                        <span class="sl-label">SL:</span>
                        <span class="sl-value">${item.quantity}</span>
                    </div>
                    
                    <div class="location-id">${item.locationId}</div>
                    
                    <div class="sku-code-text">${item.skuId}</div>
                </div>
            </div>
        `;
        
        printArea.appendChild(pageDiv);

        // Sử dụng flat: true và tăng width/height để kéo căng mã vạch chuẩn đét
        JsBarcode(`#bc-pick-${globalIndex}`, item.pickingId, {
            format: "CODE128",
            lineColor: "#000",
            width: 2.8,       
            height: 95,       /* Tăng chiều cao tối đa che phủ không gian trống */
            displayValue: true,
            fontSize: 18,
            fontOptions: "bold",
            flat: true        /* Ép mã vạch căng ngang mượt mà */
        });

        JsBarcode(`#bc-dev-${globalIndex}`, item.deviceId, {
            format: "CODE128",
            lineColor: "#000",
            width: 2.0,
            height: 65,       /* Tăng chiều cao mã vạch dưới */
            displayValue: true,
            fontSize: 14,
            fontOptions: "bold",
            flat: true
        });
    });
}
</script>
</body>
</html>
