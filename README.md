VERVE EXCHANGE RESPONSE INSPECTOR

A single-file browser tool for rendering and debugging ad responses from the Verve / PubNative exchange stack. No server, no build step, no dependencies — open in any browser.

[→ Open the tool](https://kadurann.github.io/response-inspector) 


WHAT IT DOES

Paste any Verve ad response JSON, click Render Ad, and the tool will:


Render the creative in a sandboxed iframe (banner, MRAID, native-in-banner, or VAST video)
Fire and log all impression, click, and VAST tracking beacons in real time
Show a structured summary of every bid field that matters for debugging
Let you manually fire nurl, lurl, and burl notices



SUPPORTED RESPONSE TYPES

TypeDescriptionType 1 — oRTB DSP ResponseRaw DSP bid response before the Verve ad server. adm contains the DSP creative directly.Type 2 — oRTB P+ Ad ResponseAfter the P+ (PubNative) ad server. adm is the Verve wrapper script with nurl / lurl.Type 3 — oRTB B+ Ad ResponseAfter the B+ (Smaato) ad server. Same wrapper structure, uses burl as supply billing notice.Type 4 — APIv3 / HyBid MAX_ADMoRTB envelope with admurl in ext.signaldata. Tool fetches the MAX_ADM and renders banner or VAST video, honouring all remoteconfigs.


FEATURES

Creative Rendering


Banner / HTML — MRAID 2.0 stub injected, macros resolved, renders via iframe.srcdoc
Native-in-banner — auto-detected from the pubnative-wrapper template, flagged in the summary
VAST video — inline XML or URL, full wrapper chain resolution (up to 5 hops), companion endcard, custom endcard
MRAID — stub covers getState, isViewable, addEventListener, open, and more


Beacon Tracking


Beacon interceptor patches fetch, XHR, Image.src, and sendBeacon inside the iframe
All fired beacons appear in a searchable, filterable table with type pills and timestamps
VAST tracking events fire at the correct playback quartiles with [CONTENTPLAYHEAD] macro resolved
Type 4 impression trackers held behind OMID viewability gate (≥50% visible for 1 continuous second)
Manual fire buttons for nurl, lurl, burl


Type 4 / HyBid Specifics


Fetches admurl from ext.signaldata, detects asset type (htmlbanner or vast2)
Reads remoteconfigs from MAX_ADM meta: skip offset, audio state, endcard behaviour, icon size
Resolves adexperience (performance → pc_* configs, brand → bc_* configs)
Ad format selector (Banner / Interstitial / Rewarded) — affects which skip offset and configs apply
Custom endcard HTML rendered after companion, with configurable close delay


Parsed Summary


Format detection: VAST inline XML, VAST URL, native-in-banner, or banner/HTML
Ad template ID (data-tid) decoded to human-readable name when present
Ad experience flag shown for fullscreen Type 4 placements
nurl, lurl, burl presence flagged with correct semantics per type



HOW IT COMPARES TO A REAL SDK (HYBID) ENVIRONMENT

Impression beacons: For Types 1/2/3, the beacon interceptor captures whatever the creative fires from inside the iframe — no external viewability gate is added on top. For Type 4, the full OMID logic is replicated. For VAST, impressions fire on first play — identical to the SDK.

Click beacons: For P+/B+ wrapper responses, a capture-phase click listener is added to the outer frame as a safety net, reading the wrapper's CB[] array directly since the iframe is same-origin. This handles cases where an external DSP creative script bypasses event propagation.

window.top navigation: Clicks that navigate via window.top.open() open a new browser tab instead of a deep-link — expected in a desktop browser context. Click pixel firing is unaffected.


ARCHITECTURE

Single HTML file (~1,700 lines). No framework, no npm, no build.


Vanilla JS for all parsing, rendering, and beacon logic
DOMParser for VAST XML parsing
IntersectionObserver for OMID viewability (Type 4)
Native <video> for VAST playback
iframe.srcdoc for sandboxed rendering
postMessage for cross-iframe beacon reporting



USAGE


Select the response type (Types 1–4)
Paste the full JSON response
For Type 4, select Banner / Interstitial / Rewarded before rendering
Click ▶ Render Ad
Watch the Event Log and Beacons & Trackers panels


To test a specific size: use the Overrides section to set a custom width/height before rendering.
