<script>
    import { onMount } from 'svelte';
    import L from 'leaflet';
    import 'leaflet/dist/leaflet.css';
    import 'leaflet.markercluster';
    import 'leaflet.markercluster/dist/MarkerCluster.css';
    import 'leaflet.markercluster/dist/MarkerCluster.Default.css';

    let map;
    let groupesLayer, initiativesLayer;
    let groupes_locaux = [];
    let initiatives_ssa = [];
    
    onMount(() => {
        map = L.map('map').setView([46.603354, 1.888334], 6); // Center of France

        L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
            attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
        }).addTo(map);

        groupesLayer = L.markerClusterGroup();
        initiativesLayer = L.markerClusterGroup();

        // Define custom icons
        const customIconGL = L.icon({
            iconUrl: 'static/images/icon_rs.png', // Update this path to your custom icon
            iconSize: [32, 32],
            iconAnchor: [16, 32],
            popupAnchor: [0, -32]
        });

        const customIconSSA = L.icon({
            iconUrl: 'static/images/icon_ssa.png', // Update this path to your custom icon
            iconSize: [58, 32],
            iconAnchor: [16, 32],
            popupAnchor: [0, -32]
        });

        // Add markers for groupes_locaux
        groupes_locaux.forEach(gl => {
            const popupContent = `<b>${gl.name}</b><br>
                <b>Type:</b> ${gl.type}<br>
                <b>Address:</b> ${gl.address}, ${gl.postcode} ${gl.city}<br>
                <b>Website:</b> <a href="${gl.website}" target="_blank">${gl.website}</a><br>
                <b>Phone:</b> ${gl.phone}<br>
                <p>${gl.summary}</p>`;
            L.marker([gl.lat, gl.lng], { icon: customIconGL }).addTo(groupesLayer)
                .bindPopup(popupContent);
        });

        // Add markers for initiatives_ssa
        initiatives_ssa.forEach(ssa => {
            const popupContent = `<b>${ssa.name}</b><br>
                <b>Type:</b> ${ssa.type}<br>
                <b>Address:</b> ${ssa.address}, ${ssa.postcode} ${ssa.city}<br>
                <b>Territoire d’expérimentation:</b> ${ssa["Territoire d’expérimentation"]}<br>
                <b>Structures porteuses:</b> ${ssa["Structures porteuses"].join(", ")}<br>
                <b>Partenaires:</b> ${ssa.Partenaires.join(", ")}<br>
                <b>Website:</b> <a href="${ssa.website}" target="_blank">${ssa.website}</a><br>
                <b>Phone:</b> ${ssa.phone}<br>
                <p>${ssa.summary}</p>
                <b>URLs:</b><ul>${ssa.URLs.map(url => `<li><a href="${url}" target="_blank">${url}</a></li>`).join("")}</ul>`;
            L.marker([ssa.lat, ssa.lng], { icon: customIconSSA }).addTo(initiativesLayer)
                .bindPopup(popupContent);
        });

        groupesLayer.addTo(map);
        initiativesLayer.addTo(map);
    });

    const toggleLayer = (layer, isChecked) => {
        if (isChecked) {
            map.addLayer(layer);
        } else {
            map.removeLayer(layer);
        }
    };

    const loadMarkers = async () => {
        const markers = await import('./markers.js');
        groupes_locaux = markers.groupes_locaux;
        initiatives_ssa = markers.initiatives_ssa;
    };

    loadMarkers();
</script>

<style>
    #map {
        height: 100vh;
        width: 100%;
    }
    .legend {
        position: absolute;
        bottom: 30px;
        left: 10px;
        background: white;
        padding: 10px;
        border-radius: 5px;
        box-shadow: 0 0 15px rgba(0,0,0,0.2);
        z-index: 1000;
    }
</style>

<div id="map"></div>
<div class="legend">
    <input type="checkbox" id="toggleGL" on:change="{e => toggleLayer(groupesLayer, e.target.checked)}" checked> Groupe locaux<br>
    <input type="checkbox" id="toggleSSA" on:change="{e => toggleLayer(initiativesLayer, e.target.checked)}" checked> Initiatives locales SSA
</div>


