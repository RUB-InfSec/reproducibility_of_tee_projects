# "They don't care about this": A Systematic Study of TEE Build Reproducibility in the Wild

Trusted Execution Environments (TEEs) have become a cornerstone of modern cloud computing, providing strong confidentiality and integrity guarantees for both code and data. A critical component of this trust model is remote attestation, which enables external entities to verify the authenticity and integrity of code executing within a TEE through cryptographic measurements. However, the effectiveness of remote attestation fundamentally depends on the verifier's ability to trace the reported measurement back to the original source code---a property that can only be guaranteed through reproducible builds.

This artifact accompanies our paper, which investigates how reproducible TEE builds are in practice. We analyzed 115 TEE deployments across Intel SGX, Intel TDX, and AMD SEV, finding that 91% were not reproducible, with 80% failing to provide even the two basic prerequisites: source code and a reference build. To investigate root causes, we interviewed 12 developers from industry and academia. Only one reported that reproducibility is a priority during development. Beyond technical barriers such as timestamps embedded in binaries, we identify ecosystem-level challenges — including the lack of build environment control in multi-stakeholder projects — and argue that reproducibility requires a holistic approach and stronger community commitments.

The artifact covers the technical analysis only. Interview data is not included for privacy reasons; the recruiting email, interview guide, and codebook are available in the [preprint on arXiv](https://arxiv.org/abs/2609.11411). 


## Artifact Contents

1. `results.csv` **- Manual analysis results.** A table detailing the results of our manual analysis of 232 applications from the [Awesome SGX Open Source](https://github.com/Maxul/Awesome-SGX-Open-Source/commit/008a9e657a53b0aa5acbcbfe01b27197172f29b6) repository and the 35 confidential computing products from the [Google Cloud Platform](https://console.cloud.google.com/marketplace) and [Microsoft Azure](https://azuremarketplace.microsoft.com/) marketplaces, as of March 24, 2026. For each application, the table records the criteria used to select the 115 deployments in our final dataset, and whether each project provides source code, a reference build, and reproducible binaries. See [Data Description](#data-description) for a summary of the columns.

2. `build_instructions/` **- Reproducibility test instructions.** Step-by-step instructions for the manual reproducibility tests we ran on each of the 27 applications examined in depth in the paper. Each file explains how we retrieved the reference measurement, how we compiled the project, and (where applicable) how we rebuilt it after receiving additional information from developers or maintainers.

3. **Processing script** (`evaluation.ipynb`) **:** A Jupyter notebook that parses `results.csv` and computes the tables and Fisher tests reported in Section 3 of the paper. Results were obtained using [R version 4.5.3](https://cran.r-project.org/src/base/R-4/) and Python 3.10.7 with the packages listed in `requirements.txt`.


## Data Description

| Column | Description |
|--------|-------------|
| `name` | Name of the application or product |
| `link` | URL to the project repository or marketplace listing |
| `source` | Whether the project originates from the Awesome SGX repository  (`Awesome`) or a cloud marketplace (`Azure`, `GCP`) |
| `activity` | Activity level of the project (e.g., actively maintained or archived) |
| `stability` | Stability status of the project (e.g., whether it is a prototype or production-grade) |
| `purchasable` | Whether the product requires purchase or is freely available; applicable only to marketplace products |
| `tee_type` | The TEE technology used (e.g., Intel SGX, Intel TDX, AMD SEV) |
| `project_type` | Whether the application is of a commercial or academic nature |
| `build_system` | The build system or toolchain used (e.g., Nix, Bazel, Yocto, Docker image, Dockerfile, or None) |
| `source_code` | Whether the project provides publicly accessible source code |
| `build_instructions` | Whether the project provides instructions for building from source |
| `reference` | Whether the project provides a reference build or measurement to verify against |
| `compiled` | Whether we were able to successfully compile the project from source |
| `reproduced` | Whether we were able to reproduce the reference measurement from source, containing the suffix `indep` if the project was reproduced without support from the developers |
| `replied` | Whether the developers or maintainers replied when contacted |


## Getting Started

Install the required Python packages and launch the notebook:

```bash
pip install -r requirements.txt
jupyter notebook evaluation.ipynb
```

Note that the Fisher tests in the notebook require [R](https://www.r-project.org/) to be installed and accessible on your PATH.


## Contact

For questions, please contact the corresponding author at the address listed on the arXiv preprint.


## License

This repository is licensed under the [MIT license](/LICENSE).


## Citation

```
@misc{wilde2026theydontcarethis,
      title={"They don't care about this": A Systematic Study of TEE Build Reproducibility in the Wild}, 
      author={Annika Wilde and Marco Gutfleisch and Felix Reichmann and Anirban Chakraborty and Yuval Yarom and M. Angela Sasse and Ghassan Karame},
      year={2026},
      eprint={2609.11411},
      archivePrefix={arXiv},
      primaryClass={cs.CR},
      url={https://arxiv.org/abs/2609.11411}, 
}
```