<script>
  import MapView from './lib/MapView.svelte';

  let basemap = 'street';
  let pointLayerVisible = true;
  let sidebarOpen = true;

  const options = [
    { id: 'street', label: 'Street', sub: 'MAPID Street 2D', icon: '▦' },
    { id: 'light', label: 'Light', sub: 'Clean cartography', icon: '◫' },
    { id: 'satellite', label: 'Satellite', sub: 'Imagery view', icon: '◉' },
    { id: 'demo', label: 'Demo', sub: 'MapLibre fallback', icon: '◇' }
  ];
</script>

<div class="app" class:sidebar-collapsed={!sidebarOpen}>
  <aside class="sidebar">
    <div class="brand-row">
      <div class="brand">
        <div class="brand-mark">M</div>
        <div><p class="kicker">MAPID × BINUS</p><h1>GeoSupply</h1></div>
      </div>
      <button class="icon-button collapse" aria-label="Collapse sidebar" on:click={() => sidebarOpen = false}>‹</button>
    </div>

    <div class="workspace-card">
      <div class="workspace-icon">⌖</div>
      <div><span>ACTIVE WORKSPACE</span><strong>Supplier Distribution</strong><small>Côte d’Ivoire · WebGIS</small></div>
      <span class="online-dot"></span>
    </div>

    <section>
      <div class="section-head"><span>LAYERS</span><button class="mini-button" title="Layer settings">•••</button></div>
      <div class="layer-card" class:disabled={!pointLayerVisible}>
        <div class="layer-symbol"><i></i><i></i><i></i></div>
        <div class="layer-copy"><strong>Supplier Locations</strong><span>Point · Vector tiles</span></div>
        <label class="switch" aria-label="Toggle supplier points"><input type="checkbox" bind:checked={pointLayerVisible}/><span></span></label>
      </div>
      <div class="layer-meta"><span><b class:green={pointLayerVisible}></b>{pointLayerVisible ? 'Visible on map' : 'Layer hidden'}</span><span>Point layer</span></div>
    </section>

    <section>
      <div class="section-head"><span>BASEMAP</span></div>
      <div class="basemap-grid">
        {#each options as option}
          <button class="basemap-card" class:selected={basemap === option.id} on:click={() => basemap = option.id}>
            <span class="basemap-preview preview-{option.id}"><b>{option.icon}</b></span>
            <span><strong>{option.label}</strong><small>{option.sub}</small></span>
            {#if basemap === option.id}<i class="check">✓</i>{/if}
          </button>
        {/each}
      </div>
    </section>

    <div class="sidebar-footer">
      <div class="avatar">BI</div><div><strong>WebGIS Project</strong><span>BINUS × MAPID</span></div><button class="mini-button">⌄</button>
    </div>
  </aside>

  {#if !sidebarOpen}<button class="reopen" on:click={() => sidebarOpen = true} aria-label="Open sidebar">☰</button>{/if}

  <main class="map-shell">
    <MapView {basemap} {pointLayerVisible} />

    <div class="topbar">
      <div class="title-group"><span class="breadcrumb">WEBGIS <b>/</b> SUPPLIER NETWORK</span><strong>Supplier Distribution Map</strong></div>
      <div class="top-actions">
        <div class="live-pill"><i></i> LIVE DATA</div>
        <button class="action-button" on:click={() => location.reload()} title="Reset application">↻ <span>Reset view</span></button>
      </div>
    </div>

    <div class="stats">
      <div class="stat-card"><span class="stat-icon">●</span><div><small>DATA LAYER</small><strong>Suppliers</strong></div></div>
      <div class="stat-card"><span class="stat-icon outline">◎</span><div><small>COVERAGE</small><strong>Côte d’Ivoire</strong></div></div>
      <div class="stat-card"><span class="stat-icon pulse">↗</span><div><small>STATUS</small><strong>{pointLayerVisible ? 'Active' : 'Hidden'}</strong></div></div>
    </div>

    <div class="legend-card">
      <div><strong>Map legend</strong><span>Distribution layer</span></div>
      <div class="legend-row"><i></i><span>Supplier location</span></div>
      <small>Click any point to inspect attributes</small>
    </div>
  </main>
</div>

<style>
  :global(:root){font-family:Inter,ui-sans-serif,-apple-system,BlinkMacSystemFont,"Segoe UI",sans-serif;color:#14231d;background:#edf3f0} :global(button){font:inherit}
  .app{display:flex;height:100vh;overflow:hidden;background:#edf3f0}.sidebar{width:330px;flex:0 0 330px;z-index:5;display:flex;flex-direction:column;padding:20px;background:rgba(250,252,251,.97);border-right:1px solid #dfe8e3;box-shadow:10px 0 35px rgba(28,58,45,.08);transition:.3s ease}.sidebar-collapsed .sidebar{margin-left:-330px}.brand-row,.brand,.section-head,.sidebar-footer,.topbar,.top-actions,.stat-card,.layer-meta{display:flex;align-items:center}.brand-row{justify-content:space-between}.brand{gap:11px}.brand-mark{width:40px;height:40px;display:grid;place-items:center;border-radius:12px;background:linear-gradient(145deg,#087b58,#16a274);color:white;font-weight:850;box-shadow:0 8px 18px rgba(9,126,88,.24)}.kicker{margin:0 0 2px;font-size:9px;letter-spacing:.17em;color:#789087;font-weight:800}h1{margin:0;font-size:19px;letter-spacing:-.04em}.icon-button,.mini-button{border:0;background:transparent;color:#6f8179;cursor:pointer}.icon-button{width:34px;height:34px;border:1px solid #e2e9e5;border-radius:10px;background:white;font-size:24px}.workspace-card{position:relative;display:flex;align-items:center;gap:10px;margin:23px 0 8px;padding:12px;border:1px solid #dfe8e3;border-radius:14px;background:white;box-shadow:0 5px 16px rgba(28,58,45,.04)}.workspace-icon{width:35px;height:35px;display:grid;place-items:center;border-radius:10px;background:#e9f7f1;color:#07835c;font-size:19px}.workspace-card div:nth-child(2){display:flex;min-width:0;flex:1;flex-direction:column}.workspace-card span:not(.online-dot){font-size:8px;letter-spacing:.12em;color:#91a099;font-weight:800}.workspace-card strong{margin:2px 0;font-size:12px}.workspace-card small{font-size:10px;color:#84948d}.online-dot{width:7px;height:7px;border-radius:50%;background:#20ae7c;box-shadow:0 0 0 4px #e5f7f0}section{padding:20px 0;border-top:1px solid #e9eeeb}.section-head{justify-content:space-between;margin-bottom:11px}.section-head>span{font-size:9px;letter-spacing:.16em;font-weight:850;color:#81928a}.layer-card{display:flex;align-items:center;gap:11px;padding:12px;border:1px solid #dce6e1;border-radius:13px;background:#fff;box-shadow:0 4px 14px rgba(31,66,51,.04);transition:.2s}.layer-card.disabled{opacity:.55}.layer-symbol{position:relative;width:38px;height:38px;border-radius:10px;background:#e8f7f1}.layer-symbol i{position:absolute;width:8px;height:8px;border:2px solid white;border-radius:50%;background:#118c64;box-shadow:0 1px 3px #7aa997}.layer-symbol i:nth-child(1){left:9px;top:9px}.layer-symbol i:nth-child(2){right:8px;top:14px}.layer-symbol i:nth-child(3){left:14px;bottom:7px}.layer-copy{display:flex;min-width:0;flex:1;flex-direction:column}.layer-copy strong{font-size:12px}.layer-copy span{margin-top:3px;font-size:10px;color:#899991}.switch{position:relative;width:38px;height:22px}.switch input{display:none}.switch span{position:absolute;inset:0;border-radius:99px;background:#ccd7d2;cursor:pointer;transition:.2s}.switch span:after{content:'';position:absolute;width:16px;height:16px;left:3px;top:3px;border-radius:50%;background:white;box-shadow:0 2px 5px #9aa9a2;transition:.2s}.switch input:checked+span{background:#0d8c63}.switch input:checked+span:after{transform:translateX(16px)}.layer-meta{justify-content:space-between;padding:9px 3px 0;font-size:9px;color:#91a099}.layer-meta span:first-child{display:flex;align-items:center;gap:5px}.layer-meta b{width:5px;height:5px;border-radius:50%;background:#a9b5af}.layer-meta b.green{background:#16a575}.basemap-grid{display:grid;grid-template-columns:1fr 1fr;gap:8px}.basemap-card{position:relative;padding:7px;border:1px solid #e0e8e4;border-radius:12px;background:white;text-align:left;cursor:pointer;transition:.18s}.basemap-card:hover{transform:translateY(-1px);border-color:#a9cfc0}.basemap-card.selected{border-color:#148d67;box-shadow:0 0 0 2px rgba(20,141,103,.08)}.basemap-preview{height:52px;display:grid;place-items:center;margin-bottom:7px;border-radius:8px;overflow:hidden;color:#315348;font-size:18px;background-color:#e7ece9;background-image:linear-gradient(32deg,transparent 45%,#c7d6cf 46%,#c7d6cf 49%,transparent 50%),linear-gradient(145deg,transparent 44%,#d2ddd8 45%,#d2ddd8 48%,transparent 49%)}.preview-light{background-color:#f4f4f0}.preview-satellite{background-color:#7f9274;color:white}.preview-demo{background-color:#dce9ed}.basemap-card>span:nth-child(2){display:flex;flex-direction:column}.basemap-card strong{font-size:10px}.basemap-card small{margin-top:2px;font-size:8px;color:#94a29b}.check{position:absolute;right:11px;top:11px;width:18px;height:18px;display:grid;place-items:center;border-radius:50%;background:#0b8b62;color:#fff;font-size:10px;font-style:normal}.sidebar-footer{gap:9px;margin-top:auto;padding-top:15px;border-top:1px solid #e7edea}.avatar{width:32px;height:32px;display:grid;place-items:center;border-radius:10px;background:#173d31;color:white;font-size:10px;font-weight:800}.sidebar-footer div:nth-child(2){display:flex;flex:1;flex-direction:column}.sidebar-footer strong{font-size:10px}.sidebar-footer span{font-size:9px;color:#91a099}.reopen{position:absolute;z-index:8;left:18px;top:18px;width:42px;height:42px;border:1px solid rgba(255,255,255,.8);border-radius:12px;background:rgba(255,255,255,.94);box-shadow:0 8px 24px rgba(25,55,43,.14);cursor:pointer}.map-shell{position:relative;min-width:0;flex:1}.topbar{position:absolute;z-index:3;left:22px;right:22px;top:18px;justify-content:space-between;pointer-events:none}.title-group{display:flex;flex-direction:column;padding:11px 15px;border:1px solid rgba(255,255,255,.8);border-radius:14px;background:rgba(255,255,255,.92);box-shadow:0 9px 28px rgba(23,54,42,.13);backdrop-filter:blur(14px)}.breadcrumb{font-size:8px;letter-spacing:.13em;color:#71867d;font-weight:800}.breadcrumb b{margin:0 5px;color:#c1ccc7}.title-group strong{margin-top:3px;font-size:15px;letter-spacing:-.02em}.top-actions{gap:8px;pointer-events:auto}.live-pill,.action-button{height:38px;display:flex;align-items:center;gap:7px;border:1px solid rgba(255,255,255,.8);border-radius:11px;background:rgba(255,255,255,.92);box-shadow:0 7px 20px rgba(23,54,42,.1);backdrop-filter:blur(12px);font-size:9px;font-weight:800;color:#486158}.live-pill{padding:0 12px}.live-pill i{width:6px;height:6px;border-radius:50%;background:#16a676;box-shadow:0 0 0 4px rgba(22,166,118,.12)}.action-button{padding:0 12px;cursor:pointer;color:#344c43}.stats{position:absolute;z-index:3;left:22px;top:91px;display:flex;gap:8px;pointer-events:none}.stat-card{gap:9px;min-width:132px;padding:9px 11px;border:1px solid rgba(255,255,255,.76);border-radius:12px;background:rgba(255,255,255,.88);box-shadow:0 7px 22px rgba(23,54,42,.09);backdrop-filter:blur(12px)}.stat-icon{width:27px;height:27px;display:grid;place-items:center;border-radius:8px;background:#e4f5ee;color:#07855e;font-size:9px}.stat-icon.outline{font-size:17px}.stat-icon.pulse{background:#edf4ff;color:#416e9c;font-size:13px}.stat-card div{display:flex;flex-direction:column}.stat-card small{font-size:7px;letter-spacing:.1em;color:#93a29b;font-weight:800}.stat-card strong{margin-top:2px;font-size:10px}.legend-card{position:absolute;z-index:3;left:22px;bottom:22px;width:190px;padding:13px;border:1px solid rgba(255,255,255,.8);border-radius:14px;background:rgba(255,255,255,.92);box-shadow:0 8px 26px rgba(23,54,42,.12);backdrop-filter:blur(14px)}.legend-card>div:first-child{display:flex;justify-content:space-between;align-items:center;padding-bottom:9px;border-bottom:1px solid #e8eeeb}.legend-card strong{font-size:10px}.legend-card>div:first-child span{font-size:8px;color:#96a49d}.legend-row{display:flex;align-items:center;gap:8px;padding:10px 0 6px;font-size:9px}.legend-row i{width:10px;height:10px;border:2px solid white;border-radius:50%;background:#16845c;box-shadow:0 0 0 1px #16845c}.legend-card>small{font-size:8px;color:#92a19a}@media(max-width:850px){.sidebar{width:285px;flex-basis:285px}.stats{display:none}.top-actions .action-button span{display:none}.basemap-grid{grid-template-columns:1fr}.basemap-preview{display:none}}@media(max-width:650px){.sidebar{position:absolute;height:100%;width:285px}.sidebar-collapsed .sidebar{margin-left:-285px}.topbar{left:14px;right:14px}.title-group{max-width:210px}.live-pill{display:none}.legend-card{left:14px;bottom:14px}}
</style>
