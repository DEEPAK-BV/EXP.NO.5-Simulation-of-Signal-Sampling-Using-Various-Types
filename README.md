5.Simulation of Signal Sampling Using Various Types such as
    i) Ideal Sampling
    ii) Natural Sampling
    iii) Flat Top Sampling

# AIM
  To perform experimental verification of signal sampling using various types.

# SOFTWARE REQUIRED
  Google Colab

# ALGORITHMS

1. Import Libraries and Define Original Signal:
Import necessary libraries: numpy and matplotlib.pyplot. Define original signal parameters: sampling frequency, time array, signal frequency, and signal amplitude.

2. Define Sampling Parameters:
Define sampling frequency and time array for sampling the original signal.

3. Sample Original Signal:
Sample the original signal using the defined sampling parameters to obtain the sampled signal.

4. Reconstruct Sampled Signal:
Reconstruct the sampled signal using a reconstruction technique, such as zero-order hold or linear interpolation.

5. Plot Results:
Plot the original signal, sampled signal, and reconstructed signal using matplotlib.pyplot to visualize the results.
# PROGRAM
**i) Ideal Sampling**

#Impulse Sampling or ideal sampling
import numpy as np
import matplotlib.pyplot as plt
from scipy.signal import resample
fs = 100
t = np.arange(0, 1, 1/fs) 
f = 5
signal = np.sin(2 * np.pi * f * t)
plt.figure(figsize=(10, 4))
plt.plot(t, signal, label='Continuous Signal')
plt.title('Continuous Signal (fs = 100 Hz)')
plt.xlabel('Time [s]')
plt.ylabel('Amplitude')
plt.grid(True)
plt.legend()
plt.show()
t_sampled = np.arange(0, 1, 1/fs)
signal_sampled = np.sin(2 * np.pi * f * t_sampled)
plt.figure(figsize=(10, 4))
plt.plot(t, signal, label='Continuous Signal', alpha=0.7)
plt.stem(t_sampled, signal_sampled, linefmt='r-', markerfmt='ro', basefmt='r-', label='Sampled Signal (fs = 100 Hz)')
plt.title('Sampling of Continuous Signal (fs = 100 Hz)')
plt.xlabel('Time [s]')
plt.ylabel('Amplitude')
plt.grid(True)
plt.legend()
plt.show()
reconstructed_signal = resample(signal_sampled, len(t))
plt.figure(figsize=(10, 4))
plt.plot(t, signal, label='Continuous Signal', alpha=0.7)
plt.plot(t, reconstructed_signal, 'r--', label='Reconstructed Signal (fs = 100 Hz)')
plt.title('Reconstruction of Sampled Signal (fs = 100 Hz)')
plt.xlabel('Time [s]')
plt.ylabel('Amplitude')
plt.grid(True)
plt.legend()
plt.show()

# OUTPUT
![image](https://github.com/user-attachments/assets/c601094b-04b7-4f33-b8f0-362dd83f0c6c)

# PROGRAM
  **ii) Natural Sampling**
  import numpy as np
import matplotlib.pyplot as plt
from scipy.signal import butter, lfilter


fs = 1000  # Sampling frequency (samples per second)
T = 1  # Duration in seconds
t = np.arange(0, T, 1 / fs)  # Time vector
fm = 5  # Frequency of message signal (Hz)
message_signal = np.sin(2 * np.pi * fm * t)
pulse_rate = 50  # pulses per second
pulse_train = np.zeros_like(t)
pulse_width = int(fs / pulse_rate / 2)
for i in range(0, len(t), int(fs / pulse_rate)):
    pulse_train[i:min(i + pulse_width, len(t))] = 1  # Corrected pulse width handling
nat_signal = message_signal * pulse_train
def lowpass_filter(signal, cutoff, fs, order=5):
    nyquist = 0.5 * fs
    normal_cutoff = cutoff / nyquist
    b, a = butter(order, normal_cutoff, btype='low', analog=False)
    return lfilter(b, a, signal)

reconstructed_signal = lowpass_filter(nat_signal, 10, fs) # apply low pass filter to the naturally sampled signal.

