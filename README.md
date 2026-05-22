# 12-Lead ECG Digital Fusion Synthesizer

> **⚠️ Medical & Regulatory Disclaimer**
> 
> **For Technical Demonstration and Research Purposes Only.**
> 
> *Clinical Limitations & Off-Label Use*: Standard consumer wearables (e.g., Apple Watch) are clinically validated and FDA-cleared only for Lead I rhythm strips. Any attempt to obtain other leads using such hardware is strictly off-label, unverified, and lacks clinical validation.
> 
> *Experimental Synthesis*: The 12-lead reconstructions shown in this project are the result of mathematical synthesis and digital signal processing experiments. They have not been tested for diagnostic accuracy, sensitivity, or specificity.
> 
> *Not for Diagnostic Use*: This software is a hobby/research project and is not a medical device. It must not be used to diagnose, treat, or manage any medical condition.
> 
> *No Professional Liability*: As the developer, I (Denis Fedorov) provide this as a proof-of-concept only. All clinical decisions must be made using standard-of-care, 12-lead hospital ECG equipment.

**A clinical-innovator's full-stack integration bridging iOS mobile health hardware with advanced Python digital signal processing to synthesize, natively reconstruct, and export clinical-grade 12-lead electrocardiograms.**

As an Electrophysiologist, I often encounter the limitations of consumer mobile ECG devices. While they excel at single-lead ambulatory monitoring, they lack the multi-vector spatial resolution necessary for comprehensive arrhythmia and ischemia analysis. 

To solve this, I designed a full-stack architecture: a native iOS application imports sequential single-lead recordings from the Apple Health database, packages and offloads them to a Python/FastAPI processing engine for high-resolution artifact suppression, cross-correlation alignment, and lead synthesis, and receives the data back. The mobile phone then reconstructs and plots all 12 channels in real-time, enabling direct, high-fidelity clinical PDF report generation and export.

---

## System Architecture

```mermaid
graph TD
    subgraph Consumer Wearable
        WD[Apple Watch] -- "Records Single-Lead ECG (Lead I)" --> DB[(Apple Health DB)]
    end

    subgraph iPhone Companion App (SwiftUI & Swift Charts)
        DB --> UI[Data Selection UI]
        UI --> PKG[Package Generator (CSV & JSON)]
        PKG -- "Sends ECG Package" --> BE[FastAPI Backend]
        
        BE -- "Returns Synthesized ECG Data" --> RX[Data Receiver]
        RX --> PLT[12-Channel Waveform Plotter]
        PLT --> PDF[Local 12-Lead PDF Report Generator]
    end

    subgraph Python Processing Engine (FastAPI)
        BE --> AS[Advanced Artifact Suppression]
        AS --> TA[Temporal Alignment & Synchronization]
        TA --> LS[Multi-Vector Lead Synthesis]
        LS --> MA[Morphology Analysis & Classification]
        MA --> BE
    end

    subgraph Clinical Output (Generated on iPhone)
        PLT -- "Natively Visualized" --> DS[Interactive 12-Lead SwiftUI Chart]
        PDF -- "Share & Export" --> EXP[Standardized 12-Lead PDF Report]
    end
```

---

## How It Works

### 1. iPhone Data Ingestion & Packaging
* **Data Source**: Sequential single-lead rhythm strips (Lead I) are recorded by the consumer wearable (e.g., Apple Watch) and stored within the device's local database.
* **Selection & Control**: The native iPhone app acts as the front-end controller. The user selects the group of recordings to combine, choosing whether to apply real-time synchronization.
* **Transmission Packaging**: The app compiles the high-resolution voltage samples into a structured CSV file and pairs it with JSON-formatted lead assignment metadata, transmitting the unified payload to the FastAPI backend.

### 2. High-Performance DSP Pipeline (Python/FastAPI)
Upon receiving the raw package, the Python backend executes a signal processing pipeline:
* **Artifact Suppression**: Leverages Discrete Wavelet Transform (DWT) denoising to suppress baseline wander and high-frequency noise from Lead I and II datasets without smoothing out crucial QRS/T morphology.
* **physiological Cross-Correlation Synchronization**: Aligning sequentially recorded strips requires correcting for hardware sampling rate drift and heart rate variability. The engine runs a specialized cross-correlation temporal shift algorithm to lock onto matching QRS landmarks, ensuring perfect temporal synchronization.
* **Multi-Vector Lead Synthesis**: Using the synchronized and cleaned primary vectors, the engine mathematically derives the remaining limb leads (Lead III, aVR, aVL, aVF) to form the complete frontal plane representation of cardiac electrical activity.
* **Morphology Analysis**: Performs dynamic wave delineation (P, Q, S, T peaks and intervals) against synthesized templates to verify cycle-by-cycle physiological consistency.

### 3. Native Reconstructive Visualization (iPhone App)
* **Real-Time 12-Channel Reconstruction**: Once the FastAPI backend returns the synthesized multi-lead payload, the iPhone app ingests the data and reconstructs the complete 12-channel view.
* **Native Swift Charts Rendering**: The app utilizes Apple's high-performance Swift Charts framework to natively render all 12 channels onto the screen with fluid, hardware-accelerated scrolling.

### 4. Standardized Clinical PDF Export
* **Landscape Report Layout**: Directly within the iPhone app, users can generate a standardized, print-ready 12-lead ECG grid PDF report.
* **Universal Calibration**: The PDF renders the waveforms precisely mapped to a standard ECG paper grid layout (25 mm/s horizontal speed, 10 mm/mV vertical sensitivity).
* **Direct Export**: Tapping the export icon triggers the native iOS Share Sheet, letting patients immediately share, email, or save the reconstructed 12-lead report.

---

## Key Features & Clinical Utility
* **Offloaded High-Math DSP**: Leverages Python's advanced scientific libraries (`numpy`, `scipy`, `neurokit2`, `pywt`) via a FastAPI connection for deep signal analysis while keeping the mobile client lightweight.
* **Native iOS Fluidity**: Eliminates lag by plotting tens of thousands of voltage points natively using SwiftUI and Swift Charts, ensuring a premium clinician-focused UX.
* **Physiological Alignment Accuracy**: Ensures that diagnostic measurements (QRS width, PR, and QTc intervals) remain intact across the reconstructed channels through precise temporal alignment.
* **Standardized Layouts**: Visualizes the combined results using the classic grid presentation, making it instantly readable by any cardiologist or electrophysiologist worldwide.
