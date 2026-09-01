# SIH 2026 — Shortlisted Software Problem Statements, Elaborated

**Scope of this document.** Twenty software-only problem statements from your shortlist, each decoded into what the system actually has to do, then read against the SIH idea-evaluation criteria: novelty, complexity, clarity, feasibility/practicability/sustainability, scale of impact, user experience, and potential for future work.

**Two things about how I've written this.**

First, I have deliberately *not* weighted these toward remote sensing or GIS-heavy work. Several entries here are essentially workflow-and-database applications with a map view bolted on, and I've said so plainly rather than inflating their geospatial content to make them look like a natural fit.

Second, each entry ends with a **failure mode** note — the specific thing that makes a demo of that PS collapse in front of judges. This is the part most PS-selection guides omit, and it is usually the deciding factor.

**Confidence flags used throughout:**
- *(high)* — I'm confident from the PS text itself or well-established technical knowledge
- *(medium)* — reasonable inference, worth verifying
- *(low / verify)* — I'm reasoning from patterns, not knowledge; check before you rely on it

Organisation attributions are inferences from the PS wording, not confirmed facts. Verify each on the SIH portal.

---

## 1. PS 26001 — AI-Based Early Warning and Landslide Risk Monitoring for the North Eastern Region
**Theme:** Disaster Management

### What it actually asks for
A susceptibility-and-warning stack in three layers. A static susceptibility surface built from terrain derivatives (slope, aspect, curvature, relief), lithology, land cover, and distance-to-road/drainage. A dynamic trigger layer driven by rainfall — antecedent rainfall accumulation and intensity-duration thresholds. And a delivery layer: alerts, a crowdsourced geotagged reporting channel, dashboards, offline/low-bandwidth operation, multilingual notifications.

The intellectually interesting part is the coupling between the static and dynamic layers. Susceptibility mapping alone is a solved undergraduate exercise. Turning susceptibility into a *time-varying probability* conditioned on rainfall is not.

### Novelty
Low as posed, moderate if you reframe. Landslide susceptibility mapping using logistic regression, random forests, or AHP has been done thousands of times, including specifically for Sikkim, Meghalaya, Mizoram, and the Guwahati region. Judges in the disaster-management theme have almost certainly seen it. *(high confidence)*

