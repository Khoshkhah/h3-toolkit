---
layout: default
title: Interactive Demo
nav_order: 2
---

# 🗺️ Interactive Demo

Visual demonstration of boundary tracing and polygon operations.

**Demo Cell:** `86283082fffffff` (Resolution 6, San Francisco area)  
**Intermediate Resolution:** 10

<style>
.demo-grid { display: grid; grid-template-columns: 1fr; gap: 2rem; margin: 2rem 0; }
.demo-card { background: #f6f8fa; border-radius: 8px; overflow: hidden; border: 1px solid #d0d7de; }
.demo-card h3 { padding: 0.75rem 1rem; margin: 0; background: #f0f3f6; border-bottom: 1px solid #d0d7de; font-size: 1rem; display: flex; justify-content: space-between; }
.demo-card h3 code { font-size: 0.8rem; color: #656d76; font-weight: normal; }
.map { height: 350px; border-bottom: 1px solid #d0d7de; }
.legend { padding: 0.75rem 1rem; font-size: 0.9rem; }
.legend-item { display: flex; align-items: center; gap: 0.5rem; margin: 0.25rem 0; }
.legend-color { width: 24px; height: 4px; border-radius: 2px; }
</style>

<link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>

<div class="demo-grid">

<div class="demo-card">
<h3>Original Cell Boundary <code>cell_boundary_to_geojson_cpp()</code></h3>
<div id="map1" class="map"></div>
<div class="legend">
<div class="legend-item"><div class="legend-color" style="background: #238636;"></div> Original H3 cell boundary</div>
</div>
</div>

<div class="demo-card">
<h3>Boundary Children <code>children_on_boundary_faces()</code></h3>
<div id="map5" class="map"></div>
<div class="legend">
<div class="legend-item"><div class="legend-color" style="background: #f0883e;"></div> 240 boundary cells at res 10</div>
<div class="legend-item"><div class="legend-color" style="background: #238636; opacity: 0.5;"></div> Original cell (dashed)</div>
</div>
</div>

<div class="demo-card">
<h3>Boundary from Children <code>cell_boundary_from_children_cpp()</code></h3>
<div id="map2" class="map"></div>
<div class="legend">
<div class="legend-item"><div class="legend-color" style="background: #1f6feb;"></div> Merged boundary (240 cells → 1 polygon)</div>
<div class="legend-item"><div class="legend-color" style="background: #238636; opacity: 0.5;"></div> Original cell (dashed)</div>
</div>
</div>

<div class="demo-card">
<h3>Buffered Polygon (Accurate) <code>use_convex_hull=False</code></h3>
<div id="map3" class="map"></div>
<div class="legend">
<div class="legend-item"><div class="legend-color" style="background: #f85149;"></div> Buffered boundary (76m buffer)</div>
<div class="legend-item"><div class="legend-color" style="background: #238636; opacity: 0.5;"></div> Original cell (dashed)</div>
</div>
</div>

<div class="demo-card">
<h3>Buffered Polygon (Fast) <code>use_convex_hull=True</code></h3>
<div id="map4" class="map"></div>
<div class="legend">
<div class="legend-item"><div class="legend-color" style="background: #a371f7;"></div> Convex hull + buffer</div>
<div class="legend-item"><div class="legend-color" style="background: #f85149; opacity: 0.3;"></div> Accurate mode (comparison)</div>
</div>
</div>

</div>

<script>
const center = [37.77, -122.42];
const tileUrl = 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png';

// Map 1: Original cell
const map1 = L.map('map1').setView(center, 13);
L.tileLayer(tileUrl).addTo(map1);

// Map 5: Boundary children
const map5 = L.map('map5').setView(center, 13);
L.tileLayer(tileUrl).addTo(map5);

// Map 2: Merged boundary
const map2 = L.map('map2').setView(center, 13);
L.tileLayer(tileUrl).addTo(map2);

// Map 3: Buffered accurate
const map3 = L.map('map3').setView(center, 13);
L.tileLayer(tileUrl).addTo(map3);

// Map 4: Buffered fast
const map4 = L.map('map4').setView(center, 13);
L.tileLayer(tileUrl).addTo(map4);

// Load data
const baseUrl = '/h3-toolkit/';
fetch(baseUrl + 'demo_data.json')
  .then(r => r.json())
  .then(data => {
    // Map 1: Original cell
    L.geoJSON(data.cell_boundary, { style: { color: '#238636', weight: 3, fillOpacity: 0.2 } }).addTo(map1);
    
    // Map 5: Add cell outline to boundary children map
    L.geoJSON(data.cell_boundary, { style: { color: '#238636', weight: 2, fillOpacity: 0, dashArray: '5,5' } }).addTo(map5);
    
    // Map 2: Merged boundary
    L.geoJSON(data.cell_boundary, { style: { color: '#238636', weight: 2, fillOpacity: 0, dashArray: '5,5' } }).addTo(map2);
    L.geoJSON(data.boundary_from_children, { style: { color: '#1f6feb', weight: 3, fillOpacity: 0.15 } }).addTo(map2);
    
    // Map 3: Buffered accurate
    L.geoJSON(data.cell_boundary, { style: { color: '#238636', weight: 2, fillOpacity: 0, dashArray: '5,5' } }).addTo(map3);
    L.geoJSON(data.buffered_accurate, { style: { color: '#f85149', weight: 3, fillOpacity: 0.15 } }).addTo(map3);
    
    // Map 4: Comparison
    L.geoJSON(data.buffered_accurate, { style: { color: '#f85149', weight: 2, fillOpacity: 0.1, dashArray: '3,3' } }).addTo(map4);
    L.geoJSON(data.buffered_fast, { style: { color: '#a371f7', weight: 3, fillOpacity: 0.15 } }).addTo(map4);
  })
  .catch(e => console.error('Failed to load demo_data.json:', e));

// Load boundary children
fetch(baseUrl + 'boundary_children.json')
  .then(r => r.json())
  .then(data => {
    L.geoJSON(data, { style: { color: '#f0883e', weight: 1, fillOpacity: 0.4 } }).addTo(map5);
  })
  .catch(e => console.error('Failed to load boundary_children.json:', e));
</script>
