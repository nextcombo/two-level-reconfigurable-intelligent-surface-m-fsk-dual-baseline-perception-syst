# Two-Level Reconfigurable Intelligent Surface M-FSK Dual-Baseline Perception System: Full-Linkage Simulation from Physical to Closed-Loop Tracking [with Python Code]

Two-Level Reconfigurable Smart Surface M-FSK Dual-Baseline Perception System: From Physical Modeling of PIN Diodes to Full-Linkage Simulation from Closed-Loop Tracking
The University of North Texas (UNT) researchers have released a full-physical simulation system for an integrated scenario of 28 GHz millimeter wave communication and perception. Centered around "two-level reconfigurable smart surface (RIS) + multi-quadrature frequency shift keying (M-FSK) waveform," the system comprehensively covers the entire link verification process from the equivalent circuit modeling of PIN diodes, link budget analysis, Cramer-Rao lower bound derivation, Monte Carlo statistical validation, closed-loop beam tracking, to multi-system architecture system-level benchmark testing. This paper systematically introduces the design logic, physical modeling methods, and key numerical conclusions of this system.

1. Problem Introduction: Why Single-Level RIS Faces Rate Limitation
Space-Time Modulation Reconfigurable Smart Surface (STM-RIS) has been widely regarded as a critical enabling technology for millimeter wave perception and communication in recent years. Its basic idea is to dynamically adjust the phase configuration of hundreds of units on the surface to achieve beam shaping in the spatial domain and embed modulation information in the temporal domain. However, when these two functions are condensed onto the same physical panel, a structural contradiction emerges. Specifically, beam shaping requires the array to switch phase configurations at extremely high speeds to generate radar waveforms with multiple tones; whereas the serial peripheral interface (SPI) as a conventional control link typically operates at a clock frequency of 10 MHz. For an array of 16×16 = 256 units, each reprogramming requires transmitting 256 unit phase words, each with 3 bits, totaling 768 bits. Based on a SPI clock rate of 10 MHz, the single reprogramming takes about 76.8 microseconds, corresponding to a symbol rate of only about 13 kHz. This value is far insufficient to support the waveform variability required by broadband radar perception.
The core contribution of this project lies in the introduction of a two-tier decoupling architecture: waveform modulation and beamforming are distributed across two physically separate but near-field coupled RIS panels to be completed separately, thereby bypassing the speed bottleneck of serial control.
II. System Architecture: Two-Tier T-RIS/R-RIS Decoupling Design
2.1 The spatial topology of the system follows a four-hop (4-hop) dual-baseline path:
Continuous Wave Transmitter (CW Tx) → T-RIS → (2 mm near-field gap) → R-RIS → (10 m) → Target → (10 m backscatter) → R-RIS → (8 m) → 16-element receiving array. The inter-tier distance between T-RIS (transmitter reconfigurable smart surface) and R-RIS (receiver reconfigurable smart surface) is only 2 mm, equivalent to about 0.19 wavelengths (λ ≈ 10.71 mm at 28 GHz). Within this extremely narrow gap, electromagnetic waves propagate in the evanescent mode (evanescent mode), with coupling coefficient approximately |C₀| = 0.309 (−10.2 dB).
2.2 Functional division:
T-RIS: Specializes in high-speed waveform modulation. Its core innovation is to abandon the SPI-based sequential programming mode for each unit and instead utilize the internal clock boundary of the onboard direct digital frequency synthesizer (DDS) to rapidly step through reflection phase configurations. This enables the symbol rate from about 13 kHz to 714.3 kHz, accelerating the factor by 55 times.
R-RIS: Specializes in space beamforming and target angle tracking. Since the angle change of the target within a single coherent processing interval (CPI, about 17.2 ms) is minimal, R-RIS only needs to update the beam pointing instructions once per CPI, completely without involvement in high-speed switching. This design separates high-dynamic tasks from the slow panels, fundamentally improving the trade-off between complexity and performance.
III. Waveform Design: The M-FSK radar waveform selected for this project is an **M-ary Frequency Shift Keying (M-FSK)** system, with parameters set to M = 12 discrete tones, a frequency interval Δf = 25 MHz, covering the up-converted band from fc + 25 MHz to fc + 300 MHz.
Compared to Linear Frequency Modulation Continuous Wave (FMCW), the key advantage of M-FSK in motion target perception lies in its distance-Doppler decoupling. In the dechirp process of FMCW, the radial velocity of the target introduces a linear offset into the beat frequency, leading to what's known as "distance-Doppler coupling"—the velocity error is projected as a false distance displacement, resulting in "ghost" targets. However, M-FSK encodes the distance information within the phase difference between tones and encodes the Doppler information within the phase evolution across the sweep frequency, both naturally orthogonal, fundamentally eliminating the ghost issue.
The spectrum model of the T-RIS's kth symbol output signal is:
sₙ(t) = T_mean · δ(f − fc) + T_sb / 2 · [δ(f − fc − f_m,k) + δ(f − fc + f_m,k)]
where T_mean is the carrier feedthrough coefficient, and T_sb is the sideband modulation coefficient. The upper sideband (+f_m,k) enters the bandpass filter (BPF) passband as the desired signal, while the lower sideband (−f_m,k) mirror frequency falls within the BPF stopband and is suppressed. A single BPF can simultaneously filter all 12 desired tones.
For a beam pointing angle θ₁ = 60°, the spatial beam tilt angle at the farthest tone (+300 MHz) is calculated to be only about 3.2% of the half power beam width (HPBW), significantly below the design margin of 10%, verifying the array's quasi-constant beam pointing within a bandwidth of 300 MHz.
IV. Device Physical Layer: Equivalent Circuit Model of PIN Diodes - The physical simulation system at the foundation of this project starts with the equivalent circuit modeling of PIN diodes. At a carrier frequency of 28 GHz, the circuit parameters for the PIN diode are set as follows:
Serial Resistance Rs = 2 Ω (including bonding wire and contact resistance)
Junction Capacitance Cj = 25 fF
Forward Bias Junction Resistance Rj_ON = 1 Ω
Reverse Bias Junction Resistance Rj_OFF = 8 kΩ
Feedline Characteristic Impedance Z₀ = 50 Ω
In ON State, Total Impedance Z_ON ≈ 3 Ω (near short circuit), Reflectivity |Γ_ON| approximately −0.4 dB; In OFF State, Junction Capacitance Cj and Reverse High Resistance form a capacitive impedance, Reflectivity |Γ_OFF| close to 0 dB. The vector average defines Carrier Feedthrough T_mean, while the difference defines Sideband Modulation Capability T_sb. Simulations show that at 28 GHz, |T_sb| ≈ −7.17 dB, |T_mean| ≈ −4.46 dB, meeting the basic signal-to-noise ratio conditions required for M-FSK waveform generation. The energy consumption of a single PIN tube switch event is about 10 pJ, providing device-level basis for subsequent system-level power accounting.
V. Dual-Base Link Budget System: The signal link is decomposed into 13 cascading gain/loss terms, tracking the signal power's attenuation and amplification in dB units. Key stages include:
Stage
Physical Meaning
Contribution Value
a
Transmitter Radiated Power
+20.0 dBm
c
Transmitter → T-RIS Free Space Path Loss
−79.5 dB
d
T-RIS Array Gain
+29.1 dBi
e
Sideband Modulation Loss |T_sb|²
−7.17 dB
f
Inter-stage Near Field Coupling |C₀|²
−10.2 dB
g
T-RIS → Target Path Loss
−81.4 dB
h
R-RIS Coherent Beam Shaping Gain
+36.1 dBi
i
Rx Array Gain
+18.0 dBi
After the aforementioned 13 stages, the received signal power is approximately -99.1 dBm. The thermal noise floor under conditions of B_noise ≈ 312.5 MHz, noise figure NF = 5 dB, and T₀ = 290 K is about -84.6 dBm, corresponding to a single-symbol SNR₁ ≈ -14.5 dB.

