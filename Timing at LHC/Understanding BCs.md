# Understanding BCs

# LHC timing signals and their use in MFT readout
 
## a. Units of LHC timing signals used in readout

The bunch clock (BC) frequency is in sync with LHC RF (Radio Frequency) generators. 

BC
- frequency: 40.07897 MHz
- period: 24.95 ns <=> 1 BC

Orbit <=> HBF
- frequency: 11.246 kHz
- period: 88.922 µs <=> 3564 BC

**Orbit = time taken by the beam to make one rotation in the LHC (27km circonference)**

TF
- 1 TF = 128 LHC orbits
- frequency: 87.858 Hz
- period: 11.382 ms

_TF_: Time Frame, used to cut data into chunks, to be able to distribute them for parallel processing in the EPN farm. The value is arbitrarily chosen by ALICE. It is divided into Heart Beat Frames (HBFs). The CTP (Central Trigger Processor) configuration can be customised to reject some of the HBF (hence not propagating them to the acquisition system). The accepted ones are named HBa, and the rejected ones HBr.

## b. Possible congruences (3564/$n$) on the orbit:

The divider $n$ will give the number of frames per orbit when the periodic trigger that leads to the opening of a strobe window in the sensors is set with the corresponding value of period in BC.

| Divider $n$ | Period (BC) | Frequency (kHz) | Period (µs) |
| -----------:| -----------:| ---------------:| -----------:|
|           1 |        3564 |          11.246 |      88.922 |
|           2 |        1782 |          22.492 |      44.461 |
|           3 |        1188 |          33.738 |      29.641 |
|           4 |         891 |          44.983 |      22.231 |
|           6 |         594 |          67.475 |      14.829 |
|           9 |         396 |         101.213 |       9.880 |
|          12 |         297 |         134.950 |       7.410 |
|          18 |         198 |         202.425 |       4.940 |
|          22 |         162 |         247.408 |       4.042 |

NB : Il a des synonymes dans le vocabulaire utilisé selon qu’on est fan de O2 ou de l'ALPIDE: readout frame = strobe window