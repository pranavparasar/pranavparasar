## Hi there 👋
clc;
clear all;
close all;
## -------------------------------------------------------------
#  DTMF TONE GENERATION AND DETECTION USING GOERTZEL ALGORITHM
#   Author: PRANAV PARASAR
##   -------------------------------------------------------------
#Sampling frequency
fs = 8000;                 
t = 0:1/fs:0.5;            # 0.5 sec tone duration

# DTMF Frequency Table
low_freq  = [697 770 852 941];
high_freq = [1209 1336 1477 1633];

# Keypad definition matrix
keys = ['1','2','3';
        '4','5','6';
        '7','8','9';
        '*','0','#'];

# Frequency mapping matrix
DTMF_low = [697 697 697;
            770 770 770;
            852 852 852;
            941 941 941];

DTMF_high = [1209 1336 1477;
             1209 1336 1477;
             1209 1336 1477;
             1209 1336 1477];

## -------------------------------------------------------------
#  USER INPUT (You can change the key to generate)
## -------------------------------------------------------------
key = input('Enter a DTMF Key (0-9, *, #): ','s');

# Find key location in matrix
[row, col] = find(keys == key);

# Corresponding frequencies
f1 = DTMF_low(row, col);
f2 = DTMF_high(row, col);

fprintf('Key Pressed: %s -> Frequencies: #d Hz & %d Hz\n', key, f1, f2);

## -------------------------------------------------------------
#   GENERATE DTMF SIGNAL
## -------------------------------------------------------------
x = sin(2*pi*f1*t) + sin(2*pi*f2*t);

figure;
plot(t(1:400), x(1:400));  # Show only first 400 samples
title(['DTMF Tone for Key: ', key]);
#PRANAV PARASAR
xlabel('Time (s)');
ylabel('Amplitude');

sound(x, fs);   # Play the tone

## -------------------------------------------------------------
#  DTMF DETECTION USING GOERTZEL ALGORITHM
## -------------------------------------------------------------
N = length(x);
# All 8 DTMF frequencies
freqs = [low_freq high_freq];
num_freqs = length(freqs);

power_vals = zeros(1,num_freqs);

for k = 1:num_freqs
    % index of frequency
    w = 2*pi*freqs(k)/fs;
    # Goertzel coefficients
    cosine = cos(w);
    coeff = 2*cosine;
    
   s_prev = 0;
    s_prev2 = 0;
    for n = 1:N
        s = x(n) + coeff*s_prev - s_prev2;
        s_prev2 = s_prev;
        s_prev  = s;
    end
    # Magnitude of frequency bin
    power_vals(k) = s_prev2^2 + s_prev^2 - coeff*s_prev*s_prev2;
end
## -------------------------------------------------------------<img width="892" height="659" alt="image" src="https://github.com/user-attachments/assets/b2623d2b-e56c-48f5-b526-42f438192268" />
<img width="888" height="697" alt="image" src="https://github.com/user-attachments/assets/12283a54-53d6-495a-a61e-e5519da09e8a" />

#   DETECT TWO STRONGEST FREQUENCIES
## -------------------------------------------------------------
[~, idx] = maxk(power_vals, 2);
det_low  = min(freqs(idx));
det_high = max(freqs(idx));
fprintf('\nDetected Low Freq : #d Hz\n', det_low);
fprintf('Detected High Freq: #d Hz\n', det_high);

## -------------------------------------------------------------
#   IDENTIFY THE KEY PRESSED
## -------------------------------------------------------------
det_key = '';
for i = 1:4
    for j = 1:3
        if DTMF_low(i,j)==det_low && DTMF_high(i,j)==det_high
            det_key = keys(i,j);
        end
    end
end
fprintf('\nDTMF Detection Result: %s\n', det_key);
<img width="800" height="701" alt="image" src="https://github.com/user-attachments/assets/a403fec1-7cfa-4d1a-94a7-4ed68b35ebf3" />
<img width="817" height="652" alt="image" src="https://github.com/user-attachments/assets/a7295559-cd85-4639-9302-597d5f27efce" />
<img width="757" height="649" alt="image" src="https://github.com/user-attachments/assets/cf02e68a-1093-4955-af38-2116b5bcaaaa" />
<img width="835" height="291" alt="image" src="https://github.com/user-attachments/assets/eb79572a-9b84-48ee-8824-8c62a6937352" />