This value may seem very low, but through coherent accumulation—M = 12 tones with N_sw = 1024 sweeps—about 30.1 dB of coherent accumulation gain can be achieved, raising the post-processing SNR to **+15.6 dB**, reaching the reliable detection threshold.
In the domain of distance-velocity, the system's unambiguous range is c / Δf ≈ 12 m, unambiguous velocity is λ / (2·T_sw) ≈ 319 m/s, and the range resolution is c / (M·Δf) ≈ 1.0 m.
VI. Signal Processing: The dual-base station beat frequency signals are organized into a M × N_sw complex matrix Y[k, n], where k is the tone index (1 to 12), and n is the sweep index (0 to 1023):
Y[k, n] = √(SNR₁) · exp(−j2π·k·Δf·τ) · exp(+j2π·f_d·n·T_sw) + w[k, n]
where τ = R/c is the double-path propagation delay, f_d = 2v_r/λ is the Doppler shift, and w ~ CN(0, 1) is the circularly symmetric Gaussian white noise.
Distance Processing: An IFFT is performed on the tone axis (k direction), and the power average of the N_sw sweeps is taken to obtain a one-dimensional distance image. The target peak clearly appears at the real distance R = 10 m.
Doppler Processing: A FFT is performed on the peak of the distance image in the sweep axis (n direction), obtaining a two-dimensional distance-Doppler (R-D) spectrum. The target appears as a concentrated bright spot on the R-D plane, with its radial velocity being precisely extracted without the distance offset commonly seen in FMCW systems.
VII. Statistical Estimation Theory: Cramer-Rao Lower Bound (CRLB) and Monte Carlo Verification System
The Fisher Information Matrix (FIM) for distance R, radial velocity v_r, and two-dimensional position is theoretically derived from the Cramer-Rao lower bound (CRLB). 
For distance estimation, the time delay component of the FIM is:  
I_ττ = SNR_eff · (2π)² · Var[k·Δf] where SNR_eff = SNR₁ · M · N_sw is the effective signal-to-noise ratio after coherent combining, and Var[k·Δf] is the second central moment of the tone frequency. The CRLB_R = (c/2) · √(1/I_ττ).  
For velocity estimation, the Doppler component of the FIM is:  
I_ff = SNR_eff · (2π)² · Var[n·T_sw] where Var[n·T_sw] is the second central moment of the sweeping time axis.  
Monte Carlo simulations (1000 independent trials per SNR point) within a single symbol SNR scan range of -30 dB to +20 dB indicate that at the system operating point SNR₁ = -14.5 dB, the ratio of the RMSE of distance to the CRLB (estimation efficiency η_R) approaches 1.0, verifying the asymptotic effectiveness of the IFFT peak detector under low SNR conditions. After SNR exceeds 0 dB, the RMSE of distance is limited by the Fourier resolution baseline of ΔR = 1.0 m.  
Velocity estimation presents a tripartite characteristic: in the low SNR region, it is dominated by noise (RMSE far greater than CRLB), in the middle SNR region, it closely tracks the CRLB curve, and in the high SNR region, it is constrained by the precision limit of the FFT speed resolution Δv.  
The range of the two-dimensional position CRLB varies from centimeter level (high SNR end) to decimeter level (low SNR end), determined by geometric projection from both the distance CRLB and the angle CRLB.
VIII. Closed-loop Beam Tracking: An α-smoothing filter coupled with point-to-error system realizes a closed-loop tracking loop based on an α-smoothing filter, spanning 50 CPI frames (total time duration approximately 0.86 s). The target moves at a constant velocity of v = 2 m/s along a 45° direction, with its true trajectory given by P(t) = P₀ + t · T_CPI · V.
In each CPI frame, the beam pointing error leads to degradation in the effective signal-to-noise ratio (SNR), which is precisely modeled by the Dirichlet kernel gain of a uniform linear array (ULA):
G(Δψ) = [sin(N·Δψ/2) / (N·sin(Δψ/2))]² where Δψ is proportional to the difference between the sinusoidal deviation of the beam pointing angle and the actual angle. The degradation in gain is fed back to the FIM, dynamically expanding the current frame's angle and distance estimation variance, capturing the closed-loop coupling from "pointing error → SNR reduction → variance increase → tracking accuracy decline".
The update rule for the α-smoothing filter is:
θ̃_k = α · θ̂_k + (1−α) · θ̃_{k−1} where θ̂_k is the noise angle measurement of the current frame, and θ̃_k is the smoothed beam command after filtering. The smoothing coefficient α = 0.3. Simulation results show that during a 50-frame tracking process, the RMSE of two-dimensional position is about centimeters, and the 95th percentile position error can be controlled within a reasonable range (typically centimeters). The tracking trajectory closely matches the true path, and the angle commands smoothly track the target's azimuth angle changes.
IX. Multi-Object Decoupling: A comparison of M-FSK and FMCW in multi-object scenarios deployed three colocated targets: T1 (stationary, R = 4.0 m), T2 (far away, R = 8.5 m, v = +1.5 m/s), and T3 (nearby, R = 6.0 m, v = −1.0 m/s).
In the M-FSK mode, the distance peaks of three targets appear precisely at their actual distances, distinct from each other without being affected by the radial velocity of the targets. This is because the distance coding relies solely on the phase difference between tones, completely decoupling from Doppler.
Equivalent FMCW mode (with the same bandwidth B = 300 MHz and the same CPI duration): The coupling effect of distance-Doppler leads to significant displacement of the T2 and T3 distance peaks: ΔR ≈ v * fc * T_sw / B ≈ 1.6 m/(m/s). T2 appears offset approximately 2.4 m to the right of its true distance, while T3 is offset approximately 1.6 m to the left. This comparison vividly demonstrates the core advantage of the M-FSK waveform in motion multi-target scenarios—it completely eliminates ghost target effects caused by coupling.
Tenth, System-Level Benchmark Tests: A normalized quantitative comparison module for four types of radar/perception architectures was developed:
Architecture  
Symbol Rate  
Total Power Consumption  
Distance Resolution  
Beam Diversity  
Two-level T-RIS/R-RIS (This scheme)  
714.3 kHz  
≈3.8 W  
1.0 m  
High  
Single-level STM-RIS (SPI baseline)  
13.0 kHz  
≈3.6 W  
1.0 m  
Medium  
Traditional FMCW radar  
—  
≈2.1 W  
0.5 m  
High  
AESA phased array radar  
—  
≈4.7 W  
0.15 m  
Extremely High
Power consumption breakdown: The power of the two-tier solution is mainly consumed in T-RIS and R-RIS PIN biasing (about 0.512 W each), PA driving (about 0.5 W), DDS dynamic switching (about 0.022 W) and DSP+ADC processing (about 0.55 W). Compared with the AESA active transceiver channel (15 mW × 256 channels + RF link 0.87 W, total about 4.71 W), the two-tier RIS solution has a total power advantage of about 19%.
Waveform jitter (measured in programmable symbol rate) is the most prominent highlight of the two-tier solution: its 714.3 kHz DDS-driven symbol rate far exceeds the 13.0 kHz bottleneck of a single-tier STM-RIS, achieving a 55x acceleration. This advantage directly stems from the physical decoupling of waveform modulation and beam shaping in the two-tier architecture.
XI. Robustness Analysis: Hardware Tolerance Scan Robustness Simulation Module systematically scans the impact of four categories of hardware non-ideal factors on estimation accuracy:
1  
Amplitude error (Gaussian spread between units, standard deviation 2% of mean): The impact on estimation accuracy is within acceptable range.
2  
Phase error (standard deviation 3°): introduces a slight sidelobe lift, but the main lobe gain loss is limited.
3  
Inter-level gap deviation (deviation from nominal value of 2 mm): The exponential decay characteristic of the Shui Sheng coupling coefficient makes the gap deviation be mapped into coupling loss with a sensitivity of approximately 5.4 dB/mm, which requires strict control in mechanical assembly.
4  
Phase Quantization Error (R-RIS 3-bit quantization): The phase resolution of 3-bit (8 levels) is sufficient to approximate the beamforming performance of continuous phases in theoretical analysis, with a quantization loss less than 0.2 dB.
The above scanning results provide a quantitative tolerance budget for the system's manufacturability, clarifying the acceptable tolerance range for each process parameter.
Twelfth, Conclusion and Outlook This paper systematically introduces the complete technical route for "two-tier RIS + M-FSK" dual-baseline perception simulation. The engineering value of this work lies in: replacing timing scheduling with physical decoupling, maintaining the inherent advantages of low cost and low power of RIS, while enhancing waveform flexibility by nearly two orders of magnitude.
Several core observations from the technical aspect can be summarized as follows:
The two-tier architecture is an effective way to break through the bottleneck of single-tier RIS SPI, and the introduction of DDS internal clock boundaries has enabled symbol rates to leap from 13 kHz to 714 kHz.
M-FSK waveforms have a natural capability for distance-Doppler decoupling in multi-target motion scenarios, outperforming traditional FMCW systems.
The system's post-processing signal-to-noise ratio has been improved from −14.5 dB to +15.6 dB through coherent accumulation (approximately 30 dB gain), enabling centimeter-level parameter estimation.
The closed-loop α-smoothing algorithm establishes a physically consistent coupling model between beam pointing error and estimated variance, providing a reliable theoretical foundation for tracking filter design.
System-level comparisons show that this scheme simultaneously outperforms both single-tier STM-RIS and AESA phased array solutions in terms of power efficiency and waveform agility.

