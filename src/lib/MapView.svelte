<script>
  import { onMount, onDestroy } from 'svelte';
  import maplibregl from 'maplibre-gl';
  import { basemaps, MVT_TILES, MVT_SOURCE_LAYER, MAP_CENTER, MAP_ZOOM } from './config.js';

  export let basemap = 'street';
  export let pointLayerVisible = true;

  let container;
  let map;
  let appliedBasemap = basemap;
  let popup;
  let mapReady = false;

  const SOURCE_ID = 'suppliers';
  const LAYER_ID = 'suppliers-circles';
  const HOVER_LAYER_ID = 'suppliers-hover';

  const circlePaint = {
    'circle-radius': ['interpolate', ['linear'], ['zoom'], 6, 3.5, 12, 6.5, 16, 9],
    'circle-color': '#16845c',
    'circle-opacity': 0.88,
    'circle-stroke-width': 1.5,
    'circle-stroke-color': '#ffffff',
    'circle-blur': 0.03
  };

  function escapeHtml(value) {
    return String(value ?? '—').replaceAll('&', '&amp;').replaceAll('<', '&lt;').replaceAll('>', '&gt;');
  }

  function attributeRow(label, value) {
    return `<div class="supplier-popup__row"><span>${escapeHtml(label)}</span><strong>${escapeHtml(value)}</strong></div>`;
  }

  function onSupplierClick(event) {
    const props = event.features?.[0]?.properties;
    if (!props) return;

    const supplier = props.entityname ?? 'Unnamed supplier';
    const fid = props.FID ?? props.fid ?? '—';
    const area = props.plotareaha ?? '—';
    const region = props.regionlabel ?? '—';

    popup
      .setLngLat(event.lngLat)
      .setHTML(`
        <article class="supplier-popup">
          <header class="supplier-popup__header">
            <div class="supplier-popup__icon">●</div>
            <div>
              <span class="supplier-popup__eyebrow">SUPPLIER</span>
              <h3>${escapeHtml(supplier)}</h3>
            </div>
          </header>
          <div class="supplier-popup__body">
            ${attributeRow('Feature ID', fid)}
            ${attributeRow('Plot area', `${area} ha`)}
            ${attributeRow('Region', region)}
            ${attributeRow('Longitude', Number(event.lngLat.lng).toFixed(5))}
            ${attributeRow('Latitude', Number(event.lngLat.lat).toFixed(5))}
          </div>
          <footer class="supplier-popup__footer"><span><i></i> Selected supplier</span><b>MAPID × BINUS</b></footer>
        </article>
      `)
      .addTo(map);
  }

  function onPointer(event) {
    map.getCanvas().style.cursor = 'pointer';
    const feature = event.features?.[0];
    if (feature && map.getLayer(LAYER_ID)) {
      map.setPaintProperty(LAYER_ID, 'circle-radius', ['case', ['==', ['get', 'FID'], feature.properties?.FID ?? -999], 9, ['interpolate', ['linear'], ['zoom'], 6, 3.5, 12, 6.5, 16, 9]]);
    }
  }
  function onPointerOut() {
    map.getCanvas().style.cursor = '';
    if (map.getLayer(LAYER_ID)) map.setPaintProperty(LAYER_ID, 'circle-radius', circlePaint['circle-radius']);
  }

  function clearSupplierLayer() {
    if (!map?.getStyle()) return;
    if (map.getLayer(LAYER_ID)) {
      map.off('click', LAYER_ID, onSupplierClick);
      map.off('mouseenter', LAYER_ID, onPointer);
      map.off('mouseleave', LAYER_ID, onPointerOut);
      map.removeLayer(LAYER_ID);
    }
    if (map.getSource(SOURCE_ID)) map.removeSource(SOURCE_ID);
  }

  function addSupplierLayer() {
    if (!map?.getStyle() || map.getSource(SOURCE_ID)) return;
    map.addSource(SOURCE_ID, { type: 'vector', tiles: [MVT_TILES], minzoom: 0, maxzoom: 14 });
    map.addLayer({ id: LAYER_ID, type: 'circle', source: SOURCE_ID, 'source-layer': MVT_SOURCE_LAYER, paint: circlePaint });
    map.on('click', LAYER_ID, onSupplierClick);
    map.on('mouseenter', LAYER_ID, onPointer);
    map.on('mouseleave', LAYER_ID, onPointerOut);
  }

  function applySupplierLayer() {
    if (!map?.getStyle()) return;
    popup?.remove();
    clearSupplierLayer();
    if (pointLayerVisible) addSupplierLayer();
  }

  onMount(() => {
    popup = new maplibregl.Popup({ closeButton: true, closeOnClick: true, offset: 14, maxWidth: '320px', className: 'supplier-map-popup' });
    map = new maplibregl.Map({ container, style: basemaps[basemap], center: MAP_CENTER, zoom: MAP_ZOOM });
    map.addControl(new maplibregl.NavigationControl({ showCompass: true, visualizePitch: true }), 'top-right');
    map.addControl(new maplibregl.ScaleControl({ maxWidth: 110, unit: 'metric' }), 'bottom-right');
    map.on('style.load', () => { mapReady = true; applySupplierLayer(); });
  });

  $: if (map && basemap !== appliedBasemap) {
    appliedBasemap = basemap;
    mapReady = false;
    popup?.remove();
    map.setStyle(basemaps[basemap], { diff: false });
  }

  $: if (map && mapReady && pointLayerVisible !== undefined) {
    if (pointLayerVisible && !map.getLayer(LAYER_ID)) addSupplierLayer();
    if (!pointLayerVisible && map.getLayer(LAYER_ID)) { popup?.remove(); clearSupplierLayer(); }
  }

  onDestroy(() => { popup?.remove(); map?.remove(); });
