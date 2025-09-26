I have attempted to reproduce the results from your paper, specifically Figure 19, which details the RMSE performance of the proposed CS-based velocity estimation algorithm as a function of SNR.

To do this, I utilized the publicly available code from this repository and wrote a simple script to simulate the performance curve. I must emphasize that I have made no modifications to your provided functions `joint_test_CS_v.m` and `rmse_results_2DFFT`).

To my surprise, the results from my simulation show a massive discrepancy compared to the published figure. The performance of the provided code is exceptionally poor, underperforming even a basic 2D-FFT algorithm and failing completely to match the claims made in your paper.

I have reviewed your code against the algorithm described in the paper, and while the implementation appears to follow the specified workflow, the outcome is not as expected. This leads me to question the validity of combining the proposed signal model with the CS algorithm. **Frankly, I highly suspect the results in Fig. 19 are fabricated.** It is illogical for the estimation performance of a CS algorithm, which is known to be sensitive to noise, to converge at an SNR as low as -20 dB.

I have attached my wrapper script and the resulting plot for your review. I require a reasonable explanation for why the public code fails to reproduce the results claimed in your paper.

<img width="516" height="532" alt="Image" src="https://github.com/user-attachments/assets/195a3f44-0882-4a95-bcc9-cdccba25fdd2" />

```
%% 1. Setting
SNR_vec = -25:5:10;         % dB
f_c1 = 24*10^9;             % Hz
Nsymbols1 = 28;             % OFDM
Ncarriers1 = 256;           % SC
delta_f1 = 120*10^3;        % delta_f


% rmse_results = zeros(size(SNR_vec));
rmse_results_2DFFT = zeros(size(SNR_vec));

%% 2. Simul
for i = 1:length(SNR_vec)
    current_snr = SNR_vec(i);
    
    rmse_results(i) = joint_test_CS_v(current_snr, f_c1, Nsymbols1, Ncarriers1, delta_f1);
    rmse_results_2DFFT(i) = joint_test_v(current_snr, f_c1, Nsymbols1, Ncarriers1, delta_f1);
    
end


%% 3. Pic
figure;
semilogy(SNR_vec, rmse_results, 'o-', 'LineWidth', 1, 'MarkerSize', 8);hold on;
semilogy(SNR_vec, rmse_results_2DFFT, 'b--', 'LineWidth', 1.5, 'MarkerSize', 8);

grid on;
xlabel('SNR, dB');
ylabel('RMSE, m/s');
legend('CS-RMSE',"2DFFT-RMSE");

```
