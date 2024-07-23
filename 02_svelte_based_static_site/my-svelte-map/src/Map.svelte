<script lang="ts">
    import { onMount } from 'svelte';
    import L from 'leaflet';
    import 'leaflet/dist/leaflet.css';
    import 'leaflet.markercluster';
    import 'leaflet.markercluster/dist/MarkerCluster.css';
    import 'leaflet.markercluster/dist/MarkerCluster.Default.css';
    import './Map.css'; // Import the CSS file
    import { loadYAML } from './utils'; // Create a utility function to load YAML files
  
    let map: L.Map;
    let groupesLayer: L.MarkerClusterGroup, initiativesLayer: L.MarkerClusterGroup;
  
    let groupes_locaux = [];
    let initiatives_ssa = [];
  
    // Load YAML data
    onMount(async () => {
      groupes_locaux = await loadYAML('/src/groupes_locaux.yml');
      initiatives_ssa = await loadYAML('/src/initiatives_ssa.yml');
  
      map = L.map('map').setView([46.603354, 1.888334], 6); // Center of France
  
      L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
        attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
      }).addTo(map);
  
      groupesLayer = L.markerClusterGroup();
      initiativesLayer = L.markerClusterGroup();
  
      const customIconGL = L.icon({
        iconUrl: '/images/icon_rs.png', // Update this path to your custom icon
        iconSize: [32, 32],
        iconAnchor: [16, 32],
        popupAnchor: [0, -32]
      });
  
      const customIconSSA = L.icon({
        iconUrl: '/images/icon_ssa.png', // Update this path to your custom icon
        iconSize: [58, 32],
        iconAnchor: [16, 32],
        popupAnchor: [0, -32]
      });
  
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
  
    const toggleLayer = (layer: L.LayerGroup, isChecked: boolean) => {
      if (isChecked) {
        map.addLayer(layer);
      } else {
        map.removeLayer(layer);
      }
    };
  </script>
  
  <div id="map"></div>
  <div class="legend">
    <label>
      <input type="checkbox" id="toggleGL" on:change="{e => toggleLayer(groupesLayer, e.target.checked)}" checked> Groupe locaux
    </label>
    <label>
      <input type="checkbox" id="toggleSSA" on:change="{e => toggleLayer(initiativesLayer, e.target.checked)}" checked> Initiatives locales SSA
    </label>
  </div>
  