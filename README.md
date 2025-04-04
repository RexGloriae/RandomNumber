# Implementation of a True Random Number Generator (TRNG)
## Overview
The purpose of this project is to write an implementation of a TRNG, using sources of natural origin, _id est_ pictures of the Sun from the Solar and Heliospheric Observatory (SOHO), and radio feeds from around the world.
## Features
- Uses real-world entropy sources
- Supports random *Raw Data* generation
## Entropy sources
- Images of the Sun captured by SOHO in different spectral filters
- Radio channel data from different radios around the world
## Security
The data used for testing can be found in `./Results/data.bin`. Additionally, in the same directory a report file produced by the NIST test suite can be found under the name `report.txt`.

Different pictures of statistical results and a histogram can be found in the formerly mentioned directory.
## Setup
### Clone the repository
Clone the repository using `git clone https://github.com/RexGloriae/RandomNumber`.
### Install dependencies
Install the dependencies using `pip install -r requirements.txt`.
### Usage
Run the script using `python3 ./main.py`.
#### Note
Based on the size of data you need to generate, modify in `main.py` the variable `total_numbers`. Multiply `total_numbers` by 2 (`total_numbers * 2`) to get the number of bytes about to be generated. The variable `batch_size` refers to the number of splits done in raw data to calculate live-feed entropy. It is recommended to be a value somewhere between `1\4` or `1\8` of the `total_numbers` value.
## Algorithm
The algorithm uses two main seeds and a secondary one to generate random raw data.

1. Four pictures of the Sun from SOHO are downloaded, with the following filters applied: HMI171, 0131, 211193171n and 0171. Afterwards, two different arrays are generated from each picture: one with RGB values, selecting a pixel in a pseudo-random way, and one with the Fast Fourier Transform of the picture. That way, we get 8 different arrays, which are concatenated after applying some XOR operations. The final array is being used by selecting every 20 values, generating a single value after applying Bernstein's hash alghoritm, which is therefore used as a base for the actual generation. The base data is being updated every 20 minutes, as SOHO uploads new pictures approximately every 15 minutes.

2. Five seconds are being recorder from two different radio stations around the world, available from the module *pyradio*. The values extracted from each radio are concatenated. The following proccess is similar with the one used to select values from the Sun data, but every 50 values are selected. The data is being updated after every 50-value sub-string has been exhausted from the array.

3. A hardware seed is being used for both the generation alghoritm and for specific pseudo-random values, *exempli gratia* choosing an index for a specific radio station. This value is being generated in real-time, as it is needed along the way.

For each call of the *generate()* function, both a value from the first source and one from the second one are provided, following the 20-value hash and 50-value hash proccess explained above. Then four rounds of hashing, using the *SHA3 512* hashing alghoritm. After the first round, each resulting string is being converted into an integer and is XOR-ed with the previous result. The final result is being masked to fit in a formerly set interval and it consists of a random raw data value.

Each call of the *generate()* function produces two bytes.

## Contributions
The authors would like to give thanks to Iulian Aciobăniței, Phd. Without the proffessor's guidance, this project would have been dead.

## Authors
- Codreanu Andrei-Daniel
- Ivan Florentin-Marian
