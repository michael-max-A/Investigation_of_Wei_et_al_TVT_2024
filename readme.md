### Overview
This issue documents a serious discrepancy between the performance claimed in the paper "Multiple Reference Signals Collaborative Sensing..." (published in IEEE TVT) and the results produced by the official code provided in this repository. Furthermore, it records the author's refusal to address the issue, culminating in blocking further email communication.

### Background
I initially attempted to reproduce Figure 19 from the paper, which shows the RMSE of the proposed CS-based velocity estimation algorithm versus SNR. For this purpose, I used the joint_test_CS_v.m function from the authors' repository `https://github.com/echo-stack/Multiple-RS-Collaborative-Sensing-for-ISAC` without any modification, called from a simple wrapper script to iterate through SNR values.

The results were not even remotely close to those published. The performance of the official code was exceptionally poor, significantly underperforming the claims made in the paper.

###Communication with the Author
I contacted the first author, Dr. Zhiqing Wei, via email to report this discrepancy and seek a reasonable explanation. After an initial exchange, I sent a follow-up email detailing my concerns with the methodology and the substantial performance gap.

The points raised in my final email were:

+ Incorrect Claims of Superiority: The author's own simulation data (sent in the exchange) showed the CS algorithm was only marginally better than a standard 2D-FFT in a negligible SNR window (-19dB to -17dB) and inferior everywhere else. Attached below are the simulation plots—one provided by the author via email, and one from my own replication. As is evident from the data, the performance advantage of the CS algorithm is completely negligible. Nevertheless, the author shamelessly claims superior performance for their algorithm.

<img width="2187" height="1059" alt="Image" src="https://github.com/user-attachments/assets/ef20f470-afc4-4cdb-9157-676746814014" />

<img width="1191" height="474" alt="Image" src="https://github.com/user-attachments/assets/98910c5c-deee-42eb-8ece-a445489ba5cf" />

+ Misrepresentation of Standard Techniques: The paper presents the "accumulate-then-detect" method as a key innovation, whereas it is a common-sense baseline for 2D-FFT.

+ Unjustifiable Computational Cost: The algorithm runs over 250 times slower than the 2D-FFT baseline, with no meaningful performance gain.

In this email, I urged the author to consider submitting an erratum  to IEEE TVT to maintain academic integrity.

### Current Situation
Following my last email, the author has blocked my email address, effectively refusing any further communication on this matter. A screenshot of the delivery failure notification is attached.

<img width="1290" height="575" alt="Image" src="https://github.com/user-attachments/assets/1318490a-085b-46ef-8e62-e64b253e8be9" />


### Call to the Community

Given the author's refusal to engage, I am now raising this issue publicly. The significant mismatch between the published claims and the provided code, coupled with the author's subsequent actions, points to serious issues that undermine the credibility of this work.

I believe this requires scrutiny from the wider research community. Reproducibility is a cornerstone of scientific progress, and claims made in peer-reviewed publications must be verifiable.

I am attaching my wrapper script and the plot generated from the official code for full transparency. I invite others to run these simulations and share their findings.

Sincerely,

Michael

### My final email sent

<img width="1938" height="914" alt="Image" src="https://github.com/user-attachments/assets/b91e8cf1-a088-4e1f-b754-f81925bcffb6" />

### Code

` joint_test_CS_v` and `joint_test_CS_v` can be found in their project

