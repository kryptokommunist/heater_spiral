# Thermal Somatic Feedback for Attention Regulation
## Research Proposal — BackInFocus × Heat Pixel Jacket

**Target venue:** CHI 2027 (primary), UIST 2026 (fallback)
**Last updated:** 2026-07-20
**Hardware repo:** [hpi-smart-clothing/imu2-hardware](https://github.com/hpi-smart-clothing/imu2-hardware/tree/main) | **GitHub org:** [hpi-smart-clothing](https://github.com/hpi-smart-clothing)

---

## Motivation

Contemporary computing interfaces are designed almost exclusively for the visual-cognitive channel, leaving the body as an unused output surface. Meanwhile, knowledge workers face a growing attention crisis: fragmented workflows, constant notification pressure, and the expanding presence of AI assistants that blur the boundary between self-directed and machine-generated cognition.

This proposal builds on an existing project to address this gap. **BackInFocus** detects body posture in real time using IMUs. Building on that we want to cover the jacket in heat pixels to direct attention across the body as somatic input.

---

## Research Directions

### 1. Thermal Cueing for Posture-Triggered Focus Transitions

When BackInFocus detects a posture shift associated with attention drift — slouching, forward head collapse, prolonged static hold — the heat pixel jacket delivers a brief warming pulse to the relevant body region instead of a visual or audio alert. The thermal cue reaches the body without competing for visual attention.

**Research question:** Does spatially-targeted thermal cueing tied to posture state reduce the cognitive cost of attention redirects compared to visual or audio alerts?

**Novelty:** Prior work on graceful interruption (Bolton et al. 2015) used thermal wrist feedback as a standalone alert; this direction integrates spatially-distributed heat pixel actuation with a real-time IMU-based posture model, closing the sense–act loop across the body surface rather than at a single point.

**Key precedents:** Bolton et al. 2015 (wrist thermal for graceful interruption); Matthies et al. 2018 (scalable thermal notification intensity).

---

### 2. Closed-Loop Biofeedback and Embodied Rendering of Posture

BackInFocus's continuous posture estimates are mapped to spatially-located thermal feedback across the jacket surface — warmth lights up at the slouched shoulder, a sweep moves along the spine on correction, and overall postural quality is encoded in real time as an ambient distributed signal. Rather than triggering only on threshold crossings, the body receives an ongoing somatic rendering of its own postural state, transforming an abstract sensor metric into a felt, first-person experience consistent with soma design principles.

**Research question:** Does continuous spatially-distributed thermal rendering of posture state improve metacognitive accuracy about one's own body and responsiveness to posture shifts, compared to visual dashboards or single-point haptic alerts?

**Novelty:** Existing thermal biofeedback work (ThermoPixels, Umair et al. 2020) targets arousal or emotion; applying continuous spatial thermal encoding to posture state across a garment surface is novel. Visual dashboards for body state are well-studied; this comparison between external visual representation and internal somatic representation is a distinct contribution to the soma design literature.

**Key precedents:** ThermoPixels (Umair et al. 2020); Affective Touch (Zhao et al. 2023); Berger et al. CHI 2023 (interoception and flow); WarmMind necklace (Roquet 2021); Höök soma design framework (2018).

---

### 3. Thermal Pacing for Movement and Breathing Entrainment

The heat pixel jacket delivers rhythmic warm/cool cycles — originating at the sternum or spine and spreading outward — analogous to breathing meditations. BackInFocus can time these cycles to detected postural rhythms and stillness patterns, warming during identified deep-work phases and cooling at natural movement breaks, to scaffold somatic anchoring through the body surface.

**Research question:** Does externally synchronized thermal rhythm delivered across the jacket surface promote focus entry or maintenance compared to purely cognitive or visual interventions?

**Novelty:** Flow induction research has focused on cognitive scaffolding (DeepFlow, Maier et al. 2019) and haptic entrainment (Lee et al. ISWC 2025). Coupling detected postural state to spatially-moving thermal rhythm across a jacket — using the body's own breathing-like response as the delivery mechanism — has not been examined.

**Key precedents:** Berger et al. CHI 2023 (interoception → flow); Lee et al. ISWC 2025 (rhythmic haptic biofeedback via entrainment); DeepFlow (Maier et al. 2019).

---

### 4. Thermal Co-Presence for Collaborative Focus

Paired jackets worn by co-workers exchange posture state signals over UWB proximity. When both individuals are in a detected upright, focused posture state, each jacket delivers a subtle shared-warmth signal — a non-verbal, non-disruptive social affordance for synchronized deep work.

**Research question:** Can thermal co-presence signals derived from shared posture state support collaborative focus without disrupting either person's individual work?

**Novelty:** Thermal social biofeedback (Haliburton et al. IMWUT 2023; Breeze, Frey et al. CHI 2018) has been studied in passive display and remote-sharing contexts. Using mutual detected posture state — rather than raw physiological signal — as the trigger for distributed thermal co-presence across the jacket is unexplored.

**Key precedents:** Haliburton et al. IMWUT 2023 (thermal display of group engagement); Breeze (Frey et al. CHI 2018).

---

### 5. Embodied Developer Experience: Thermal Modes and Body Textures for AI-Augmented Programming

Software developers cycle through distinct cognitive modes during programming: deep flow, cognitive overload, error-debugging frustration, recovery, and AI-assisted composition. These states are physiologically measurable — heart rate, EDA, skin temperature — and have been empirically characterized in workplace studies (Stolp, Brandebusemeyer et al., HPI/SAP 2024–25, using the CognitIDE IntelliJ plugin). Yet nothing currently feeds the detected state back to the developer's body.

The heat pixel jacket closes this loop by delivering distinct thermal patterns distributed across the body surface that embody each mode. The design hypothesis is that a developer can internalize their own cognitive state more quickly and with less attentional cost when that state is felt across the body rather than read on a screen.

**Thermal mode mapping:**

| Developer state | Thermal pattern |
|---|---|
| Deep flow / deep work | Slow sustained warmth — constant, quiet, centred on upper back |
| Cognitive overload | Gentle cooling pulse spreading outward — a somatic "ease off" |
| Error loop / frustration | Rhythmic warm/cool cycle along the spine — pacing cue toward regulated breathing |
| Break recommended | Single warming bloom across the shoulders, then fade — somatic nudge, no visual interrupt |
| AI-assisted composition | Distinct warm texture (e.g., diffuse radiant warmth across the whole back) vs. self-directed work (focused point warmth at the sternum) |

**Body texture for interface state — tabs and agent contexts:** The heat pixel jacket extends this further. Different body locations or thermal textures can correspond to different interface contexts — for instance, distinct heat patterns for each open Claude Code agent chat tab or IDE panel. A developer switching between a debugging agent, a code-review agent, and a documentation agent would feel a different somatic texture for each, reducing the cognitive cost of context switching. The interface state becomes spatially distributed across the body rather than requiring visual re-orientation. This maps the abstract concept of "which context am I in" onto pre-attentive, ambient body sensation.

**The AI-augmented cognition problem:** As developers increasingly work alongside AI coding assistants (Claude Code, Copilot, Cursor), a core metacognitive challenge emerges: distinguishing moments of genuine self-directed reasoning from moments of passive acceptance of AI output. A dedicated thermal register for AI-assisted vs. self-driven cognitive effort could help developers maintain awareness of their own cognitive engagement — a somatic grounding for the human in the loop.

**Research question:** Do thermal mode patterns corresponding to physiologically detected developer states, and thermal textures corresponding to interface contexts, reduce the cognitive cost of mode/context switching and improve metacognitive accuracy about one's own cognitive engagement during AI-augmented software development?

**Novelty:** CognitIDE maps physiological data to code (Stolp et al.) but provides no actuation. AI-augmented coding tools provide no physiological sensing. Closing the sense–act loop for developer experience specifically, and using distributed thermal body texture across the jacket as a spatial encoding of interface context, has no direct precedent. The nearest work (Matthews et al. UIST 2004; AttentivU, Kosmyna et al. 2018) addresses peripheral attention guidance but not somatic mode embodiment or AI-context disambiguation.

**System composition:** CognitIDE physiological IDE plugin + BackInFocus IMU posture layer → heat pixel jacket actuation.

**Key precedents:** CognitIDE (Stolp, Brandebusemeyer et al., HPI 2024–25); Matthews et al. UIST 2004 (managing attention in peripheral displays); Kosmyna et al. UbiComp 2018 (AttentivU biofeedback glasses); Whitmore et al. CHI 2024 (rhythmic haptic stimuli for attention).

---

### 6. Thermal Sensation for Affective Presence in Mediated Environments

In VR, immersive media, and teleoperation, warmth carries immediate affective meaning — proximity to a fire, sunlight, social closeness. A heat pixel jacket can encode these affective qualities spatially across the body: warmth spreading from the back like sunlight, cooling at the shoulders as a calming signal. BackInFocus's continuous posture and movement monitoring can adapt the intensity and spatial distribution of thermal affective content to the user's detected engagement and postural state, preventing over-stimulation during tense or static body configurations.

**Research question:** Can spatially-addressable body-worn thermal stimulation via a heat pixel jacket convey affective content (warmth, calm, social presence) that measurably shifts emotional state in immersive environments, independently of visual rendering?

**Novelty:** Thermal feedback in VR has been studied as environmental realism (temperature matching visual cues). The distinct question — can spatially-distributed thermal sensation across a garment carry affective meaning in its own right, sufficient to shift emotional state even without matching visual content — has not been isolated. The coupling with continuous posture-based state sensing is also new.

**Key precedents:** Ichihashi et al. CHI 2025 (everyday thermal experience design); Haar et al. Scientific Reports 2020 (augmenting aesthetic chills); ThermoCaress (Liu et al. CHI 2021).

---

## Prioritization

**Hardware-first primary deliverable:** The heat pixel jacket — posture sensing via IMU (BackInFocus), custom PCB integrating sensing and heating circuit, and spatially-addressable heat pixels covering the jacket surface — is the primary technical deliverable. All study directions depend on this platform.

**Three user study conditions (Oct–Nov 2026):** Drawing with heat patterns and posture feedback; cognitive task with heat patterns and posture feedback; navigation (distance and direction to a point in space). These pilot the core posture-sensing → heat pixel actuation loop across distinct task types.

**CHI 2027 primary:** Combine directions 1 + 2 into a within-subjects closed-loop study. Continuous thermal encoding of posture state across the jacket surface, with posture-shift pulses as the salient event. Measures: posture correction rate, attention recovery speed, metacognitive accuracy, subjective experience. Population: software developers (leverages existing HPI/SAP study infrastructure and participant pool).

**UIST 2026 fallback:** Technical + formative study introducing the heat pixel jacket as a programmable somatic display platform, with a thermal rhythm perception study and early direction 5 prototype.

**Direction 5 (AI-augmented programming):** Most novel and timely, but requires the full heat pixel jacket platform for the body-texture mapping. Best positioned as a CHI 2027 design paper or second empirical paper.

**Master's thesis use cases (~12 months):** Embodied software (coupling mouse movement / interface modes to warmth location/pattern); social / dance (mapping warmth patterns onto groups); meditation (orbiting awareness along spine front/back); stress reduction; emotional state transfer via movies/affordances; medical (stroke rehabilitation, pain management).

**Scope note:** Primary deliverable is technical implementation. An expert interview study (doctors, health practitioners) is optional if time permits. Key reading areas: meditation and body-computer interfaces. Second supervisor: Baudisch / Ebel chair.

---

## Timeline

| Period | Milestone |
|---|---|
| **Jul–Sep 2026** | Finalize heat pixel jacket prototype. Get posture sensing running (BackInFocus IMU pipeline). Test flex copper yarn heating. Develop PCB integrating posture sensing + heating circuit. Couple posture sensing → heat pixel output → breath/movement/software UIs. Implement direction 1 system. |
| **Oct–Nov 2026** | Small user study (3 conditions): drawing with heat patterns + posture feedback; cognitive task with heat patterns + posture feedback; navigation (distance + direction to point in space). Pilot study (n=8–10): directions 1+2, within-subjects, software developer population. Refine thermal patterns and study protocol. |
| **Nov 2026** | UIST 2026 conference (Nov 2–5). Submit platform/formative paper if pilot yields sufficient results. UIST 2027 deadlines not yet announced — expected ~Mar/Apr 2027. |
| **Dec 2026–Jan 2027** | Full study (n=24–30): directions 1+2 combined. Data collection at HPI / SAP sites. |
| **Feb 2027** | Data analysis, paper writing. |
| **Sep 10, 2026** | **CHI 2027 full paper deadline** |
| **Jan 21, 2027** | **CHI 2027 posters / demos / SRC deadline** |
| **Apr–Jun 2027** | Direction 5 prototype (heat pixel jacket + CognitIDE integration). Design probe study. |

> CHI 2027 full paper deadline is Sep 10, 2026 — data collection must be complete before then.

---

## People & Collaboration

| Person / Group | Context | Link |
|---|---|---|
| Patrick Baudisch | Primary supervisor (HPI) | — |
| Holly McKee | Supervisor (PhD, Arnrich chair, HPI) | — |
| Orhan Konak | Collaborator (PhD, Arnrich chair, HPI) | — |
| Arnrich / Ebel chairs | Second supervisor candidates | https://hpi.de/en/arnrich/teaching/smartclothing/ |
| Mikey Siegel | Contemplative / somatic HCI | https://www.cofo.org/team/mikey-siegel |
| Kathrin Devaney | Meditation use case | https://www.cofo.org/team/kathryn-devaney |
| Alexander Technique school | Posture feedback expertise | https://alexander-technik-schule.de/en/ |
| Tanzfabrik Berlin / Marameo | Dance studios — study participants | https://www.tanzfabrik-berlin.de/ / https://www.marameo.de/ |

---

## Conference Deadlines

**CHI 2027** — Pittsburgh, May 10–14, 2027 | https://chi2027.acm.org

| Track | Deadline |
|---|---|
| Full papers | **Sep 10, 2026** |
| Full paper notifications | Dec 17, 2026 |
| Posters & Interactive Demos | Jan 21, 2027 |
| Student Research Competition (≈ WIP) | Jan 21, 2027 |
| Panels | Nov 19, 2026 |
| Workshops (organizer submissions) | Oct 1, 2026 |

> CHI does not have a dedicated "work in progress" track — Posters and the Student Research Competition are the closest equivalents.

**UIST 2027** — deadlines not yet announced | https://uist.acm.org/
*(Based on UIST 2026 pattern: paper abstract ~Mar, PDF ~Apr, posters/demos ~Jul)*

| Track | Expected (est.) |
|---|---|
| Full papers — abstract | ~Mar 2027 |
| Full papers — PDF | ~Apr 2027 |
| Posters, Demos, Workshops (≈ WIP) | ~Jul 2027 |

---

## Relevant Academic Literature

### Thermal Wearable Feedback for Attention & Focus

| Paper | Authors | Venue | Year |
|---|---|---|---|
| ThermalBracelet: Exploring Thermal Haptic Feedback Around the Wrist | Peiris, Feng, Chan | CHI | 2019 |
| ThermoCaress: A Wearable with Illusory Moving Thermal Stimulation | Liu, Nishikawa et al. | CHI | 2021 |
| Feeling the Temperature of the Room: Unobtrusive Thermal Display of Engagement | Haliburton, Schött et al. | IMWUT | 2023 |
| HeatFlow: A Thermal-Tactile Display for Dynamic 2D Thermal Movements | Singhal, Honrales, Kim | CHI | 2025 |
| Thermal in Motion: Designing Thermal Flow Illusions | Singhal, Honrales et al. | CHI | 2024 |
| ThermEarhook: Spatial Thermal Haptic Feedback on the Auricular Skin | Nasser, Zheng, Zhu | ISMAR | 2021 |
| Thermodule: Wearable and Modular Thermal Feedback System | Maeda, Kurahashi | AH Conference | 2019 |
| ThermoPixels: Personalizing Arousal-Based Interfaces through Hybrid Crafting | Umair, Sas, Alfaras | DIS | 2020 |
| The Heat Is On: A Temperature Display for Affective Feedback | Tewell, Bird, Buchanan | CHI | 2017 |
| Scaling Notifications Beyond Alerts (thermal, mechanical, electrical) | Matthies, Daza Parra, Urban | arXiv | 2018 |
| Dual-sided Peltier Elements for Rapid Thermal Feedback in Wearables | Kang, Kim et al. | ICRA Workshop | 2024 |
| Thermal Masking Across the Human Body | Wang, Honrales et al. | CHI | 2026 |
| A Wrist-Worn Thermohaptic Device for Graceful Interruption | Bolton, Jalaliniya, Pederson | IxDA | 2015 |
| ThermalBitDisplay: Thermal Feedback Perceived Differently by Body Part | Niijima, Takeda et al. | CHI EA | 2020 |
| Thermal and Wind Devices for Multisensory HCI: An Overview | Da Silveira, Rodrigues, Saleme | Multimedia Tools | 2023 |

---

### Somatic / Interoceptive Biofeedback Wearables

| Paper | Authors | Venue | Year |
|---|---|---|---|
| Affective Sleeve: Wearable Materials with Haptic Action for Calmness | Papadopoulou, Berry, Knight, Picard | ACII | 2019 |
| Breathing Scarf: First-Person Wearable for Emotional Regulation | Cochrane, Cao, Girouard, Loke | CHI | 2022 |
| Breathing Inward: Targeted-Heat Wearable for Diaphragmatic Breathing | Dublin, Giron, Grishko, Talmasky | CHI | 2026 |
| Biosensing and Actuation — Platforms Coupling Body Input-Output Modalities | Alfaras, Primett, Umair et al. | Sensors (MDPI) | 2020 |
| Echoes of the Body: Multisensory Biofeedback for Interoceptive Awareness | Qiu, Zordan | ACM | 2025 |
| ambienBeat: Wrist Tactile Biofeedback for Heart Rate Regulation | Choi, Ishii | CHI | 2020 |
| SootheMind: Vibrotactile and Thermotactile Stimuli for Emotion Modulation | Wang, Ke, Huang et al. | CHI | 2026 |
| Exploring Personalized Vibrotactile and Thermal Patterns for Affect Regulation | Umair, Sas et al. | CHI | 2021 |
| Affective Touch as Immediate and Passive Wearable Intervention | Zhao, Tao et al. | IMWUT | 2023 |
| Emotional Response to Vibrothermal Stimuli | Shetty, Mehta et al. | Applied Sciences | 2021 |
| Affective Stroking: Thermal Mid-Air Tactile for Stress Regulation | He, Zeng et al. | Applied Sciences | 2024 |
| Constructing the Thermal Affective Design Space for Emotion Regulation | Feng, Halskov, Bennett et al. | CHI | 2026 |
| Augmenting Aesthetic Chills Using a Wearable Prosthesis | Haar, Jain, Schoeller, Maes | Scientific Reports | 2020 |
| Breeze: Sharing Biofeedback Through Wearable Technologies | Frey, Grabli, Slyper, Cauchard | CHI | 2018 |
| WarmMind: A Thermal Necklace for Emotional Awareness | Roquet | Dissertation | 2021 |
| Affective Wearable Haptic Interventions: A Systematic Literature Review | Lee, Rüddiger et al. | IMWUT | 2026 |

---

### Attention Restoration Theory (ART) + Technology

| Paper | Authors | Venue | Year |
|---|---|---|---|
| A Framework for Interactive Mindfulness Meditation Using Attention-Regulation | Niksirat, Silpasuwanchai et al. | CHI | 2017 |
| Attention Regulation Framework: Designing Self-Regulated Mindfulness Technologies | Niksirat, Silpasuwanchai et al. | TOCHI | 2019 |
| Immediate Attention Restoration from Interactive and Immersive Technologies | Barton, Sheen, Byrne et al. | Frontiers in Psychology | 2020 |
| Human Attention Restoration, Flow, and Creativity: A Conceptual Integration | Pham, Sanocki | Journal of Imaging | 2024 |
| Designing Restoration: Protecting and Restoring Attention Through Participatory Design | Tench | Dissertation | 2022 |
| Optimising Visual UIs to Reduce Cognitive Fatigue | Panakaduwa, Coates et al. | CHI | 2024 |

---

### Flow State Detection and Induction

| Paper | Authors | Venue | Year |
|---|---|---|---|
| Using Interoception to Foster Flow States During Mental Work | Berger, Knierim, Benke | CHI | 2023 |
| Exploring Flow in Real-World Knowledge Work Using cEEGrid Sensors | Knierim, Stano, Kurz, Heusch | CHI | 2025 |
| DeepFlow: Detecting Optimal User Experience from Physiological Data | Maier, Elsner et al. | AAMAS | 2019 |
| Flow From Motion: A Deep Learning Approach | Eteke, Havlucu et al. | arXiv | 2018 |
| Improving Attention Using Wearables via Haptic and Multimodal Rhythmic Stimuli | Whitmore, Chan, Zhang et al. | CHI | 2024 |
| Closed-Loop Rhythmic Haptic Biofeedback via Smartwatch for Relaxation | Lee, Moschina et al. | ISWC | 2025 |

---

### Calming / Stress Wearables (Affective Computing)

| Paper | Authors | Venue | Year |
|---|---|---|---|
| Why Meditation Wearables Fail: Reward Misspecification in Biofeedback Systems | Bose | arXiv | 2026 |
| AttentivU: Biofeedback Glasses to Monitor and Improve Attention | Kosmyna, Sarawgi, Maes | UbiComp | 2018 |
| Using Real-Time Biofeedback of HRV to Track and Improve Attention | Loudon, Zampelis, Deininger | CHI | 2017 |
| FAR: End-to-End Vibrotactile System for Affect Regulation | Miri, Arora et al. | CHI | 2022 |
| PIV: Placement, Pattern, Personalization of a Vibrotactile Breathing Pacer | Miri, Flory et al. | CHI | 2020 |
| You Can't Force Calm: Designing Respiratory Regulating Interfaces | Wongsuphasawat, Gamburg, Moraveji | CHI | 2012 |
| Peripheral Paced Respiration: Influencing Physiology During Information Work | Moraveji et al. | CHI | 2011 |
| Shared User Interfaces of Physiological Data: Social Biofeedback Review | Moge, Wang, Cho | CHI | 2022 |

---

### Developer Experience & Physiological Monitoring

| Paper | Authors | Venue | Year |
|---|---|---|---|
| CognitIDE: Physiological Data Linked to Source Code in IntelliJ | Stolp, Brandebusemeyer et al. | HPI/SAP internal | 2024–25 |
| DevEx: What Actually Drives Productivity | Noda, Storey, Forsgren, Greiler | ACM Queue | 2023 |
| DevEx in Action: A Study of Its Tangible Impacts | Forsgren, Kalliamvakou, Noda et al. | ACM Queue | 2023 |
| Measuring Mental Workload Using Physiological Measures: A Systematic Review | Charles, Nixon | Applied Ergonomics | 2019 |

---

### Soma Design / Body-Centric HCI

| Paper | Authors | Venue | Year |
|---|---|---|---|
| Designing with the Body: Somaesthetic Interaction Design | Höök | MIT Press (book) | 2018 |
| The Somatic Turn in Human-Computer Interaction | Loke, Schiphorst | Interactions | 2018 |
| Embracing First-Person Perspectives in Soma-Based Design | Höök, Caramiaux, Erkut et al. | Informatics | 2018 |
| Unpacking Non-Dualistic Design: The Soma Design Case | Höök, Benford et al. | ACM Transactions | 2021 |
| Making New Worlds — Transformative Becomings with Soma Design | Stähl, Balaam, Comber et al. | CHI | 2022 |
| Towards Designing for Everyday Thermal Experiences | Ichihashi, Bheda, Howell et al. | CHI | 2025 |

---

### Peripheral / Ambient Displays for Focus

| Paper | Authors | Venue | Year |
|---|---|---|---|
| Ambient Displays: Architectural Space as Interface | Wisneski, Ishii, Dahley et al. | Int. Workshop | 1998 |
| A Toolkit for Managing User Attention in Peripheral Displays | Matthews, Dey, Mankoff, Carter | UIST | 2004 |
| LiteCo: Illuminating Workspace Awareness with Ambient Display | Xu, Liu, Van Essen | CHI EA | 2025 |
| Ambient Information Design for a Work Environment | Torpus, Kovacevic, Navarro | HCI (Springer) | 2023 |
| Exploring Vibrotactile and Peripheral Cues for Spatial Attention Guidance | Stratmann, Lücken, Gruenefeld | Pervasive Displays | 2018 |

---

### Cognitive Load / Gaze-Based Attention Monitoring

| Paper | Authors | Venue | Year |
|---|---|---|---|
| A Survey on Measuring Cognitive Workload in HCI | Kosch, Karolus, Zagermann, Reiterer | ACM Computing Surveys | 2023 |
| Measuring Cognitive Load Using Eye Tracking in Visual Computing | Zagermann, Pfeil, Reiterer | CHI Workshop | 2016 |
| Neuro-Informed Joint Learning for Cognitive Workload Decoding | Yang, Feng, Chen | arXiv | 2025 |
| Real-Time Attention Regulation Using Wearable EEG-Based BCI | Huang, Chen et al. | IEEE Transactions | 2024 |

---

## Notable Commercial Precedents

| Product | Institution | Relevance |
|---|---|---|
| **Embr Wave** | Embr Labs (MIT spin-out) | Commercial thermal bracelet; cited in CHI literature for facilitating slow breathing and mindful attention — nearly identical form factor to Heater Spiral |
| **Muse Headband / HeartMath** | Interaxon / HeartMath Inc. | Biofeedback wearables cited as reward misspecification failure cases (Bose 2026) — relevant anti-patterns for system design |
| **E4 / Embrace Plus** | Empatica (MIT-affiliated) | EDA+PPG+temperature research platform; common sensor baseline for physiological monitoring |

---

## Theoretical Foundations

- **Attention Restoration Theory (ART)** — Kaplan & Kaplan (1989): involuntary attention, directed attention fatigue, restorative environments
- **Soma Design** — Höök (2018): body as primary design material, first-person methodology
- **Somaesthetics** — Shusterman: aesthetic appreciation of one's own somatic experience
- **Affective Computing** — Picard (1997): machines that recognize, interpret, and simulate human affect
- **Flow Theory** — Csikszentmihalyi: optimal experience, challenge-skill balance, loss of self-consciousness