plt.figure(figsize=(14, 10))
plt.subplot(4, 1, 1)
plt.plot(t, message_signal, label='Original Message Signal')
plt.legend()
plt.grid(True)
plt.subplot(4, 1, 2)
plt.plot(t, pulse_train, label='Pulse Train')
plt.legend()
plt.grid(True)
plt.subplot(4, 1, 3)
plt.plot(t, nat_signal, label='Natural Sampling')
plt.legend()
plt.grid(True)
plt.subplot(4, 1, 4)
plt.plot(t, reconstructed_signal, label='Reconstructed Message Signal', color='green')
plt.legend()
plt.grid(True)

plt.tight_layout()
plt.show()

# OUTPUT
![image](https://github.com/user-attachments/assets/d94f25c8-c123-4d9e-9c85-24ade2359cc4)

# PROGRAM
 **iii) Flat Top Sampling**
 # PROGRAM
import numpy as np
import matplotlib.pyplot as plt
from scipy.signal import butter, lfilter

fs = 1000  # Sampling frequency (samples per second)
T = 1  # Duration in seconds
t = np.arange(0, T, 1/fs)  # Time vector

fm = 5  # Frequency of message signal (Hz)
message_signal = np.sin(2 * np.pi * fm * t)


pulse_rate = 50  # pulses per second
pulse_train_indices = np.arange(0, len(t), int(fs / pulse_rate))
pulse_train = np.zeros_like(t)

pulse_train[pulse_train_indices] = 1

flat_top_signal = np.zeros_like(t)
sample_times = t[pulse_train_indices]
pulse_width_samples = int(fs / (2 * pulse_rate)) # Adjust pulse width as needed

for i, sample_time in enumerate(sample_times):
    index = np.argmin(np.abs(t - sample_time))
    if index < len(message_signal):
        sample_value = message_signal[index]
        start_index = index
        end_index = min(index + pulse_width_samples, len(t))
        flat_top_signal[start_index:end_index] = sample_value

def lowpass_filter(signal, cutoff, fs, order=5):
    nyquist = 0.5 * fs
    normal_cutoff = cutoff / nyquist
    b, a = butter(order, normal_cutoff, btype='low', analog=False)
    return lfilter(b, a, signal)

cutoff_freq = 2 * fm  # Nyquist rate or slightly higher
reconstructed_signal = lowpass_filter(flat_top_signal, cutoff_freq, fs)

plt.figure(figsize=(14, 10))

plt.subplot(4, 1, 1)
plt.plot(t, message_signal, label='Original Message Signal')
plt.title('Original Message Signal')
plt.xlabel('Time (s)')
plt.ylabel('Amplitude')
plt.legend()
plt.grid(True)

plt.subplot(4, 1, 2)
plt.stem(t[pulse_train_indices], pulse_train[pulse_train_indices], basefmt=" ", label='Ideal Sampling Instances')
plt.title('Ideal Sampling Instances')
plt.xlabel('Time (s)')
plt.ylabel('Amplitude')
plt.legend()
plt.grid(True)

plt.subplot(4, 1, 3)
plt.plot(t, flat_top_signal, label='Flat-Top Sampled Signal')
plt.title('Flat-Top Sampled Signal')
plt.xlabel('Time (s)')
plt.ylabel('Amplitude')
plt.grid(True)
plt.legend()

plt.subplot(4, 1, 4)
plt.plot(t, reconstructed_signal, label=f'Reconstructed Signal (Low-pass Filter, Cutoff={cutoff_freq} Hz)', color='green')
plt.title('Reconstructed Signal')
plt.xlabel('Time (s)')
plt.ylabel('Amplitude')
plt.legend()
plt.grid(True)

plt.tight_layout()
plt.show()
# OUTPUT
![image](https://github.com/user-attachments/assets/badef113-cb41-4175-be42-1993113b9b71)

 
# RESULT / CONCLUSIONS

Thus the given eperiment ideal sampling ,natural sampling,flat top sampling has been verified successfully by using python