</script>

<div class="map" bind:this={container}></div>

<style>
  :global(.maplibregl-map) { font-family: Inter, ui-sans-serif, system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif; }
  .map { width: 100%; height: 100%; min-height: 400px; }
  :global(.supplier-map-popup .maplibregl-popup-content) { padding: 0; overflow: hidden; border-radius: 18px; box-shadow: 0 14px 40px rgba(17, 38, 30, .22); border: 1px solid rgba(218, 226, 222, .9); }
  :global(.supplier-map-popup .maplibregl-popup-tip) { border-top-color: white; }
  :global(.supplier-map-popup .maplibregl-popup-close-button) { z-index: 2; width: 28px; height: 28px; top: 9px; right: 9px; display: grid; place-items: center; border-radius: 8px; color: #6f7c76; font-size: 20px; line-height: 1; }
  :global(.supplier-map-popup .maplibregl-popup-close-button:hover) { background: #edf5f1; color: #176f50; }
  :global(.supplier-popup) { width: 310px; background: #fff; color: #24332d; }
  :global(.supplier-popup__header) { display: flex; align-items: center; gap: 11px; padding: 15px 42px 13px 15px; background: linear-gradient(135deg, #eaf8f2, #ffffff); border-bottom: 1px solid #e7eeea; }
  :global(.supplier-popup__icon) { width: 34px; height: 34px; display: grid; place-items: center; flex: 0 0 34px; border-radius: 10px; background: #16845c; color: #fff; font-size: 12px; box-shadow: 0 5px 12px rgba(22,132,92,.2); }
  :global(.supplier-popup__eyebrow) { display: block; margin-bottom: 2px; color: #16845c; font-size: 9px; line-height: 1; letter-spacing: .12em; font-weight: 800; }
  :global(.supplier-popup h3) { margin: 0; max-width: 180px; font-size: 14px; line-height: 1.3; color: #1e2d27; }
  :global(.supplier-popup__body) { padding: 6px 15px; }
  :global(.supplier-popup__row) { display: grid; grid-template-columns: 82px 1fr; gap: 10px; align-items: start; padding: 9px 0; border-bottom: 1px solid #eef2f0; }
  :global(.supplier-popup__row:last-child) { border-bottom: 0; }
  :global(.supplier-popup__row span) { font-size: 11px; color: #8a9691; }
  :global(.supplier-popup__row strong) { font-size: 11px; line-height: 1.4; font-weight: 650; color: #34443d; text-align: right; overflow-wrap: anywhere; }
  :global(.supplier-popup__footer) { display:flex;justify-content:space-between;align-items:center;padding:10px 15px;background:#fafcfb;border-top:1px solid #edf1ef;font-size:8px;color:#8c9c95;letter-spacing:.02em; }
  :global(.supplier-popup__footer span){display:flex;align-items:center;gap:5px}:global(.supplier-popup__footer i){width:6px;height:6px;border-radius:50%;background:#16a575;box-shadow:0 0 0 3px #e2f6ef}:global(.supplier-popup__footer b){font-size:7px;letter-spacing:.1em;color:#a1ada7}
</style>
