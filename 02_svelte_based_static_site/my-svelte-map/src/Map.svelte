<script lang="ts">
    import { onMount } from 'svelte';
    import L from 'leaflet';
    import 'leaflet/dist/leaflet.css';
    import 'leaflet.markercluster';
    import 'leaflet.markercluster/dist/MarkerCluster.css';
    import 'leaflet.markercluster/dist/MarkerCluster.Default.css';
    import './Map.css'; // Import the CSS file
  
    let map: L.Map;
    let groupesLayer: L.MarkerClusterGroup, initiativesLayer: L.MarkerClusterGroup;
  
    const groupes_locaux = [
      { "name": "Groupe Local Alpes-Maritimes", "type": "Groupe Local", "lat": 43.7102, "lng": 7.2620, "address": "Alpes-Maritimes", "postcode": "", "city": "", "website": "", "phone": "", "email": "alpes-maritimes@reseau-salariat.info", "summary": "Groupe local pour les Alpes-Maritimes." },
      { "name": "Groupe Local Alsace", "type": "Groupe Local", "lat": 48.5734, "lng": 7.7521, "address": "Alsace", "postcode": "", "city": "", "website": "", "phone": "", "email": "alsace@reseau-salariat.info", "summary": "Groupe local pour l'Alsace." },
      { "name": "Groupe Local Vienne", "type": "Groupe Local", "lat": 46.5802, "lng": 0.3404, "address": "Vienne", "postcode": "", "city": "", "website": "", "phone": "", "email": "adherents-vienne@lists.reseau-salariat.info", "summary": "Groupe local pour la Vienne." }
    ];
  
    const initiatives_ssa = [
      {
        "name": "Collectif Acclimat’action",
        "type": "initiative SSA",
        "Structures porteuses": ["Vrac Bordeaux", "les râteleurs", "supercoop", "capsciences", "CeDS", "Du vert dans les rouages", "e-graine", "L'estey", "Moisa et MIAM - acteurs issus de l’éducation populaire de l’économie sociale et solidaire du travail social ou de la recherche scientifique"],
        "Territoire d’expérimentation": "Bordeaux métropole et pays foyen",
        "Partenaires": ["l’ADEME", "la Région Nouvelle Aquitaine", "la Fondation Carasso", "la Fondation Européenne pour le Climat", "Bordeaux Métropole", "le Conseil Départemental de Gironde et la Ville de Bordeaux"],
        "lat": 44.8775859,
        "lng": -0.5717582,
        "address": "Av. André Reinson",
        "postcode": "33300",
        "city": "Bordeaux",
        "website": "",
        "phone": "",
        "email": "",
        "summary": "Pour réfléchir et expérimenter dans les quartiers populaires",
        "URLs": ["https://www.acclimataction.fr/", "https://securite-sociale-alimentation.org/initiative/collectif-acclimataction-pour-reflechir-et-experimenter-dans-les-quartiers-populaires/"]
      },
      {
        "name": "Caisse commune de l’alimentation Montpellier",
        "type": "initiative SSA",
        "Structures porteuses": ["Réseau cocagne", "Secours Catholique France", "VRAC", "UGESS", "Réseau Civam"],
        "Territoire d’expérimentation": "Montpellier, France",
        "Partenaires": ["collectif national « Accès digne à l’alimentation »"],
        "lat": 43.6119,
        "lng": 3.8772,
        "address": "Montpellier",
        "postcode": "",
        "city": "Montpellier",
        "website": "https://tav-montpellier.xyz/?Experimentation",
        "phone": "",
        "email": "",
        "summary": "Un collectif informel de 25 organisations mobilisé pour monter une expérimentation autour de la démocratie alimentaire.",
        "URLs": ["https://tav-montpellier.xyz/?Experimentation"]
      },
      {
        "name": "Collectif SSA 35",
        "type": "initiative SSA",
        "Structures porteuses": ["Collectif informel - groupe local de réflexion vers une SSA"],
        "Territoire d’expérimentation": "Ille-et-Vilaine (35)",
        "Partenaires": ["Alternatiba", "AMAP", "ATTAC Rennes", "CIVAM", "Le P’tit Blosneur", "Maison de la consommation et l’environnement"],
        "lat": 48.1173,
        "lng": -1.6778,
        "address": "Place de la Gare",
        "postcode": "35000",
        "city": "Rennes",
        "website": "",
        "phone": "",
        "email": "collectif-ssa35@protonmail.com",
        "summary": "Le Collectif SSA 35 mène différentes actions pour faire connaître la proposition politique au grand public et mobiliser pour la création de la SSA.",
        "URLs": []
      }
    ];
  
    onMount(() => {
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
  