## Images

![Two-tier reconfigurable smart surface M-FSK dual-baseline perception system: full-linkage simulation from PIN diode physical to closed-loop tracking [with Python code]](01.jpg)
![Two-tier reconfigurable smart surface M-FSK dual-baseline perception system: full-linkage simulation from PIN diode physical to closed-loop tracking [with Python code]](02.jpg)
![Two-tier reconfigurable smart surface M-FSK dual-baseline perception system: full-linkage simulation from PIN diode physical to closed-loop tracking [with Python code]](03.jpg)
![Two-tier reconfigurable smart surface M-FSK dual-baseline perception system: full-linkage simulation from PIN diode physical to closed-loop tracking [with Python code]](04.jpg)
![Two-tier reconfigurable smart surface M-FSK dual-baseline perception system: full-linkage simulation from PIN diode physical to closed-loop tracking [with Python code]](05.jpg)
![Two-tier reconfigurable smart surface M-FSK dual-baseline perception system: full-linkage simulation from PIN diode physical to closed-loop tracking [with Python code]](06.jpg)
![Two-tier reconfigurable smart surface M-FSK dual-baseline perception system: full-linkage simulation from PIN diode physical to closed-loop tracking [with Python code]](07.jpg)
![Two-tier reconfigurable smart surface M-FSK dual-baseline perception system: full-linkage simulation from PIN diode physical to closed-loop tracking [with Python code]](08.jpg)
![Two-tier reconfigurable smart surface M-FSK dual-baseline perception system: full-linkage simulation from PIN diode physical to closed-loop tracking [with Python code]](09.jpg)
![Two-tier reconfigurable smart surface M-FSK dual-baseline perception system: full-linkage simulation from PIN diode physical to closed-loop tracking [with Python code]](10.jpg)
![Two-tier reconfigurable smart surface M-FSK dual-baseline perception system: full-linkage simulation from PIN diode physical to closed-loop tracking [with Python code]](11.jpg)
![Two-tier reconfigurable smart surface M-FSK dual-baseline perception system: full-linkage simulation from PIN diode physical to closed-loop tracking [with Python code]](12.jpg)
![Two-tier reconfigurable smart surface M-FSK dual-baseline perception system: full-linkage simulation from PIN diode physical to closed-loop tracking [with Python code]](13.jpg)
![Two-tier reconfigurable smart surface M-FSK dual-baseline perception system: full-linkage simulation from PIN diode physical to closed-loop tracking [with Python code]](14.jpg)

<!-- PAYMENT_BLOCK -->

## Get this dataset

- Payment link (Stripe): [Buy now](https://buy.stripe.com/3cs8yP7sY87d0vu9AB)
- Payment QR code: ![Payment QR code](pay.png)

<!-- /PAYMENT_BLOCK -->
