<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Peta Gereja Kefamenanu</title>
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
        }

        .container {
            max-width: 1200px;
            margin: 0 auto;
            padding: 20px;
        }

        .header {
            text-align: center;
            color: white;
            margin-bottom: 30px;
        }

        .header h1 {
            font-size: 2.5rem;
            margin-bottom: 10px;
            text-shadow: 2px 2px 4px rgba(0,0,0,0.3);
        }

        .header p {
            font-size: 1.1rem;
            opacity: 0.9;
        }

        .controls {
            background: rgba(255, 255, 255, 0.95);
            padding: 20px;
            border-radius: 15px;
            box-shadow: 0 8px 32px rgba(0,0,0,0.1);
            backdrop-filter: blur(10px);
            border: 1px solid rgba(255,255,255,0.2);
            margin-bottom: 20px;
        }

        .search-container {
            display: flex;
            gap: 10px;
            flex-wrap: wrap;
            align-items: center;
            margin-bottom: 15px;
        }

        .search-input {
            flex: 1;
            min-width: 200px;
            padding: 12px 15px;
            border: 2px solid #e0e0e0;
            border-radius: 25px;
            font-size: 16px;
            transition: all 0.3s ease;
        }

        .search-input:focus {
            outline: none;
            border-color: #667eea;
            box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
        }

        .btn {
            padding: 12px 24px;
            border: none;
            border-radius: 25px;
            cursor: pointer;
            font-size: 16px;
            font-weight: 600;
            transition: all 0.3s ease;
            text-transform: uppercase;
            letter-spacing: 0.5px;
        }

        .btn-primary {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
        }

        .btn-primary:hover {
            transform: translateY(-2px);
            box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
        }

        .btn-secondary {
            background: linear-gradient(135deg, #f093fb 0%, #f5576c 100%);
            color: white;
        }

        .btn-secondary:hover {
            transform: translateY(-2px);
            box-shadow: 0 5px 15px rgba(245, 87, 108, 0.4);
        }

        .filter-buttons {
            display: flex;
            gap: 10px;
            flex-wrap: wrap;
        }

        .map-container {
            background: rgba(255, 255, 255, 0.95);
            border-radius: 15px;
            overflow: hidden;
            box-shadow: 0 8px 32px rgba(0,0,0,0.1);
            backdrop-filter: blur(10px);
            border: 1px solid rgba(255,255,255,0.2);
        }

        #map {
            height: 600px;
            width: 100%;
        }

        .legend {
            background: rgba(255, 255, 255, 0.95);
            padding: 20px;
            border-radius: 15px;
            box-shadow: 0 8px 32px rgba(0,0,0,0.1);
            backdrop-filter: blur(10px);
            border: 1px solid rgba(255,255,255,0.2);
            margin-top: 20px;
        }

        .legend h3 {
            color: #333;
            margin-bottom: 15px;
            text-align: center;
        }

        .legend-items {
            display: flex;
            justify-content: center;
            gap: 30px;
            flex-wrap: wrap;
        }

        .legend-item {
            display: flex;
            align-items: center;
            gap: 10px;
            padding: 10px 15px;
            background: rgba(255, 255, 255, 0.8);
            border-radius: 10px;
            box-shadow: 0 2px 8px rgba(0,0,0,0.1);
        }

        .legend-icon {
            width: 24px;
            height: 24px;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 14px;
            box-shadow: 0 2px 5px rgba(0,0,0,0.2);
        }

        .legend-icon.catholic {
            background: white;
            border: 2px solid #8B4513;
            color: #8B4513;
        }

        .legend-icon.protestant {
            background: white;
            border: 2px solid #4169E1;
            color: #4169E1;
        }

        .legend-icon.center {
            background: red;
            border: 2px solid white;
        }

        .legend-text {
            font-weight: 600;
            color: #333;
        }

        .stats {
            background: rgba(255, 255, 255, 0.95);
            padding: 20px;
            border-radius: 15px;
            box-shadow: 0 8px 32px rgba(0,0,0,0.1);
            backdrop-filter: blur(10px);
            border: 1px solid rgba(255,255,255,0.2);
            margin-top: 20px;
            text-align: center;
        }

        .stats h3 {
            color: #333;
            margin-bottom: 15px;
        }

        .stat-item {
            display: inline-block;
            margin: 0 20px;
            padding: 10px;
        }

        .stat-number {
            font-size: 2rem;
            font-weight: bold;
            color: #667eea;
        }

        .stat-label {
            font-size: 0.9rem;
            color: #666;
            text-transform: uppercase;
            letter-spacing: 1px;
        }

        /* Custom marker styles */
        .church-marker {
            background: #fff;
            border-radius: 50%;
            box-shadow: 0 2px 10px rgba(0,0,0,0.3);
        }

        .catholic-marker {
            border: 3px solid #8B4513;
        }

        .protestant-marker {
            border: 3px solid #4169E1;
        }

        /* Fitur dari Mymap.html */
        .info {
            padding: 15px 20px;
            font: 14px/18px 'Segoe UI', Arial, sans-serif;
            background: white;
            box-shadow: 0 4px 8px rgba(0,0,0,0.2);
            border-radius: 8px;
            border: 1px solid #ddd;
            min-width: 250px;
            max-width: 300px;
        }

        .info h4 {
            margin: 0 0 10px;
            color: #2c3e50;
            font-weight: 700;
            font-size: 16px;
            text-align: center;
            border-bottom: 2px solid #3498db;
            padding-bottom: 5px;
        }

        .info .place-name {
            font-weight: 900;
            color: #2980b9;
            font-size: 17px;
            margin-bottom: 5px;
        }

        .info .place-type {
            font-size: 14px;
            color: #27ae60;
            margin-bottom: 8px;
            font-weight: 500;
            background: #e8f5e8;
            padding: 3px 8px;
            border-radius: 4px;
            display: inline-block;
        }

        .info .place-details {
            font-size: 13px;
            color: #34495e;
            margin: 3px 0;
            display: flex;
            justify-content: space-between;
        }

        .info .detail-label {
            font-weight: 500;
            color: #7f8c8d;
        }

        .info .place-address {
            font-size: 12px;
            color: #7f8c8d;
            margin-top: 2px;
            font-weight: 600;
        }

        .coordinates-info {
            position: absolute;
            top: 20px;
            right: 20px;
            z-index: 1000;
            background: #3498db;
            padding: 12px 18px;
            border-radius: 8px;
            color: white;
            font-size: 12px;
            font-weight: 600;
            box-shadow: 0 4px 8px rgba(0,0,0,0.2);
        }

        .worship-popup {
            max-width: 400px;
            min-width: 300px;
        }

        .popup-content {
            padding: 15px;
        }

        .popup-header {
            text-align: center;
            margin-bottom: 15px;
        }

        .popup-title {
            font-size: 18px;
            font-weight: bold;
            color: #2c3e50;
            margin: 0 0 5px 0;
        }

        .popup-type {
            font-size: 14px;
            color: #27ae60;
            background: #e8f5e8;
            padding: 4px 12px;
            border-radius: 20px;
            display: inline-block;
        }

        .popup-image {
            width: 100%;
            height: 200px;
            object-fit: cover;
            border-radius: 8px;
            margin: 10px 0;
            border: 2px solid #ddd;
        }

        .popup-details {
            margin-top: 15px;
        }

        .detail-row {
            display: flex;
            justify-content: space-between;
            margin: 8px 0;
            padding: 5px 0;
            border-bottom: 1px solid #eee;
        }

        .detail-label {
            font-weight: 600;
            color: #7f8c8d;
        }

        .detail-value {
            color: #2c3e50;
            font-weight: 500;
        }

        .popup-address {
            background: #f8f9fa;
            padding: 10px;
            border-radius: 5px;
            margin-top: 10px;
            border-left: 4px solid #3498db;
        }

        .popup-address strong {
            color: #2c3e50;
        }

        .search-results {
            margin-top: 10px;
            max-height: 200px;
            overflow-y: auto;
            background: white;
            border-radius: 8px;
            box-shadow: 0 4px 8px rgba(0,0,0,0.2);
            display: none;
        }

        .search-result-item {
            padding: 8px 10px;
            background: #f8f9fa;
            margin: 3px 0;
            border-radius: 4px;
            cursor: pointer;
            font-size: 12px;
            transition: all 0.2s ease;
            border: 1px solid #e9ecef;
        }

        .search-result-item:hover {
            background: #e3f2fd;
            transform: translateX(5px);
        }

        /* Responsive design */
        @media (max-width: 768px) {
            .container {
                padding: 10px;
            }

            .header h1 {
                font-size: 2rem;
            }

            .search-container {
                flex-direction: column;
            }

            .search-input {
                min-width: 100%;
            }

            .filter-buttons {
                justify-content: center;
            }

            .legend-items {
                justify-content: space-around;
            }

            .legend-item {
                margin-bottom: 10px;
            }

            #map {
                height: 400px;
            }

            .coordinates-info {
                top: 10px;
                right: 10px;
                padding: 8px 12px;
                font-size: 10px;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>🏛️ Peta Gereja Kefamenanu</h1>
            <p>Temukan gereja katolik dan protestan di sekitar Kefamenanu, NTT</p>
        </div>

        <div class="controls">
            <div class="search-container">
                <input type="text" id="searchInput" class="search-input" placeholder="Cari nama gereja...">
                <button onclick="searchChurch()" class="btn btn-primary">🔍 Cari</button>
                <div class="search-results" id="searchResults"></div>
            </div>
            
            <div class="filter-buttons">
                <button onclick="filterChurches('all')" class="btn btn-primary" id="filterAll">🏛️ Semua</button>
                <button onclick="filterChurches('katolik')" class="btn btn-primary" id="filterCatholic">⛪ Katolik</button>
                <button onclick="filterChurches('protestan')" class="btn btn-primary" id="filterProtestant">✝️ Protestan</button>
            </div>
        </div>
        
        <div class="map-container">
            <div id="map"></div>
        </div>

        <div class="legend">
            <h3>📍 Legenda</h3>
            <div class="legend-items">
                <div class="legend-item">
                    <div class="legend-icon catholic">⛪</div>
                    <span class="legend-text">Gereja Katolik</span>
                </div>
                <div class="legend-item">
                    <div class="legend-icon protestant">✝️</div>
                    <span class="legend-text">Gereja Protestan</span>
                </div>
                <div class="legend-item">
                    <div class="legend-icon center"></div>
                    <span class="legend-text">Pusat Kefamenanu</span>
                </div>
            </div>
        </div>

        <div class="stats">
            <h3>📊 Statistik Gereja</h3>
            <div class="stat-item">
                <div class="stat-number" id="totalChurches">6</div>
                <div class="stat-label">Total Gereja</div>
            </div>
            <div class="stat-item">
                <div class="stat-number" id="catholicCount">3</div>
                <div class="stat-label">Gereja Katolik</div>
            </div>
            <div class="stat-item">
                <div class="stat-number" id="protestantCount">3</div>
                <div class="stat-label">Gereja Protestan</div>
            </div>
        </div>
    </div>

    <div class="coordinates-info">
        📍 Koordinat: -9.472152, 124.483611
    </div>

    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
    <script>
        // Koordinat pusat Kefamenanu
        const CENTER_COORDS = [-9.472152, 124.483611];
        
        // Data gereja dengan koordinat yang diberikan
        const churches = [
            {
                name: "Gereja Katedral Santo Yosep",
                type: "Gereja Katolik",
                coords: [-9.3415, 124.4735],
                capacity: 1200,
                established: 1967,
                address: "Jl. Basuki Rahmat, Kefamenanu",
                category: "catholic",
                image: "https://via.placeholder.com/400x300/8B4513/FFFFFF?text=Katedral+Santo+Yosep",
                description: "Gereja Katedral terbesar di Kefamenanu yang menjadi pusat kegiatan umat Katolik di Timor Tengah Utara.",
                priest: "Pastor Antonius Benu",
                services: "Misa Minggu: 06.00, 08.00, 17.00 WITA",
                phone: "(0380) 21234"
            },
            {
                name: "GMIT Maranatha",
                type: "Gereja Protestan", 
                coords: [-9.3395, 124.4775],
                capacity: 600,
                established: 1970,
                address: "Jl. Sukarno Hatta, Kefamenanu",
                category: "protestant",
                image: "https://via.placeholder.com/400x300/4169E1/FFFFFF?text=GMIT+Maranatha",
                description: "Gereja Masehi Injili di Timor yang melayani jemaat Protestan di wilayah Kefamenanu.",
                pastor: "Pdt. Yohanis Manehat",
                services: "Kebaktian Minggu: 08.00, 17.00 WITA",
                phone: "(0380) 21567"
            },
            {
                name: "Gereja Katolik Santa Maria",
                type: "Gereja Katolik",
                coords: [-9.3450, 124.4810],
                capacity: 200,
                established: 1980,
                address: "Kelurahan Maubani",
                category: "catholic",
                image: "https://via.placeholder.com/400x300/8B4513/FFFFFF?text=Gereja+Santa+Maria",
                description: "Gereja Katolik dengan arsitektur tradisional Timor",
                priest: "Pastor Markus Seran",
                services: "Misa Minggu: 07.00 WITA",
                phone: "(0380) 21789"
            },
            {
                name: "Gereja Protestan Bethel",
                type: "Gereja Protestan",
                coords: [-9.3395, 124.4635],
                capacity: 350,
                established: 1990,
                address: "Jl. Veteran, Kefamenanu",
                category: "protestant",
                image: "https://via.placeholder.com/400x300/4169E1/FFFFFF?text=Gereja+Bethel",
                description: "Gereja Protestan yang melayani komunitas lokal",
                pastor: "Pdt. David Talan",
                services: "Kebaktian Minggu: 08.30 WITA, Pemuda: Sabtu 19.00 WITA",
                phone: "(0380) 21890"
            },
            {
                name: "Gereja Katolik Hati Kudus",
                type: "Gereja Katolik",
                coords: [-9.3475, 124.4655],
                capacity: 400,
                established: 1985,
                address: "Jl. Diponegoro, Kefamenanu",
                category: "catholic",
                image: "https://via.placeholder.com/400x300/8B4513/FFFFFF?text=Gereja+Hati+Kudus",
                description: "Pusat kegiatan umat Katolik di wilayah tengah Kefamenanu",
                priest: "Pastor Lukas Berek",
                services: "Misa Minggu: 09.00 WITA",
                phone: "(0380) 21901"
            },
            {
                name: "Gereja Protestan GPIB Efrata",
                type: "Gereja Protestan",
                coords: [-9.3435, 124.4695],
                capacity: 500,
                established: 1975,
                address: "Jl. Ahmad Yani, Kefamenanu",
                category: "protestant",
                image: "https://via.placeholder.com/400x300/4169E1/FFFFFF?text=GPIB+Efrata",
                description: "Gereja GPIB dengan program pelayanan masyarakat yang aktif",
                pastor: "Pdt. Samuel Naif",
                services: "Kebaktian Minggu: 10.00 WITA",
                phone: "(0380) 21456"
            }
        ];

        // Inisialisasi peta
        const map = L.map('map').setView(CENTER_COORDS, 13);

        // Tambahkan tile layer
        L.tileLayer('https://server.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer/tile/{z}/{y}/{x}', {
            maxZoom: 19,
            attribution: '© Esri &mdash; Source: Esri, i-cubed, USDA, USGS, AEX, GeoEye, Getmapping, Aerogrid, IGN, IGP, UPR-EGP, and the GIS User Community'
        }).addTo(map);

        L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
            maxZoom: 19,
            opacity: 0.7,
            attribution: '© <a href="http://www.openstreetmap.org/copyright">OpenStreetMap</a>'
        }).addTo(map);

        // Fungsi untuk mendapatkan warna berdasarkan kategori
        function getColor(category) {
            switch(category) {
                case 'catholic': return '#8B4513';
                case 'protestant': return '#4169E1';
                default: return '#808080';
            }
        }

        // Fungsi untuk membuat popup detail
        function createDetailedPopup(properties) {
            var leaderTitle = '';
            var leaderName = '';
            
            if (properties.priest) {
                leaderTitle = 'Pastor';
                leaderName = properties.priest;
            } else if (properties.pastor) {
                leaderTitle = 'Pendeta';
                leaderName = properties.pastor;
            }

            return `
                <div class="worship-popup">
                    <div class="popup-content">
                        <div class="popup-header">
                            <h3 class="popup-title">${properties.name}</h3>
                            <span class="popup-type">${properties.type}</span>
                        </div>
                        
                        <img src="${properties.image}" alt="${properties.name}" class="popup-image" 
                             onerror="this.src='https://via.placeholder.com/400x300/666666/FFFFFF?text=Gambar+Tidak+Tersedia'">
                        
                        <p style="font-style: italic; color: #666; margin: 10px 0;">${properties.description}</p>
                        
                        <div class="popup-details">
                            <div class="detail-row">
                                <span class="detail-label">Kapasitas:</span>
                                <span class="detail-value">${properties.capacity} orang</span>
                            </div>
                            <div class="detail-row">
                                <span class="detail-label">Didirikan:</span>
                                <span class="detail-value">${properties.established}</span>
                            </div>
                            ${leaderName ? `
                            <div class="detail-row">
                                <span class="detail-label">${leaderTitle}:</span>
                                <span class="detail-value">${leaderName}</span>
                            </div>
                            ` : ''}
                            <div class="detail-row">
                                <span class="detail-label">Telepon:</span>
                                <span class="detail-value">${properties.phone}</span>
                            </div>
                        </div>
                        
                        <div class="popup-address">
                            <strong>📍 Alamat:</strong><br>
                            ${properties.address}
                        </div>
                        
                        <div style="margin-top: 10px; padding: 8px; background: #f0f8ff; border-radius: 4px; font-size: 12px;">
                            <strong>🕐 Jadwal Ibadah:</strong><br>
                            ${properties.services}
                        </div>
                    </div>
                </div>
            `;
        }

        // Simpan semua marker
        let churchMarkers = [];
        let currentFilter = 'all';

        // Tambahkan marker gereja ke peta
        function addChurchMarkers() {
            // Clear existing markers
            churchMarkers.forEach(marker => map.removeLayer(marker));
            churchMarkers = [];

            churches.forEach(church => {
                const iconHtml = `<div style="
                    background: ${getColor(church.category)};
                    width: 30px;
                    height: 30px;
                    border-radius: 50%;
                    border: 3px solid white;
                    box-shadow: 0 4px 8px rgba(0,0,0,0.3);
                    display: flex;
                    align-items: center;
                    justify-content: center;
                    color: white;
                    font-size: 16px;
                    font-weight: bold;
                ">${church.type.includes('Katolik') ? '⛪' : '✝️'}</div>`;
                
                const customIcon = L.divIcon({
                    className: 'custom-worship-marker',
                    html: iconHtml,
                    iconSize: [30, 30],
                    iconAnchor: [15, 15],
                    popupAnchor: [0, -15]
                });

                // Show/hide based on current filter
                if (currentFilter === 'all' || 
                    (currentFilter === 'katolik' && church.category === 'catholic') || 
                    (currentFilter === 'protestan' && church.category === 'protestant')) {
                    
                    const marker = L.marker(church.coords, {
                        icon: customIcon,
                        title: church.name
                    }).addTo(map);
                    
                    marker.bindPopup(createDetailedPopup(church), {
                        maxWidth: 400,
                        className: 'worship-popup'
                    });
                    
                    churchMarkers.push(marker);
                }
            });

            updateStats();
        }

        // Filter gereja berdasarkan tipe
        function filterChurches(type) {
            currentFilter = type;
            addChurchMarkers();
            
            // Update button states
            document.querySelectorAll('.filter-buttons .btn').forEach(btn => {
                btn.style.opacity = '0.7';
            });
            
            if (type === 'all') {
                document.getElementById('filterAll').style.opacity = '1';
            } else if (type === 'katolik') {
                document.getElementById('filterCatholic').style.opacity = '1';
            } else if (type === 'protestan') {
                document.getElementById('filterProtestant').style.opacity = '1';
            }
        }

        // Pencarian gereja
        function searchChurch() {
            const searchTerm = document.getElementById('searchInput').value.toLowerCase();
            const searchResults = document.getElementById('searchResults');
            
            if (!searchTerm) {
                searchResults.style.display = 'none';
                return;
            }

            const results = churches.filter(church => 
                church.name.toLowerCase().includes(searchTerm) || 
                church.type.toLowerCase().includes(searchTerm)
            );

            displaySearchResults(results);
        }

        // Menampilkan hasil pencarian
        function displaySearchResults(results) {
            const searchResults = document.getElementById('searchResults');
            searchResults.innerHTML = '';
            
            if (results.length === 0) {
                const noResult = document.createElement('div');
                noResult.className = 'search-result-item';
                noResult.textContent = 'Tidak ditemukan hasil pencarian';
                searchResults.appendChild(noResult);
            } else {
                results.forEach(church => {
                    const div = document.createElement('div');
                    div.className = 'search-result-item';
                    div.innerHTML = `<strong>${church.name}</strong><br><small>${church.type}</small>`;
                    div.onclick = function() {
                        map.setView(church.coords, 17);
                        
                        setTimeout(() => {
                            const marker = churchMarkers.find(m => 
                                m.getLatLng().lat === church.coords[0] && 
                                m.getLatLng().lng === church.coords[1]
                            );
                            if (marker) {
                                marker.openPopup();
                            }
                        }, 500);
                        
                        searchResults.style.display = 'none';
                    };
                    searchResults.appendChild(div);
                });
            }
            
            searchResults.style.display = results.length > 0 ? 'block' : 'none';
        }

        // Update statistik
        function updateStats() {
            const visibleChurches = churches.filter(church => 
                currentFilter === 'all' || 
                (currentFilter === 'katolik' && church.category === 'catholic') || 
                (currentFilter === 'protestan' && church.category === 'protestant')
            );
            
            const catholics = visibleChurches.filter(church => church.category === 'catholic').length;
            const protestants = visibleChurches.filter(church => church.category === 'protestant').length;

            document.getElementById('totalChurches').textContent = visibleChurches.length;
            document.getElementById('catholicCount').textContent = catholics;
            document.getElementById('protestantCount').textContent = protestants;
        }

        // Event listener untuk input pencarian
        document.getElementById('searchInput').addEventListener('input', function() {
            const searchTerm = this.value.toLowerCase();
            if (searchTerm.length > 0) {
                const results = churches.filter(church => 
                    church.name.toLowerCase().includes(searchTerm) || 
                    church.type.toLowerCase().includes(searchTerm)
                );
                displaySearchResults(results);
            } else {
                document.getElementById('searchResults').style.display = 'none';
            }
        });

        // Event listener untuk Enter key pada search input
        document.getElementById('searchInput').addEventListener('keypress', function(e) {
            if (e.key === 'Enter') {
                searchChurch();
            }
        });

        // Sembunyikan hasil pencarian saat klik di luar
        document.addEventListener('click', function(e) {
            if (!e.target.closest('.search-container')) {
                document.getElementById('searchResults').style.display = 'none';
            }
        });

        // Marker untuk titik pusat
        const centerIcon = L.divIcon({
            className: 'center-marker',
            html: '<div style="background: #e74c3c; width: 20px; height: 20px; border-radius: 50%; border: 3px solid white; box-shadow: 0 2px 10px rgba(0,0,0,0.3);"></div>',
            iconSize: [20, 20],
            iconAnchor: [10, 10]
        });

        const centerMarker = L.marker(CENTER_COORDS, {
            icon: centerIcon,
            title: 'Pusat Kota Kefamenanu'
        }).addTo(map);

        centerMarker.bindPopup('<b>🏛️ Pusat Kota Kefamenanu</b><br>Koordinat: -9.472152, 124.483611<br><small>Kabupaten Timor Tengah Utara, NTT</small>');

        centerMarker.on('click', function() {
            map.setView(CENTER_COORDS, 13);
        });

        // Tambahkan skala
        L.control.scale({
            position: 'bottomleft',
            metric: true,
            imperial: false
        }).addTo(map);

        // Inisialisasi
        addChurchMarkers();
        updateStats();

        console.log('🏛️ Peta Gereja Kefamenanu telah dimuat!');
        console.log(`📊 Total ${churches.length} gereja telah ditambahkan ke peta`);
    </script>
</body>
</html>