Where headroom exists: rainfall-threshold learning rather than rainfall-threshold assumption. Most operational systems import an intensity–duration curve from published literature (often Caine's or a regional variant) and apply it uniformly. A system that *learns* region-specific ID thresholds from the historical inventory, and reports uncertainty on them, is a defensible novelty claim.

Second angle: road-network connectivity impact. Predicting that a slope will fail is less actionable than predicting *which villages become unreachable*. Graph analysis over the NH/SH network with landslide-blocked edges converts a hazard map into a logistics product. I have not seen this done well in student projects. *(medium)*

### Complexity
Moderate, and unevenly distributed. The ML is easy. The hard parts are class imbalance (landslide pixels are a tiny fraction of the study area, and naive sampling produces models with 99% accuracy that predict nothing), spatial autocorrelation in train/test splitting (random splits leak neighbouring pixels and inflate AUC badly), and the absence of true negatives — you have a landslide inventory, not a no-landslide inventory.

If your presentation addresses spatially-blocked cross-validation and negative-sample strategy, you will be operating above the level of most competing teams. If it doesn't, a knowledgeable judge will ask and the answer will hurt.

### Feasibility
Data is genuinely available. Bhukosh (GSI) hosts a national landslide inventory. SRTM/Cartosat DEMs, GSI lithology, IMD gridded rainfall, GPM IMERG for near-real-time rainfall, Sentinel-2 for land cover. Terrain derivatives from a DEM are a morning's work.

The blocker the PS quietly introduces is **soil moisture sensors**. Those do not exist as an accessible network in NER. You will be simulating that input, and you should say so openly in the deck rather than let a judge discover it. SMAP or Sentinel-1-derived soil moisture at 9 km or 1 km is a defensible substitute, and naming it as a substitution reads as rigour rather than as a gap. *(high)*

### Scale of impact
High and easy to argue. NER landslide fatalities and road closures are well-documented and politically salient. The eight-state framing gives you a clean scale story.

### User experience
The PS explicitly demands multilingual notification and offline sync. Take this seriously — it is a scored criterion, and disaster-management judges care about the district magistrate and the village-level worker, not the model. Three distinct interfaces: an administrator dashboard, a field-officer reporting app, a citizen alert channel. Offline-first sync via local storage with queued upload is not hard to build and demos memorably.

### Future work
Strong. InSAR-based slow deformation monitoring (Sentinel-1 SBAS) is the natural extension and shows you know where the field is going. So does assimilating field reports back into the model as pseudo-labels.

### Failure mode
The demo shows a static heatmap that never changes, because the "real-time" pipeline is a cron job nobody wired up. Judges ask "what changes between 9 AM and 6 PM in this display?" and there is no answer. Build the temporal dimension first, even with replayed historical rainfall; the static map is the easy part and it is not what distinguishes you.

---

## 2. PS 26009 — AI/ML and Space Technology for Manganese Reserve Identification and Production Shortfall Prediction
**Theme:** Space Technology · Almost certainly MOIL Limited

### What it actually asks for
Two loosely-joined systems presented as one. A mineral prospectivity mapping component (where is the manganese) and a production forecasting component (why will we miss target this quarter, and what should we change).

### Novelty
Here I need to flag something directly, because it affects whether you should pick this at all.

The PS asks you to identify **sub-surface** manganese reserves using **rainfall, soil moisture, vegetation index, and land surface temperature**. Those four variables have no established causal pathway to sub-surface manganese mineralisation. Vegetation stress over metalliferous soils (geobotanical anomaly) is a real phenomenon, but it is weak, confounded by everything, and is not how anyone locates manganese. Manganese prospecting uses hyperspectral or ASTER-band mineral alteration mapping, structural lineament analysis, gravity/magnetic geophysics, and drilling. *(high confidence that the listed variables are the wrong tool; medium confidence about what MOIL actually intends)*

This creates a fork:

- **Take the PS literally**, build what it asks, get a model that appears to work because it is overfitting to spatial autocorrelation with known deposit locations, and hope no geologist is on the panel. Risky.
- **Reframe defensibly**: use ASTER/Sentinel-2 band ratios for alteration mineral indices and lineament density as the geological signal, and use the rainfall/NDVI/LST variables where they actually belong — in the *production* half, as predictors of monsoon-driven mine flooding, haul-road degradation, and equipment downtime. This is honest, technically sound, and shows domain judgement.

The second path is where the novelty is, and it is real novelty: weather-conditioned mine production forecasting with corrective-action recommendation is a genuinely under-served problem. *(medium)*

### Complexity
The prospectivity half is a moderate ML problem with a severe data problem. The production forecasting half is a time-series problem with an optimisation layer (rescheduling, equipment redeployment) that is more interesting than it looks — this is effectively a constrained scheduling problem, and if you actually implement one rather than printing heuristic advice, complexity scores well.

### Feasibility
The critical unknown is whether MOIL releases historical production, equipment downtime, and drilling data. Without it the production half is synthetic. *(low confidence — verify. Check the SIH portal for attached datasets or a contact; this single fact should determine whether you pick this PS.)*

Public substitutes: GSI Bhukosh geology and mineral occurrence layers, ASTER on USGS EarthExplorer, and MOIL's own annual reports which publish production figures at a coarse annual/mine level.

### Scale of impact
Narrow but deep. One PSU, roughly ten mines, mostly in Nagpur–Bhandara and Balaghat. That is a small scale story, but a specific and credible one. Judges from the sponsoring organisation care more about fit to their operations than about national scale.

### User experience
Single-persona: a mine planning officer. Build for them specifically — production vs. target, forecast band, ranked shortfall drivers, recommended action. Resist the temptation to build a general-purpose GIS.

### Future work
Extension to other MOIL minerals and to other mining PSUs is the obvious pitch.

### Failure mode
A geologist judge asks how NDVI relates to sub-surface manganese, and the team has no answer beyond "the model found a correlation." Prepare for this question specifically, or reframe as described above.

---

## 3. PS 26012 — AI-Based Automated Urban Parcel Mapping and Cadastral Feature Extraction from Drone Imagery
**Theme:** Smart Automation · Reads like Department of Land Resources / a state survey department

### What it actually asks for
Take high-resolution drone orthoimagery plus DSM/DTM, and produce a topologically clean cadastral parcel layer: parcel boundaries, building footprints, roads, land-use classes, with overlap and geometry-inconsistency detection, plus a Web-GIS editing interface.

### Novelty
The segmentation is commodity. Building footprint extraction from aerial imagery has pretrained models, benchmark datasets, and off-the-shelf pipelines. Nobody scores novelty points for a U-Net or SAM on rooftops in 2026. *(high)*

The genuinely hard and under-solved step is **raster-to-topology**: converting a probability mask into closed, non-overlapping, gap-free, vertex-shared polygons that satisfy cadastral topology rules. Segmentation output is ragged; cadastral parcels are legally required to tile the plane exactly. Bridging that gap — polygon regularisation, boundary snapping, sliver removal, gap closure, shared-edge enforcement — is where the actual research problem lives, and it is where almost every team will hand-wave. *(high)*

Make topology your headline claim, not segmentation. This single reframing is probably worth more to your score than any model choice.

Secondary novelty: using the DSM–DTM difference (normalised DSM) as a hard geometric prior. A building is an object with height; a painted courtyard is not. Most image-only pipelines confuse these constantly. Explicitly fusing height into the segmentation is straightforward and demonstrably improves results.

### Complexity
High, and legibly so. Multi-modal fusion (optical + height), instance segmentation, vectorisation, topological repair, and an editing interface with persistence. This is a lot of real engineering.

### Feasibility
The data question is the risk. Indian urban drone ORI at SVAMITVA-grade resolution is not openly downloadable. *(medium-high confidence — verify whether the PS attaches a dataset.)*

Workable substitutes: the Inria Aerial Image Labeling dataset, SpaceNet, OpenAerialMap, the Dutch AHN LiDAR-plus-imagery combination for a genuine DSM/DTM pair, and ISPRS Vaihingen/Potsdam which give you exactly the optical + nDSM + labels combination this PS describes. Vaihingen in particular is almost purpose-built for this. *(high)*

Say clearly in the deck that you validated on ISPRS-style data and that the pipeline ingests any ORI+DSM+DTM triple. That converts a data gap into an architecture claim.

### Scale of impact
Very high. SVAMITVA-type property-card programmes cover hundreds of thousands of villages, and urban cadastral backlog is a documented national bottleneck. Easy impact narrative.

### User experience
Judges here will be land-records people. They will look for the human-in-the-loop path: confidence flagging on uncertain boundaries, a surveyor review queue, an edit trail. A fully-automatic system that offers no correction workflow will read as naive to anyone who has worked with land records, because in that domain the machine output is a *draft*, never a record.

### Future work
Change detection between drone survey epochs to detect encroachment and unauthorised construction is the obvious and strong extension.

### Failure mode
Beautiful segmentation masks, no vectors. The demo shows coloured overlays and the judge asks to see the shapefile, or asks whether two adjacent parcels share an edge. If you cannot export a topologically valid polygon layer, you have built an image-processing demo, not a cadastral system.

---

## 4. PS 26011 — 3D ULPIN Generation and Vertical Property Mapping System
**Theme:** Smart Automation · Land governance

### What it actually asks for
Extend the ULPIN concept from a 2D parcel identifier into a 3D identifier that can uniquely name an apartment on the seventh floor, a basement parking bay, a metro tunnel segment, and the air rights above a plot — then map ownership to those volumes.

### Novelty
This is the highest-novelty item in the land-governance cluster, by a clear margin. 3D cadastre is an active international research area with a formal standards basis (ISO 19152 LADM, and the 3D extension work), and India has essentially no operational deployment. *(medium-high — I'm confident 3D cadastre is a live research area and that LADM is the relevant standard; less confident about the exact status of Indian pilots. Worth a search.)*

The specific novelty you can claim: an identifier *scheme*. How do you construct a 3D ULPIN that is hierarchical, human-parseable, collision-free, stable under building modification, and derivable from geometry? That is a design problem with real intellectual content, and it is rare for a hackathon team to produce genuine schema design rather than another CRUD app.

### Complexity
High, but note that the complexity is *conceptual* more than computational. Volumetric topology validation — do these apartment volumes tile the building envelope without overlap or void? — is a real geometric problem. Floor segmentation from point clouds is a real perception problem. Reconciling a floor plan (2D, local coordinates, often a PDF) with a georeferenced point cloud is a real registration problem.

### Feasibility
Medium, with a specific bottleneck: you need building-interior data. Point clouds of building *exteriors* are available (open LiDAR from several international cities, and photogrammetric point clouds you can generate yourself). Floor plans registered to those exteriors are not.

The honest scope reduction: demonstrate the full pipeline on one building where you construct the floor plan yourself, and make the *framework* the deliverable rather than the coverage. State this as a deliberate scoping decision.

### Scale of impact
Very high in principle — every apartment in urban India is an unaddressed case in the current system, and apartment ownership disputes are extremely common. The argument writes itself.

### User experience
This PS has a genuine 3D visualisation opportunity, and 3D demos are memorable in a way dashboards are not. A clickable building where selecting a unit surfaces its 3D ULPIN and ownership record is a strong finale moment. Use CesiumJS or deck.gl. *(high — both handle 3D tiles and georeferenced geometry well)*

### Future work
Utility networks and underground infrastructure as first-class 3D parcels, and linkage to building permission workflows.

### Failure mode
The team builds a 3D viewer and calls it a cadastre. The distinguishing question a sharp judge asks is: "if the owner of flat 702 sells to someone, what changes in your database, and what stops flat 703's volume from overlapping it?" If the answer is only visual, you have built a rendering, not a cadastral system. Volumetric topology validation is the substance here.

---

## 5. PS 26014 — Integrated GIS-Based Digital Public Infrastructure for Land Governance (Land Stack)
**Theme:** Agriculture, FoodTech & Rural Development · Department of Land Resources

### What it actually asks for
A parcel-centric integration layer. Cadastral base layer keyed by ULPIN, essential governance layers (Record of Rights, registration, master plan, building permissions, encumbrance, zoning) linked to each parcel, and extension layers (utilities, tax, valuation, environmental restrictions). Plus open APIs, standardised metadata, RBAC, audit trails, citizen-facing services, and a formal technical standards document.

### Novelty
Low on technique, high on architecture. There is no algorithm here. The PS is asking for interoperability design across heterogeneous state systems, and it explicitly names the hard part: land is a State subject, so record formats, field schemas, measurement units, languages, and terminology differ across every state.

Your novelty claim, if you take this, is a **schema harmonisation layer** — a canonical land-record data model with per-state adapters, and ideally a semi-automatic mapping tool that ingests an unseen state's record format and proposes a field mapping. That is genuine and it directly answers the PS's stated central difficulty. *(medium-high)*

### Complexity
Engineering-heavy, algorithm-light. Many integrations, many roles, many workflows, careful API design. Complexity scores on breadth rather than depth, which some judges reward and others do not.

Note the explicit deliverable of a **Standard Technical Document** covering API standards, data schemas, security framework, GIS standards, and UI/UX guidelines. This is unusual and it tells you what the sponsoring department actually wants: a specification as much as a prototype. Teams that produce only code will be under-delivering against a stated requirement. That is also an opportunity, because most teams will skim past it.

### Feasibility
High for a prototype, because the PS explicitly permits mock or sample datasets. Bhu-Naksha and state land-record portals give you realistic schema examples to build adapters against.

The trap is scope. This PS lists roughly fifteen integrations. You cannot build fifteen. Build three deeply — cadastral base, RoR, and one extension layer — and make the fourth demonstrably pluggable via a documented adapter interface. Depth on the integration *mechanism* beats shallow coverage.

### Scale of impact
Maximal on paper — national land governance infrastructure. The impact section writes itself, which also means every competing team will write it identically. Impact is not where you differentiate on this PS.

### User experience
Two very different personas: a tehsildar or registrar with a complex workflow, and a citizen who wants to check who owns a plot. Both matter and they need genuinely separate interfaces. The citizen path should be extremely simple — search, view, verify, request.

### Future work
Nationwide adapter library, and the DPI framing (consent layer, integration with other India Stack components) is what the department will want to hear.

### Failure mode
It becomes a generic admin dashboard that any web team could build, with a map tab. The geospatial and interoperability substance evaporates under time pressure, and the demo is indistinguishable from a hundred others. If you pick this, the harmonisation layer must be built early and demoed first.

---

## 6. PS 26016 — Real-Time National Land Acquisition and Management System
**Theme:** Smart Automation · Land acquisition / infrastructure ministry

### What it actually asks for
Workflow digitisation of the land acquisition lifecycle, from project proposal to final possession, across central ministries, state governments, district authorities, and implementing agencies. Geo-tagged parcels on a map, status tracking, compensation and R&R monitoring, document repository, MIS dashboards.

### Novelty
Lowest of the cluster, and I would be honest with yourselves about this. It is a workflow management system with geo-tagging. The PS text does not ask for a single non-trivial algorithm. *(high)*

If you take it, novelty has to be manufactured, and the two credible sources are:
- **Automated parcel reconciliation** — matching a project alignment corridor against cadastral layers to auto-generate the affected-parcel list with areas, rather than having officers enter it manually. This is real geoprocessing and it is the single most laborious step in actual acquisition practice.
- **Compensation anomaly detection** — flagging awards that deviate substantially from comparable parcels, as a transparency instrument.

Both are additions you make, not things the PS asked for. That is fine and it is how you differentiate on a low-novelty PS.

### Complexity
Low-to-moderate on the stated requirements. Multi-tier RBAC across four government levels with delegation and audit is genuinely fiddly, and doing it properly is more work than it looks. But it is not intellectually difficult.

### Feasibility
Highest in your shortlist, alongside 26019. Everything is buildable with standard web tooling and mock data. A polished, complete, working system is very achievable in the finale window.

There is a strategic argument for this: a *complete, polished, bug-free* system on an easy PS often outscores a *broken, ambitious* system on a hard one, because clarity and practicability are scored criteria and a working demo dominates a promised one. Whether that trade suits your team depends on what you want out of the event.

### Scale of impact
High and concrete. Land acquisition delays are one of the most-cited causes of infrastructure cost overruns in India, and the numbers are publicly documented. Good impact narrative with real citations available.

### User experience
This is where you win this PS. Government workflow tools are notoriously bad, and a genuinely well-designed one stands out immediately. Invest disproportionately in interaction design, form ergonomics, and dashboard legibility. Mobile field verification is explicitly requested and is a strong demo element.

### Future work
Natural bridge to PS 26017 (delay prediction) as an analytics layer on the same data model. If your team is split, these two are the most complementary pair in the shortlist.

### Failure mode
Nothing dramatic. It just doesn't stand out. The risk on this PS is not collapse, it is anonymity.

---

## 7. PS 26017 — Predictive Analytics for Early Detection of Land Acquisition Delays
**Theme:** Smart Automation

### What it actually asks for
Risk-score each land acquisition project for probability of delay, identify the driving factors, explain the prediction, and recommend mitigations. Dashboards by district and state, GIS visualisation of high-risk projects, alerts, and model retraining on new data.

### Novelty
Moderate. Applying survival analysis or gradient-boosted classification to administrative project data is not new in general, but it is genuinely under-applied to Indian land acquisition. The PS explicitly requires **explainable AI**, which is a useful hook: SHAP or a similar attribution method on a delay model is a strong, visual, judge-friendly demo. *(high)*

Stronger novelty angle: **stage-wise hazard modelling**. Delay is not one event; a project can stall at notification, at award, at compensation disbursement, at possession, at R&R. A survival model with competing risks that predicts *where* in the pipeline a project will stall, and its expected time-to-stage, is considerably more sophisticated than a binary delayed/not-delayed classifier, and more operationally useful. *(medium-high)*

### Complexity
Moderate. The modelling is standard; the framing choice above is what raises it. Time-to-event data with censoring (projects still in progress) is a real subtlety that most teams will get wrong by treating in-progress projects as "not delayed."

### Feasibility
**This is the problem with this PS, and it is serious.** There is no public dataset of Indian land acquisition project timelines with delay causes. You will be generating synthetic training data. *(high confidence)*

A model trained on data you invented, validated against data you invented, achieving accuracy you designed into it, is circular. A sharp judge will find this in one question. You have two defences:

1. Generate the synthetic data from a **documented causal structure** derived from real sources — CAG reports on infrastructure delays, PMG/PRAGATI project monitoring disclosures, NHAI annual reports, published academic work on acquisition delays. Then state openly that the model is validated on a simulator whose parameters come from documented evidence, and that the contribution is the framework, not the coefficients.
2. Anchor part of the system in **real** data — for example, use actual district-level litigation load or actual notified-vs-acquired area figures where they are published.

Do not present synthetic-data accuracy figures as though they were real performance. It is the fastest way to lose credibility with a technical judge.

### Scale of impact
High if the model is real. Currently theoretical.

### User experience
Explainability is the UX. A risk score with no reason is useless to an administrator. Every flagged project should surface its top contributing factors in plain language and a suggested action.

### Future work
Coupling to real MIS data once a system like 26016 exists. Say this explicitly — it shows systems thinking.

### Failure mode
"What data did you train on?" "We generated it." "So what does 87% accuracy mean?" There is no good improvised answer to that. Prepare the answer in advance or pick differently.

---

## 8. PS 26018 — Intelligent Land Record Digitization and Validation System
**Theme:** Smart Automation

### What it actually asks for
OCR and information extraction from scanned, handwritten, degraded, multilingual legacy land records. Extract structured fields (survey number, khasra, khata, owner, area, village, tehsil, land classification, mutation, registration), classify them, validate against business rules and cross-database checks, score confidence, and route low-confidence records to human review.

### Novelty
Moderate to high, and specifically because of **Indic handwritten text recognition**. Printed English OCR is solved. Handwritten Devanagari, Gujarati, Odia, Telugu, and Bengali on degraded 1950s-era registers is emphatically not solved. This is an open research problem with real difficulty. *(high)*

The differentiating contributions available:
- A **confidence-calibrated** extraction pipeline where the confidence score is actually meaningful — that is, where records flagged at 60% confidence genuinely fail more often than those at 90%. Most systems output softmax scores that are badly miscalibrated. Doing calibration properly (temperature scaling, or reliability diagrams in the deck) is unusual and rigorous.
- **Rule-based cross-validation** as an error detector: areas of sub-parcels should sum to the parent, khasra numbers should exist in the village register, mutation chains should be temporally consistent. Using domain constraints to catch OCR errors is smarter than trying to make OCR perfect, and it is the approach a working system would actually take.

### Complexity
High. Layout analysis on non-standard tabular registers, multilingual handwriting recognition, named-entity extraction from noisy text, validation logic, human-in-the-loop workflow. Genuinely difficult and legibly so.

### Feasibility
Medium and it depends on data. IIIT-Hyderabad and other groups have released Indic handwriting datasets; IAM and RIMES exist for the Latin-script baseline. Land record scans are available in varying quality from state portals. *(medium — verify what's actually downloadable, since state portal access varies a lot.)*

Pragmatic path: fine-tune an existing multilingual OCR backbone rather than training from scratch, and spend your effort on layout parsing, validation, and calibration. That allocation of effort is also more defensible in a presentation.

### Scale of impact
Very high. Legacy record digitisation is a stated national priority under DILRMP and the backlog is enormous. Easy to argue.

### User experience
The verification queue is the product. Design it for a data-entry operator: field-level confidence highlighting, side-by-side scan and extracted form, single-keystroke accept/correct, and measurable throughput. If you can show "this reduces per-record handling time from four minutes to forty seconds," that is a better claim than any accuracy number.

### Future work
Active learning from operator corrections — the system improves from the review queue. The PS asks for this and it is a clean story.

### Failure mode
The demo works on clean printed samples and visibly breaks on the first genuinely degraded handwritten page. Judges will test this. Curate a demo set that includes hard cases *and show the system flagging them as low-confidence* — graceful failure on hard inputs is a feature, and demonstrating it deliberately is far stronger than being caught out.

---

## 9. PS 26019 — National Digital Platform for Research, Policy Innovation and Evidence-Based Land Governance
**Theme:** Smart Automation

### What it actually asks for
A knowledge platform: repository of research papers, policy documents, datasets, legal texts and case studies; AI-powered search and recommendation; collaborative workspaces; GIS visualisation of land use and policy impact; analytics and policy simulation; an innovation portal for hackathons and grants.

### Novelty
Lowest in the shortlist. This is a document repository with semantic search and dashboards. Every component is off-the-shelf. *(high)*

The single element with genuine content is the **policy simulation module** — assessing the likely outcomes of a proposed reform before implementation. If you actually build a simulation (agent-based model of land transactions, or a spatial model of a zoning change's effect on land use), you have something. If it is a dropdown that displays precomputed scenarios, you have nothing.

I would only recommend this PS if your team specifically wants to build the simulation component and treat the repository as scaffolding around it.

### Complexity
Low, unless you make the simulation real. RAG over a document corpus is a weekend project in 2026 and judges know it.

### Feasibility
Very high. Corpora are readily assembled from public sources.

### Scale of impact
Diffuse. "Better-informed policymaking" is real but unmeasurable, and impact arguments that cannot be quantified score weakly against ones that can.

### User experience
Researcher-facing. Search quality is everything; nothing else compensates for bad retrieval.

### Future work
Broad but unfocused.

### Failure mode
It becomes "we built a chatbot over some PDFs." Given how commoditised RAG has become, this is a hard PS to look impressive on. I would rank it last unless the simulation angle genuinely appeals to someone on your team.

---

## 10. PS 26053 — Adaptive Variable-Resolution 2.5D LiDAR Mapping for Dynamic Environment Perception
**Theme:** Smart Vehicles

### What it actually asks for
Take raw LiDAR point clouds, semantically segment them into drivable terrain, static obstacles, and dynamic objects, then project into a **non-uniform** 2.5D elevation grid where cell size grows with range — roughly 5 cm cells within 10 m, coarsening to 50 cm out to 100 m. Demonstrate real-time performance and a large memory reduction against a uniform high-resolution representation.

### Novelty
Moderate-to-high, and unusually well-specified. Foveated and multi-resolution mapping exists in robotics literature (multi-resolution occupancy grids, OctoMap's hierarchical structure), so the concept is not new. What is less explored is the *learned* combination — semantic segmentation feeding a variable-resolution elevation representation with explicit alignment guarantees. *(medium — I'm confident about OctoMap and multi-resolution grids; less confident about how much prior work exists on exactly this learned-semantic + adaptive-2.5D combination. Worth a literature check on arXiv before committing, because the answer changes your novelty claim.)*

The PS also names the real technical crux itself: **alignment errors and data loss during 3D-to-2.5D projection under non-uniform resolution**. When adjacent cells have different sizes, boundary handling, elevation aggregation, and semantic label propagation all become non-trivial. Whoever solves that cleanly has the contribution.

### Complexity
High and honest. Point cloud deep learning (PointNet++ or sparse convolution as the PS suggests), a custom spatial data structure, real-time constraints, and a quantitative memory/latency evaluation. This is a genuine systems-plus-ML problem.

### Feasibility
**Excellent, and this is the standout feasibility case in your shortlist.** SemanticKITTI provides point-wise semantic labels on real automotive LiDAR sequences. nuScenes and Waymo Open give more. Sparse convolution libraries (MinkowskiEngine, spconv, or torchsparse) are mature. Pretrained segmentation backbones exist. *(high)*

You can have a working baseline within days of starting, which leaves the whole build window for the adaptive-grid contribution — the actual novel part. That is a very healthy project shape and rare among these twenty.

### Scale of impact
Narrower than the governance PSs, and framed toward autonomous vehicles, which is a smaller Indian near-term market. But the argument generalises to any robotics platform with a LiDAR — agricultural machinery, mining haul trucks, warehouse robots, drone navigation. Make that generalisation explicitly rather than pitching only to cars.

### User experience
Least UX-weighted PS here. The "user" is a downstream planning module. Your interface is a real-time visualisation, and it should be genuinely good — an animated 2.5D map with a live memory-and-FPS counter comparing against the uniform-grid baseline is a compelling and immediately legible demo.

### Future work
Temporal fusion across frames, uncertainty-aware cell aggregation, and deployment on embedded hardware (Jetson) are all natural.

### Failure mode
Semantic segmentation gets built, the adaptive grid does not, and the demo is a segmentation viewer. The segmentation is the *easy, already-solved* part and it is not what the PS is asking for. Build a naive adaptive grid on ground-truth labels first, before touching the network, so the novel component exists from day one.

---

## 11. PS 26066 — OceanEmbed: Satellite Embedding-Based Reconstruction of Subsurface Ocean Temperature
**Theme:** Space Technology · Reads like INCOIS or ISRO/SAC

### What it actually asks for
Learn a mapping from daily surface satellite fields (SST, SSS, SSH/SLA, surface currents U/V, surface winds U/V) at 0.25° resolution to the full vertical temperature profile at fifteen standard depths from 0 m to 1000 m, over the North Indian Ocean (5°N–30°N, 45°E–105°E). Train against GLORYS reanalysis, validate against gridded ARGO.

### Novelty
Moderate. Subsurface reconstruction from surface fields is an established research line — there is a decade of published work using random forests, neural networks, and more recently transformers on exactly this problem, including for the Indian Ocean. *(medium-high confidence that substantial prior work exists; I'd search "subsurface temperature reconstruction deep learning Indian Ocean" before finalising your novelty claim, because you need to know what you're differentiating from.)*

The PS's own novelty proposal is in the word **embeddings** — learning a compact latent representation of surface ocean state rather than regressing directly. That framing has real content: a shared latent space could transfer across basins, support multiple downstream variables (salinity, mixed-layer depth), and expose interpretable structure corresponding to physical regimes (eddies, upwelling, thermocline states).

The strongest defensible contribution: **physics-informed constraints on the reconstruction**. Temperature profiles are not arbitrary vectors — they are near-monotonic below the mixed layer, stratification must be stable, and the thermocline has characteristic shape. A model that enforces these as architectural or loss constraints, rather than treating fifteen depths as fifteen independent regressions, is both more physical and more novel. *(medium-high)*

### Complexity
High, mostly in data engineering. Multi-source harmonisation to a common 0.25°/daily grid, land masking, gap handling, temporal alignment, and a spatiotemporal model that respects geographic structure. The ML itself is a supervised regression once the data is ready.

### Feasibility
This is the one to think hardest about, because the risk is not conceptual, it is **volume and time**.

GLORYS reanalysis at 1/12° daily with depth levels over that domain is a very large download, and Copernicus Marine access requires registration and their toolbox. ARGO gridded products from INCOIS LAS need separate handling. Getting all of it downloaded, regridded, aligned, and masked is likely to consume a substantial fraction of your total effort before you train anything. *(medium-high confidence on the volume issue; verify actual download sizes for your exact domain and time window before committing.)*

Mitigation, and I would treat this as mandatory: **do the entire data pipeline before the finale**. Arrive with a cleaned, cached, analysis-ready tensor. Also start with a deliberately reduced scope — one year, the Bay of Bengal only, five depth levels — get end-to-end results, then expand. Teams that try to build the full-domain pipeline during the event will spend the whole window on netCDF.

### Scale of impact
Genuinely high and specific: marine heatwave monitoring, fisheries advisories, cyclone intensity forecasting (which depends critically on upper-ocean heat content in the Bay of Bengal), and data assimilation. The cyclone link is the most vivid argument for an Indian panel.

### User experience
Scientific users. The valuable interface elements are a depth-slider map, an interactive profile plot at a clicked location with the ARGO observation overlaid for comparison, and per-depth skill metrics. Showing your errors honestly, per depth, is more persuasive to a scientific panel than a headline RMSE.

### Future work
Extension to salinity, to other basins, and to forecast rather than nowcast. Also the transfer-learning story the embedding framing sets up.

### Failure mode
Two distinct ones. First, the pipeline eats the event and there is no model. Second, subtler and more damaging: the model performs well at 5 m and poorly at 500 m, because surface fields carry little information about the deep ocean, and the team reports only the aggregate RMSE. A scientific judge will ask for the depth-wise breakdown immediately. Report it yourself, and discuss *why* skill degrades with depth — that turns a weakness into evidence of understanding.

---

## 12. PS 26083 — Extreme Heatwave Early Warning and Human Thermal Stress Index
**Theme:** Disaster Management

### What it actually asks for
Move heat warning from ambient temperature to physiological impact. Compute WBGT, UTCI, or Heat Index; link those to a mortality risk index using health, demographic, and weather data; forecast 3–5 days ahead at ward level; deliver colour-coded GIS alerts and push notifications; provide an API that triggers heat action plan measures.

### Novelty
Split. The thermal indices themselves are **published formulas** — WBGT, UTCI, and the Rothfusz Heat Index are deterministic calculations from temperature, humidity, wind, and radiation. Implementing them is arithmetic, not innovation, and presenting index computation as your contribution will be seen through immediately. *(high)*

The novelty must live in one of three places:

- **The exposure–response link.** Estimating a local relationship between thermal stress and excess mortality or hospitalisation is real epidemiological modelling (distributed lag non-linear models are the standard tool in this literature) and it is genuinely hard. *(medium — I'm confident DLNMs are the standard approach; less confident about Indian data availability to fit one.)*
- **Downscaling.** IMD forecasts are coarse; the PS asks for ward-level. Statistically downscaling to intra-urban resolution using land surface temperature, built-up fraction, NDVI, sky view factor, and building density is genuine geospatial work and plays directly to your team's strengths. This is probably your best angle.
- **Vulnerability weighting.** The same WBGT is not equally dangerous in a ward with high elderly density, high outdoor-worker share, low tree cover, and poor housing. A composite vulnerability surface layered on the hazard surface is the difference between a weather product and a public health product.

### Complexity
Moderate. Index computation trivial, downscaling moderate, exposure–response modelling hard. Your complexity score depends entirely on which of these you actually build.

### Feasibility
Weather data is easy — IMD, ERA5 for reanalysis and historical training, and open forecast APIs. Demographic data from Census 2011 at ward level is available though dated. Land surface temperature from Landsat and MODIS is straightforward.

**Mortality data is the blocker.** Indian all-cause daily mortality at city-ward resolution is not publicly available. *(medium-high confidence — verify; some municipal corporations publish civil registration summaries, and there is published academic work on Ahmedabad and Delhi heat mortality that may include usable aggregates.)*

Fallback: fit the exposure–response function from published literature coefficients rather than raw data, and be explicit that you are transferring published relationships. That is a legitimate and standard epidemiological practice, and stating it clearly is far better than obscuring it.

### Scale of impact
Very high and increasingly urgent. Indian heat mortality is a rising, well-documented problem, and city heat action plans are actively being written. The Ahmedabad Heat Action Plan is the standard reference and is worth citing.

### User experience
Two audiences with opposite needs. Municipal commissioners need a decision dashboard with clear trigger thresholds mapped to specific actions (open cooling centres, shift work hours, alert hospitals). The public needs a single colour and one sentence. Do not give the public a UTCI number — nobody knows what 38 UTCI means. "Dangerous today: avoid outdoor work between 11 and 4" is the product.

### Future work
Coupling to power grid demand forecasting, and to urban greening and cool-roof planning as a mitigation-planning tool.

### Failure mode
The system is a weather app with extra formulas. If a judge can summarise your project as "you computed heat index and put it on a map," you have not differentiated. The downscaling and vulnerability layers are what make it a distinct product.

---

## 13. PS 26085 — Urban Flood Nowcasting System (Drainage and Rainfall Coupling)
**Theme:** Disaster Management

### What it actually asks for
Street-level inundation prediction with 0–3 hour lead time. Couple radar rainfall nowcasts with a high-resolution DEM for surface routing, and with a graph representation of the stormwater drainage network (manholes and inlets as nodes, pipes and canals as edges) to model hydraulic capacity and predict where surcharge pushes water onto streets. Output street-by-street depth estimates and an API for flood-safe routing.

### Novelty
High as specified, and this is the most technically interesting disaster PS in your list. Most urban flood projects do one of two things: pure hydrodynamic modelling (accurate, far too slow for nowcasting) or pure ML on historical flood reports (fast, no physics, fails on unseen rainfall). The PS asks for a **coupled surface-plus-network** model, which sits in the useful middle and is genuinely less common. *(medium-high)*

The strongest contribution available: a **graph neural network over the drainage network** as a fast surrogate for hydraulic simulation. Train it against SWMM or similar hydrodynamic model outputs, then run inference in seconds instead of hours. Physics-informed surrogate modelling for urban drainage is an active research area with real headroom. *(medium — I'm confident about surrogate modelling generally and about SWMM; less confident about how much specific GNN-for-drainage work already exists. Check this.)*

### Complexity
Genuinely high. Radar nowcasting (optical flow or a ConvLSTM/DGMR-style model), 2D surface flow routing, graph hydraulics, and coupling between the surface and subsurface domains. This is the most demanding coupling problem in the shortlist.

### Feasibility
**Here is the blocker, and it is severe: the drainage network data does not exist publicly.** Municipal stormwater network geometry — pipe diameters, invert levels, slopes, connectivity — is not published by Indian municipal corporations, and in many cases is not digitised at all. *(high confidence)*

This is not a minor gap. It is the input the entire PS is built around.

Your options:
1. **Synthesise a plausible network** from street centrelines and DEM-derived flow direction, with diameters assigned by contributing catchment area using standard design formulas. Defensible, and arguably itself a contribution — "automated drainage network inference where records are unavailable" is a real and useful capability given that most Indian cities are in exactly this situation. This is the angle I'd push.
2. **Use an international city with open data.** Several European and US cities publish full stormwater GIS. Build and validate there, argue transferability.
3. **Check for a specific released dataset.** *(verify on the portal — if the sponsoring body attaches a real network for one city, this PS becomes dramatically more attractive.)*

DEMs are the other constraint. SRTM at 30 m is useless for street-level flooding. You need 1–5 m. Options are limited in India; check whether any city has published LiDAR or high-resolution photogrammetric DEM. *(medium — worth searching, some smart-city programmes have.)*

### Scale of impact
Very high, very vivid, and immediately legible to any Indian judge. Mumbai, Chennai, and Bengaluru flooding are annual national news.

### User experience
The routing API is the killer feature. A map showing flooded street segments with predicted depth, and a rerouted ambulance path around them, is a demo that lands instantly and needs no explanation.

### Future work
Assimilation of crowdsourced flood reports and CCTV-based water level estimation to correct the model in real time.

### Failure mode
No drainage data, so the team quietly drops the network component and ships a DEM-based sink-filling model — which is just terrain analysis and predicts flooding in the same low spots regardless of rainfall or drainage state. The network coupling *is* the PS. If you cannot solve the data problem, do not pick this one.

---

## 14. PS 26142 — Deep Learning Based Super-Resolution Mapping from Medium-Resolution Satellite Imagery
**Theme:** Space Technology · ISRO-linked

### What it actually asks for
Take 10 m Sentinel-2 imagery and produce outputs below 4 m while preserving geospatial and spectral consistency. Include preprocessing, paired-data training, accuracy assessment, validation against high-resolution references, and explicit handling of uncertainty — the PS is unusually direct that some reconstructed detail is *inferred, not observed*.

### Novelty
Low on the core method, and you should go in knowing that. Satellite image super-resolution is one of the most crowded topics in remote sensing deep learning. There are dozens of papers, public benchmarks, and pretrained models. A team presenting an ESRGAN or a diffusion model on Sentinel-2 is presenting a reimplementation. *(high)*

But read the PS text again — it hands you the differentiator explicitly: **"it must clearly manage uncertainty because some reconstructed details are inferred by the model and not directly observed."** The problem-setter is telling you what they care about.

Almost nobody does this properly. Generative super-resolution hallucinates plausible texture, and if a user then digitises a "building" that the model invented, the consequences are real. A system that outputs **per-pixel uncertainty alongside the enhanced image** — via ensembles, MC dropout, or a diffusion model's sample variance — and that visually distinguishes reconstructed-and-reliable from reconstructed-and-speculative regions, is doing something the field genuinely under-serves. *(high confidence that this is under-addressed)*

Second angle: **spectral fidelity**. GANs optimised for perceptual quality routinely distort band relationships, which destroys downstream index computation. Showing that your NDVI/NDWI values survive super-resolution, with quantitative before-and-after comparison against the reference, is a rigorous and unusual evaluation. Most teams will report PSNR and SSIM, which are the wrong metrics for a scientific product.

### Complexity
Moderate on implementation, high if you take uncertainty seriously. Training a super-resolution network is well-trodden. Calibrated uncertainty estimation is not.

### Feasibility
High. Sentinel-2 is freely available. For high-resolution pairs: PlanetScope has an education/research programme, WorldView samples exist, and NAIP gives 0.6 m aerial imagery over the US with an established Sentinel-2 pairing workflow used in published work. Co-registration between the pair is the fiddly part and you should budget real time for it. *(medium-high)*

The dataset construction — genuinely co-registered, radiometrically comparable, temporally close pairs — is where the quality of your result is determined. More than the architecture.

### Scale of impact
Broad but indirect. It is an enabling technology, so the impact argument runs through downstream applications (crop boundary delineation, damage assessment, urban mapping). Demonstrate at least one downstream task improving with your outputs; that converts an abstract claim into a measured one.

### User experience
Side-by-side sliders are the obvious demo and everyone will do them. Differentiate with an **uncertainty overlay toggle** — the same enhanced image, with speculative regions visibly marked. That is memorable and it directly demonstrates your novelty claim.

### Future work
Multi-temporal super-resolution using a Sentinel-2 time stack rather than a single image, which is more physically grounded because it uses genuinely additional observations rather than learned priors alone.

### Failure mode
The model produces sharp, beautiful, wrong images, and a judge zooms in and asks "is that building real?" If you cannot answer, the whole product is undermined. If your answer is "the uncertainty map flags that region as low confidence, and here's why," you have won the exchange.

---

## 15. PS 26143 — Satellite Oil Spill Detection with AIS Correlation for Vessel Attribution
**Theme:** Disaster Management · Reads like Indian Coast Guard or DG Shipping

### What it actually asks for
A three-stage pipeline. Detect and characterise oil slicks in SAR imagery, computing geometric properties and, if possible, age. Back-trace the slick using oceanographic and meteorological data to estimate origin point and time, and forward-predict drift. Then reconstruct vessel traffic from historical AIS around that origin window, filter irrelevant traffic, and score suspect vessels on proximity, trajectory, and behavioural anomalies.

### Novelty
Moderate-to-high, and structurally excellent. Each individual stage exists in the literature — SAR oil spill segmentation is well-studied, Lagrangian drift back-tracking is standard oceanography, AIS anomaly detection has its own literature. **The integration into an attribution chain is the contribution**, and it is a genuinely useful one because attribution is the actual bottleneck in marine pollution enforcement. *(medium-high)*

The most interesting technical piece is the **back-trace under uncertainty**. You are not looking for a point; you are computing a probability distribution over origin location and time, given uncertain currents, wind drift factors, and unknown slick age. That distribution is what you then intersect with AIS tracks. Framing attribution as a probabilistic spatiotemporal intersection, rather than a nearest-vessel lookup, is the sophisticated version and it is much more defensible in front of a judge who asks "how confident are you?"

Second piece: **look-alike discrimination**. Low-wind areas, biogenic films, algal blooms, and rain cells all produce dark patches in SAR that resemble oil. Distinguishing them requires wind field context and shape/texture analysis. This is the well-known hard part of SAR oil detection and addressing it explicitly signals domain knowledge. *(high)*

### Complexity
High and well-distributed across three genuinely different technical domains — computer vision, physical oceanography, and spatiotemporal data analysis. This reads as a serious multi-disciplinary system, which scores well.

### Feasibility
**Very good, and the PS itself points you at the data.** The Zenodo Sentinel-1 SAR oil spill dataset is named in the PS. MarineCadastre publishes AIS format and US data. Sentinel-1 is freely available via Copernicus. Currents from CMEMS or HYCOM, winds from ERA5. The PS explicitly permits synthetic AIS where real data is unavailable, which removes your biggest legal and access risk. *(high)*

This is one of the better-scoped PSs in your list: hard problem, named datasets, permitted synthetic fallback.

### Scale of impact
High and concrete. India's EEZ is roughly 2.3 million km², shipping traffic through the Arabian Sea and Bay of Bengal is heavy, and illegal bilge discharge is a real and largely unpunished problem. Attribution enables enforcement, and enforcement changes behaviour. That is a clean causal impact chain.

### User experience
Design for a Coast Guard watch officer. A ranked suspect list with evidence — the slick, the back-traced origin envelope, each candidate vessel's track through it, and the anomaly indicators that raised its score. Every ranking must be inspectable, because this output could support a legal action and an unexplainable accusation is worthless.

### Future work
Near-real-time operation on Sentinel-1 acquisitions, and extension to dark-vessel detection (ships visible in SAR but absent from AIS, which is itself a strong indicator of intent).

### Failure mode
Detection works, attribution is a straight-line nearest-neighbour search that names whichever ship happened to be closest. That is not attribution, it is proximity, and a judge with any domain familiarity will say so. The drift model is what makes this project real.

---

## 16. PS 26166 — Multi-Modal, Sun-Angle and Scale-Invariant Image Correspondence Using Chandrayaan-2 Optical Images
**Theme:** Space Technology · ISRO/SAC

### What it actually asks for
Register Chandrayaan-2 optical imagery (OHRC, TMC-2, IIRS) against lunar reference imagery (LRO NAC, SELENE) to sub-pixel accuracy, with uniformly distributed match points, under three simultaneous adversities: illumination variation from differing sun azimuth and elevation, viewpoint/geometric distortion, and large scale ratios between sensors of very different resolutions.

### Novelty
High, and this is the most scientifically well-posed problem in your shortlist. The evaluation criteria are unambiguous — RMSE, inlier count, inlier ratio — which means your result is objectively measurable rather than rhetorically argued. That is rare and valuable in a hackathon setting. *(high)*

The core difficulty is real and interesting. On the airless Moon, the same crater under low sun and high sun looks completely different, because appearance is dominated by shadow geometry rather than by albedo. Classical descriptors (SIFT, ORB) fail badly here, and this is a documented, well-known failure mode. *(high)*

Approaches with genuine headroom:
- **Shadow-invariant representation.** Convert both images to a domain where illumination is factored out — terrain-derived features, or a shape-from-shading normalisation, so you match geometry rather than appearance.
- **Learned local features.** SuperPoint plus SuperGlue, LoFTR, or DISK are dense/detector-free learned matchers that substantially outperform classical descriptors under appearance change. Fine-tuning one on lunar imagery is a strong, achievable, and defensible contribution. *(high confidence these are the relevant state of the art; medium confidence on how well they transfer to lunar imagery without adaptation — this is exactly the empirical question your project would answer, which is a good position to be in.)*
- **Match distribution enforcement.** The PS explicitly asks for uniform distribution across the image, which most matchers do not provide — they cluster matches on high-texture regions. Adaptive non-maximal suppression or grid-constrained selection addresses this directly and is a specific, checkable requirement that many teams will overlook.

### Complexity
High and focused. This is one deep problem rather than a broad system, which suits a team that would rather go deep than wide.

### Feasibility
Medium, gated on data access. ISSDC's Chandrayaan-2 browse portal and the LROC/QuickMap portals are named in the PS with links, so the data exists and is nominally accessible. The friction is in download mechanics, format handling (PDS labels), and the co-registration bookkeeping. *(medium — verify you can actually pull matched OHRC/TMC and LRO NAC scenes over the same area before committing. This is a same-day check and it should gate your decision.)*

Budget serious time for PDS data handling. It is not a familiar format and it will consume more of your schedule than you expect.

### Scale of impact
Narrow in population terms, high in strategic terms. This directly serves Indian planetary science, Chandrayaan data exploitation, and future lunar landing site selection and navigation. For an ISRO-affiliated panel, this framing matters more than user numbers.

### User experience
Least UX-driven PS in the list, and that is fine — the users are ISRO scientists. Deliver a clean tool: load source and reference, run, inspect match overlay, view the error distribution, export the registered product and match point file. A match-point distribution visualisation with an RMSE readout is exactly what this audience wants to see.

### Future work
Extension to IIRS hyperspectral registration, to Chandrayaan-3 data, and to automated global mosaic generation.

### Failure mode
The team runs SIFT, gets a handful of matches under mild illumination difference, and shows that. The evaluation set will contain hard pairs — large sun-angle differences and large scale ratios — and classical methods will collapse on them. Test against genuinely hard pairs early so you find out on day two rather than at the demo.

---

## 17. PS 26167 — SatQuery AI: Interactive Vision-Language Assistant for Multimodal Remote Sensing Analysis
**Theme:** Space Technology · ISRO/SAC

### What it actually asks for
An agentic vision-language system over remote sensing imagery. Mandatory capabilities: single-image VQA, plus one of captioning or text-guided grounding; bi-temporal change description or change VQA; joint optical–SAR analysis; and an agentic controller that classifies the query, validates inputs, selects specialist models from a registry, executes them, integrates outputs, estimates confidence, and produces an auditable execution trace. The PS states explicitly that a generic VLM without remote-sensing adaptation will not satisfy the requirements.

### Novelty
Moderate. Remote sensing VLMs are an active area with existing work (RSGPT, GeoChat, EarthGPT and similar), and agentic orchestration frameworks are commodity in 2026. *(medium-high — I'm confident RS-specific VLMs exist and are published; less confident about the current state of the art as of mid-2026, which is worth a search.)*

Where headroom exists:
- **Cross-modal optical–SAR joint reasoning.** This is the least-served capability in the list. Most RS VLMs are optical-only. A model that genuinely reasons over a co-registered optical–SAR pair, rather than captioning each separately and concatenating, is a real contribution.
- **The auditable execution trace.** The PS explicitly says only the observable trace is evaluated, not internal reasoning. That is a strong hint about what they want: verifiable tool selection, not a chain-of-thought performance. Building a clean, inspectable execution record is both easy and directly aligned with the stated evaluation.
- **Evidence grounding.** Answers accompanied by the specific image region supporting them.

### Complexity
High, and heavy on compute rather than on ideas. Fine-tuning even a modest VLM requires real GPU time. Orchestrating multiple specialist models requires them all to exist and run.

### Feasibility
Medium, with two specific concerns.

First, **compute**. The PS requires fine-tuning or domain adaptation of at least one vision-language component. On BigEarthNet-scale data this is not a laptop task. Plan for cloud GPU access, and prefer parameter-efficient adaptation (LoRA on a frozen backbone) over full fine-tuning. *(high)*

Second, and worth flagging: **the arXiv identifier given in your sheet for BigEarthNet (2603.29630) does not correspond to the real BigEarthNet paper**, which is arXiv 1902.06148 with the BigEarthNet-MM/v2 follow-up work. The listed ID appears to be a transcription error in the PS attachment. Verify the actual dataset link on the portal before building against it. *(high confidence that 1902.06148 is the correct original reference; medium confidence about what the PS intended by the other ID.)* Small detail, but if the dataset link is broken it is better to know now.

Benchmarks named — VRSBench, RSVQA, CDVQA — are public and gettable, which helps.

### Scale of impact
High as an accessibility argument: it lets non-experts query satellite imagery without GIS training. That is a real and growing need. The counter-argument a skeptical judge may raise is whether natural-language querying is genuinely how operational analysts want to work, versus whether it is a demo-friendly interface. Have an answer.

### User experience
The best UX opportunity in the shortlist. Conversational, multi-turn, with visual evidence returned alongside text. This will demo extremely well if it works. The execution summary panel — showing which models the agent selected and why — is both a required deliverable and a genuinely compelling interface element.

### Future work
Extension to more sensors, to time series rather than pairs, and to report generation.

### Failure mode
Two. First, the fine-tuning does not finish or does not help, and the system is a generic VLM with a prompt wrapper — which the PS explicitly rules out as insufficient. Second, the agentic layer is a set of if-statements dressed as an agent, and a judge asks what happens on a query outside your test set. Build the model adaptation early and the orchestration second; the orchestration is the part you can fake convincingly under time pressure, so it should not be the part you leave until the end.

---

## 18. PS 26168 — AI/ML-Based Intelligent Dead Reckoning for Seamless Navigation
**Theme:** Smart Vehicles

### What it actually asks for
Turn a smartphone into a navigation system that survives GNSS blackout. Automatic in-vehicle alignment (determining phone orientation relative to the vehicle), AI-based speed estimation from noisy IMU alone with no OBD-II connection, vibration and shock filtering, map-matching with non-holonomic constraints against OpenStreetMap, an AI-augmented GNSS+INS fusion engine, seamless millisecond transition between modes, and a live navigation UI. Deliverable is both a mobile app and an edge-deployable engine.

### Novelty
Moderate-to-high, and unusually *sharp* because the PS gives you a hard quantitative target: **positional drift under 10% of distance travelled**, with worked examples (under 5 m over 50 m, under 100 m over 1 km at 60 km/h). Explicit numeric acceptance criteria are rare in SIH problem statements and they are a gift — you know exactly what success means, and so do the judges. *(high)*

Learned inertial odometry is an established research line (IONet, RoNIN, TLIO and successors), so the concept is not new. The novel specifics here are the *smartphone, unmounted-orientation, vehicle-scale* combination — most published inertial odometry work is pedestrian-scale or uses well-mounted, well-calibrated IMUs. Vehicle dynamics with an arbitrarily oriented phone in a holder, subject to engine harmonics and pothole shocks, is a harder and less-solved setting. *(medium-high)*

The strongest technical piece: **the alignment engine**. Estimating phone-to-vehicle orientation online, and re-estimating it when the phone shifts in its mount, is a prerequisite for everything else and is genuinely non-trivial. If you nail this, the rest follows; if you don't, nothing works.

### Complexity
High and multi-layered: signal processing, sequence modelling, Kalman or factor-graph fusion, HMM map-matching, and mobile deployment with real-time constraints. Also a rare PS in this list where the *deployment* is part of the difficulty — an ONNX or TFLite model running at 10 Hz on a phone is a real engineering constraint, not an afterthought.

### Feasibility
Good, with a caveat. **IO-VNBD is named and available on GitHub**, and the PS requires you to submit preliminary results on it as part of the proposal — which means you must start modelling before submission, not after. Note that timeline. *(high)*

OSM is free, and map-matching libraries exist. The caveat is that the finale evaluation will use live phone sensors in a real or simulated GNSS-denied environment, so a model that works on IO-VNBD but not on *your* phone's IMU is a failure mode. Collect your own data on multiple phones, and treat cross-device generalisation as a first-class problem rather than an afterthought.

### Scale of impact
Very high and very legible. Every smartphone navigation user in India, every delivery rider, every ambulance in an urban canyon. Tunnels, flyovers, multi-level parking, and dense urban cores are universal experiences, and every judge in the room has personally watched their map freeze in one. That shared experience is worth a lot in a presentation.

### User experience
The demo is the product. A phone showing a vehicle icon moving smoothly through a tunnel while GPS is off, with the true path overlaid, is one of the most immediately convincing demos available across all twenty of these problem statements. Nothing needs explaining.

### Future work
Extension to two-wheelers (explicitly mentioned in the PS, and the dynamics differ substantially from cars — leaning, different vibration signature), and to pedestrian indoor navigation.

### Failure mode
Drift. The model works for fifteen seconds and then the position wanders off the road. This is the *expected* behaviour of inertial navigation and beating it is the whole problem. Instrument your drift-versus-time curve from the first week and treat it as the single metric that matters. Also: if the phone is picked up mid-run and your alignment estimate does not recover, the demo dies in front of the judges — build recovery in and then deliberately demonstrate it.

---

## 19. PS 26176 — ORCA: Marine Ecosystem Reasoning with Collaborative Agents
**Theme:** Space Technology / Marine · Reads like INCOIS or ISRO

### What it actually asks for
A conversational, multi-agent marine decision support platform. Interpret natural-language queries (in Indian regional languages, auto-detected), decompose into tasks, coordinate specialised agents (planning, data discovery, weather, ocean analytics, geospatial reasoning, risk assessment, visualisation), retrieve satellite and oceanographic data, reason spatiotemporally, and return explainable recommendations with maps and evidence. Plus safety alerts, geofencing near maritime boundaries and protected areas, and route advice.

### Novelty
Moderate. Multi-agent orchestration is well-trodden by 2026, and INCOIS already issues Potential Fishing Zone advisories and ocean state forecasts operationally. The system is largely an intelligent interface over existing products. *(medium-high)*

Real novelty is available in two places:
- **Multilingual, low-literacy-appropriate interaction.** Tamil, Malayalam, Telugu, Odia, Marathi and Bengali voice interaction for fishermen is a genuine accessibility contribution and directly addresses why existing advisories are under-used. This is more socially valuable than the agent architecture and I would lead with it.
- **Cross-source correlation with explanation.** The PS lists "why has fish productivity declined in this coastal region" as a target query. Answering that requires actually correlating chlorophyll trends, SST anomalies, upwelling indices, and fishing pressure — real analysis, not retrieval. If you build one such causal-analysis capability properly, it distinguishes you from teams that build pure retrieval.

### Complexity
Moderate. Heavy on integration breadth, light on any single hard problem. Many APIs, many agents, much glue.

### Feasibility
Good. INCOIS publishes PFZ advisories, ocean state forecasts, and Live Access Server data. IMD gives weather. Sentinel-3 and MODIS give chlorophyll and SST. Maritime boundaries and marine protected area polygons are available. *(medium-high — verify API access terms for INCOIS products specifically, since some require registration.)*

Main risk is scope. The PS lists eight example query types across weather, safety, ecology, routing, and regulation. Build three well.

### Scale of impact
Very high and morally weighty. Indian marine fisher fatalities from weather events, and accidental crossing of the International Maritime Boundary Line (particularly the Palk Strait), are serious recurring problems with documented human cost. The geofencing feature alone has a strong case. *(medium-high confidence on the general problem; I'd verify current figures rather than cite from memory.)*

### User experience
This is the whole project. Your user may have limited literacy, an entry-level phone, intermittent connectivity, and is operating a boat. That is a demanding design brief and taking it seriously is the differentiator. Voice-first, offline-capable for cached advisories, minimal text, high-contrast, and simple visual symbols. A team that designs for a laptop-based analyst has missed the point.

### Future work
Integration with vessel transponders, community reporting, and catch logging for stock assessment.

### Failure mode
It is a chatbot in front of a weather API. The agents are functions with agent-sounding names, and the "reasoning" is template filling. This PS is easy to fake and correspondingly hard to impress with. If you pick it, one genuinely hard analytical capability — the productivity-decline query, or true multi-constraint route optimisation over a sea-state field — must be real.

---

## 20. PS 26227 — Semantic Retrieval and Multi-Temporal Change Analysis of Satellite Imagery
**Theme:** Space Technology · The sovereignty and offline requirements read like a defence or intelligence organisation

### What it actually asks for
Make an imagery archive searchable by meaning. Free-text search over tiles ("newly built structures near a river", "large vehicle concentrations on open ground"), image-to-image similarity search, multi-temporal change detection with change-type classification and earliest-observation estimation, systematic false-alarm suppression for season/illumination/cloud/registration confounds, embedding-based clustering for site discovery, an analyst review queue with provenance and feedback-driven reranking, incremental index updates without full rebuild, and **complete offline operation with network disabled during evaluation**.

### Novelty
Moderate on components, high on the system. Every piece exists — CLIP-style embeddings, remote sensing foundation models, vector databases, change detection networks. What does not commonly exist is all of it integrated, running on-premises, with rigorous false-alarm control and a provenance-preserving analyst workflow. *(medium-high)*

Two places with real research content:

**False-alarm suppression** is the deepest technical problem here and the PS spends the most words on it. Distinguishing genuine change from seasonal phenology, illumination difference, atmospheric variation, and sub-pixel misregistration is the central unsolved difficulty in operational change detection. The PS even states the desired trade-off — precision over recall. A team that builds explicit confound modelling (phenological normalisation, illumination-invariant representation, registration-error-aware confidence) rather than treating false alarms as a threshold to tune, is addressing the actual problem. *(high)*

**Earliest-observation estimation** is subtler than it appears. Determining the earliest date at which a change is supported by usable imagery requires reasoning jointly about change confidence and observation quality (cloud, gaps, sensor), and it is a genuinely interesting sub-problem that most teams will treat as trivial.

### Complexity
Highest system complexity in the shortlist. Vector indexing at scale, incremental ingestion, multi-model inference, geospatial provenance, a full analyst workflow with audit trails, and a hard offline constraint. This is a production system specification, not a prototype specification.

### Feasibility
This is the concern, and it is about **scope, not data**. The data is easy — Sentinel-1, Sentinel-2, Landsat Collection 2, and Bhuvan are all open and named in the PS.

But count the required capabilities: six explicitly enumerated, each of which is a substantial project. Plus reproducibility deliverables — architecture note, index-build procedure, provenance documentation, and a report stating indexed area, scene count, build time, storage footprint, query latency, and hardware. That is a professional engineering deliverable set. *(high)*

The offline constraint is also a real operational risk: everything must be staged locally, including model weights, before the network is cut. One missing dependency at demo time is fatal. Rehearse the full offline run more than once.

Note also that the evaluation uses **held-out** semantic queries and **held-out** labelled change/no-change cases you have not seen. You cannot tune to the test set, which means genuine generalisation is being measured. That rewards principled work and punishes demo-tuning, which is unusual and worth knowing.

### Scale of impact
High in the intelligence, monitoring, and infrastructure-audit domains. Narrower in population terms, but the sponsoring organisation is clearly a serious operational user, and fit to their needs is what will be scored.

### User experience
Analyst-centric and specific. The review queue with before/after evidence, confidence, and accept/reject that persists to an audit trail is the core interface, and it is explicitly enumerated in the PS. Build exactly what is asked for rather than reinterpreting it — this PS is unusually prescriptive, which is a signal that the requirements come from real operational practice.

### Future work
Additional sensors, more change types, and active learning from analyst feedback.

### Failure mode
Semantic search works beautifully, change detection is a thresholded image difference that reports every seasonal variation as change, and the false-alarm section of the demo is skipped. The held-out no-change cases will expose this immediately. Build the confound suppression as a first-class component from the start, not as a post-hoc filter.

---

# Comparative View

## Where the technical difficulty actually sits

| PS | Title (short) | Technical depth | Novelty headroom | Data risk | Demo strength |
|---|---|---|---|---|---|
| 26001 | NER Landslide | Moderate | Low–Moderate | Low | Moderate |
| 26009 | MOIL Manganese | Moderate | Moderate (if reframed) | **High** | Moderate |
| 26012 | Cadastral Extraction | High | Moderate (topology) | Moderate | Strong |
| 26011 | 3D ULPIN | High | **High** | Moderate | **Very strong** |
| 26014 | Land Stack DPI | Moderate | Low–Moderate | Low | Moderate |
| 26016 | Land Acquisition MIS | Low | Low | Low | Moderate |
| 26017 | Delay Prediction | Moderate | Moderate | **Very high** | Moderate |
| 26018 | Record Digitization | High | Moderate–High | Moderate | Strong |
| 26019 | Research Platform | Low | **Very low** | Low | Weak |
| 26053 | 2.5D LiDAR | **High** | Moderate–High | **Very low** | Strong |
| 26066 | OceanEmbed | High | Moderate | Moderate (volume) | Moderate |
| 26083 | Heat Stress Index | Moderate | Moderate | Moderate–High | Strong |
| 26085 | Urban Flood Nowcast | **Very high** | **High** | **Very high** | **Very strong** |
| 26142 | Super-Resolution | Moderate | Moderate (uncertainty) | Low | Strong |
| 26143 | Oil Spill + AIS | **High** | Moderate–High | **Low** | **Very strong** |
| 26166 | Chandrayaan Registration | **High** | **High** | Moderate | Moderate |
| 26167 | SatQuery AI | High | Moderate | Moderate (compute) | **Very strong** |
| 26168 | Dead Reckoning | **High** | Moderate–High | **Low** | **Very strong** |
| 26176 | ORCA Marine Agents | Moderate | Moderate | Low | Strong |
| 26227 | Semantic Retrieval | **Very high** | Moderate–High | Low (scope risk high) | Strong |

Ratings are my judgement, not measurement. Treat the columns as prompts for your own assessment rather than as findings.

## Groupings that might help you decide

**Objectively evaluable, hard, well-scoped.** 26166, 26168, 26053, 26143. These four share a valuable property: success is measurable against a stated metric, the datasets are identified, and the hard part is genuinely hard. If your team wants to be judged on engineering rather than on presentation, this is the cluster.

**High novelty, high risk.** 26085, 26011. Both have real intellectual content and both have a specific blocker (drainage data; interior building data). If the blocker resolves, they are among the strongest options here. Check before committing.

**High polish, low novelty.** 26016, 26014, 26019. Achievable, complete, unremarkable. There is a legitimate strategy here — a flawless system beats an ambitious broken one — but it depends on your team out-executing rather than out-thinking the field.

**Data-integrity risk.** 26017 and 26009 both require you to build on data that may not exist. That is survivable if handled openly and fatal if handled quietly.

**Demo-forward.** 26168, 26085, 26011, 26167. These four produce demonstrations that need no explanation, which matters more in a time-limited judging round than most teams appreciate.

---

# What I'd Want to Check Before You Commit

Several of my assessments rest on assumptions I could not verify from the PS text. Each of these is a quick check and each could change a recommendation:

1. **Attached datasets.** For 26009, 26012, 26017, 26018, and 26085 I have flagged data availability as the deciding risk. If the SIH portal attaches real datasets to any of these, my feasibility ratings are wrong and should be revised upward. Check every PS's attachment section.

2. **The BigEarthNet reference in 26167.** The arXiv ID in your sheet does not match the actual BigEarthNet paper. Confirm the real link.

3. **Prior work searches.** For 26053 (learned adaptive-resolution 2.5D mapping), 26066 (subsurface reconstruction for the Indian Ocean), and 26085 (GNN surrogates for drainage hydraulics), my novelty estimates are based on general familiarity rather than a current literature check. Twenty minutes on arXiv for each would firm these up, and would also give you the related-work slide that most teams skip.

4. **Team composition against required skills.** Several of these need capabilities beyond geoinformatics. 26168 needs mobile deployment. 26167 needs GPU access and VLM fine-tuning experience. 26227 needs serious backend engineering. 26011 needs 3D geometry. Map your team's actual skills against these before novelty considerations.

5. **Competition density.** SIH publishes per-PS submission counts. A brilliant idea on a PS with four hundred submissions faces different odds than a solid idea on one with thirty. Check the counters before locking in.

6. **Current criteria wording.** I've used the standard SIH evaluation set, which has been consistent across editions. Confirm against your SPOC's 2026 material in case the weighting changed.

---

# Where This Analysis Could Be Wrong

Worth stating plainly, since these ratings will influence a decision that you cannot easily reverse.

**My novelty judgements are the weakest part of this document.** I have assessed novelty from general familiarity with each field rather than from a current literature search. For fast-moving areas — remote sensing VLMs, learned inertial odometry, super-resolution — the state of the art in mid-2026 may differ substantially from what I'm reasoning about. If I'm wrong, I am most likely *overestimating* novelty headroom in the ML-heavy PSs, because those fields move fastest. Treat every novelty rating as a hypothesis to check, not a finding.

**I may be systematically underrating the workflow-heavy land governance PSs.** My ratings reward algorithmic difficulty, but SIH judging includes clarity, user experience, and practicability, and the sponsoring departments for those PSs care about workflow completeness and standards compliance more than about model sophistication. A panel of land-records officials will not be impressed by a topology algorithm they cannot evaluate, and may be genuinely impressed by a system that matches their actual process. My framing embeds a bias toward technical depth that may not match how these are scored.

**A specific signal that my read is wrong:** if you check the submission counters and find the land-governance PSs are heavily contested while the ISRO-linked technical ones are sparse, that tells you experienced teams are reading the incentives differently than I am — and they have information about the judging that I don't.

**How to test this cheaply.** Before committing, take your two leading candidates and write the one-slide judge summary for each: the problem, the contribution, the demo, the impact. If one of them cannot be stated in four sentences without hedging, that is a signal the PS is either too diffuse or that you have not found its core yet. It is a fast and surprisingly reliable filter, and it costs an hour rather than a weekend.