```matlab

% -------------------------------------------------------------------------
% MATLAB Script: OFDM Radar Velocity Estimation Performance and Computation Time Simulation
% -------------------------------------------------------------------------
% Description:
%   This script executes a loop simulation to evaluate and compare the
%   performance and computational efficiency of two different OFDM radar
%   velocity estimation algorithms. It calculates the Root Mean Square Error (RMSE)
%   and the required computation time for each algorithm over a range of
%   Signal-to-Noise Ratios (SNRs) and plots the results.
%
%   - Algorithm 1: 'joint_test_v' (Assumed to be the traditional or baseline method)
%   - Algorithm 2: 'joint_test_CS_v' (The Compressed Sensing based method)
%
% 
% Date: September 23, 2025
% -------------------------------------------------------------------------

%% 1. Initialize Workspace
clear;      % Clear all variables from the workspace
clc;        % Clear the command window
close all;  % Close all open figure windows

%% 2. Define Simulation Parameters
% --- Radar System Parameters ---
carrier_frequency   = 24 * 10^9;    % Carrier frequency (Hz), 24 GHz
num_symbols         = 28;           % Number of OFDM symbols per subcarrier
num_carriers        = 256;          % Number of subcarriers
subcarrier_spacing  = 120 * 10^3;   % Subcarrier spacing (Hz), 120 kHz

% --- Simulation Range Parameters ---
snr_db_range = -30:1:-17;            % Define the Signal-to-Noise Ratio (SNR) scan range (dB)
num_snr_points = length(snr_db_range); % Calculate the number of SNR points

% --- Pre-allocate Result Storage Arrays ---
% Pre-allocating memory can improve loop execution efficiency
rmse_traditional = zeros(1, num_snr_points); % Store RMSE results for the traditional algorithm
rmse_cs = zeros(1, num_snr_points);          % Store RMSE results for the Compressed Sensing algorithm
time_traditional = zeros(1, num_snr_points); % (New) Store computation time for the traditional algorithm
time_cs = zeros(1, num_snr_points);          % (New) Store computation time for the Compressed Sensing algorithm

%% 3. Execute Simulation Loop
fprintf('Starting OFDM radar velocity estimation simulation...\n');
% Loop through each defined SNR value
for i = 1:num_snr_points
    % Get the current SNR value for this loop iteration
    current_snr_db = snr_db_range(i);
    
    % Display current progress
    fprintf('Processing SNR = %.1f dB (%d / %d)\n', current_snr_db, i, num_snr_points);
    
    % --- Run and Time Traditional Algorithm ---
    tic; % Start timer
    rmse_traditional(i) = joint_test_v(current_snr_db, carrier_frequency, num_symbols, num_carriers, subcarrier_spacing);
    time_traditional(i) = toc; % Stop timer and store the elapsed time
    
    % --- Run and Time Compressed Sensing Algorithm ---
    tic; % Start timer
    rmse_cs(i) = joint_test_CS_v(current_snr_db, carrier_frequency, num_symbols, num_carriers, subcarrier_spacing);
    time_cs(i) = toc; % Stop timer and store the elapsed time
end
fprintf('Simulation finished.\n');
fprintf('Average computation time for Traditional Algorithm: %.4f seconds\n', mean(time_traditional));
fprintf('Average computation time for CS Algorithm: %.4f seconds\n', mean(time_cs));

%% 4. Performance Visualization (RMSE)
fprintf('Plotting performance curves...\n');
% Create a new figure window
figure;
% Plot the two RMSE curves using a logarithmic Y-axis
semilogy(snr_db_range, rmse_traditional, '--o', 'LineWidth', 1.5, 'MarkerSize', 6);
hold on; % Hold the current plot to draw the next curve
semilogy(snr_db_range, rmse_cs, 'd-', 'LineWidth', 1.5, 'MarkerSize', 6);
hold off; % Release the current plot

% --- Format the plot ---
% Set axis limits
axis([-30 10 1 300]); 
% Add a legend and set the best location
legend('Traditional 2D-FFT Method', 'Compressed Sensing (CS) Algorithm', 'Location', 'northeast');
% Add title and axis labels
title('OFDM Radar Velocity Estimation Performance vs. SNR', 'FontSize', 14);
xlabel('Signal-to-Noise Ratio (SNR / dB)', 'FontSize', 12);
ylabel('Root Mean Square Error (RMSE / m/s)', 'FontSize', 12);
% Add grid lines for better readability
grid on;
% Set axis font size
set(gca, 'FontSize', 10);

%% 5. Computation Time Visualization (Newly Added)
fprintf('Plotting computation time comparison...\n');
% Create a second figure window
figure;
% Plot computation time curves using a logarithmic Y-axis
semilogy(snr_db_range, time_traditional, '--o', 'LineWidth', 1.5, 'MarkerSize', 6);
hold on;
semilogy(snr_db_range, time_cs, 'd-', 'LineWidth', 1.5, 'MarkerSize', 6);
hold off;

% --- Format the plot ---
legend('Traditional 2D-FFT Method', 'Compressed Sensing (CS) Algorithm', 'Location', 'northwest');
title('Algorithm Computation Time vs. SNR', 'FontSize', 14);
xlabel('Signal-to-Noise Ratio (SNR / dB)', 'FontSize', 12);
ylabel('Single Run Computation Time (seconds)', 'FontSize', 12);
grid on;
set(gca, 'FontSize', 10);
fprintf('Script execution finished.\n');

```
