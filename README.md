## AMU_CONAINER_ENV

<div align="center">
  <img src="./logo.png" alt="Project Logo" width="300px">
</div>

**Containerized Development Environment for Amumax, Pyzfn, and Python**

![License](https://img.shields.io/github/license/kkingstoun/amu_container_env?style=flat&logo=opensourceinitiative&logoColor=white&color=0080ff)
![Last Commit](https://img.shields.io/github/last-commit/kkingstoun/amu_container_env?style=flat&logo=git&logoColor=white&color=0080ff)
![Top Language](https://img.shields.io/github/languages/top/kkingstoun/amu_container_env?style=flat&color=0080ff)
![Languages Count](https://img.shields.io/github/languages/count/kkingstoun/amu_container_env?style=flat&color=0080ff)

Built with the following tools and technologies:

![GNU Bash](https://img.shields.io/badge/GNU%20Bash-4EAA25.svg?style=flat&logo=GNU-Bash&logoColor=white)

---

## Overview

AMU_CONAINER_ENV is a highly portable, containerized development environment dedicated to running simulations and analyses using Amumax, Pyzfn, and Python. It is optimized for use in any environment supporting virtualization and is specifically designed for seamless integration with high-performance computing centers.

---

## Features

- **Amumax Integration**: Supports efficient simulations of magnetization dynamics with Amumax.
- **Python & Pyzfn**: Integrated Python environment with Pyzfn for data analysis and numerical experiments.
- **Code-Server**: Remote VS Code access in a browser, allowing a familiar development environment from anywhere.
- **GPU Acceleration**: Optional GPU support for improved simulation performance.
- **Supercomputing Ready**: Configurable for PCSS infrastructure and other HPC centers, allowing optimal binding and resource management.

---
## Project Structure

```sh
└── pcss_container_env/
    ├── code-server
    │   ├── config.yaml
    │   ├── extensions-list.txt
    │   └── settings.json
    ├── image.def
    ├── starship.toml
    ├── .zshrc
    └── start.sh
```

### Description

- **code-server/**: Contains configuration files (`config.yaml`, `extensions-list.txt`, `settings.json`) that are mounted into the container to set up and customize the VS Code environment. This allows for persistent changes to extensions, editor settings, and other customizations, making the development environment adaptable and user-friendly.
- **starship.toml**: Configuration for the terminal prompt (`starship`) used inside the container. It is mounted to ensure a consistent and personalized terminal prompt across sessions.
- **.zshrc**: Shell configuration file for Zsh. This is mounted to provide custom aliases, environment variables, and settings, ensuring the user's shell environment is consistently configured every time the container is started.

### Project Index
<details open>
	<summary><b>AMU_CONAINER_ENVTAINER_ENV/</b></summary>
	<ul>
		<li><b><a href='https://github.com/kkingstoun/amu_container_env/blob/master/starship.toml'>starship.toml</a></b>: Configuration for the terminal prompt setup.</li>
		<li><b><a href='https://github.com/kkingstoun/amu_container_env/blob/master/image.def'>image.def</a></b>: Singularity definition file to build the container image.</li>
		<li><b><a href='https://github.com/kkingstoun/amu_container_env/blob/master/start.sh'>start.sh</a></b>: Startup script to initialize the container environment.</li>
		<li><b>code-server/</b>
			<ul>
				<li><b><a href='https://github.com/kkingstoun/amu_container_env/blob/master/code-server/config.yaml'>config.yaml</a></b>: Configuration file for Code-Server settings.</li>
				<li><b><a href='https://github.com/kkingstoun/amu_container_env/blob/master/code-server/settings.json'>settings.json</a></b>: VS Code settings for extensions and environment setup.</li>
				<li><b><a href='https://github.com/kkingstoun/amu_container_env/blob/master/code-server/extensions-list.txt'>extensions-list.txt</a></b>: List of VS Code extensions to be installed in the container environment.</li>
			</ul>
		</li>
	</ul>
</details>

---
## Getting Started

### Prerequisites

Before getting started with amu_container_env, ensure your runtime environment meets the following requirements:

- **Singularity**: Ensure that Singularity is installed in your environment.
- **GPU Acceleration (Optional)**: NVIDIA drivers installed for GPU usage.

### Installation

Install amu_container_env using one of the following methods:

**Build from source:**

1. Clone the amu_container_env repository:
```sh
git clone https://github.com/kkingstoun/amu_container_env
```

2. Navigate to the project directory:
```sh
cd amu_container_env
```

3. Pull the container image using Singularity:
```sh
singularity pull --arch amd64 library://kkingstoun/amuenv/amuenv:latest
```

**Note**: You might encounter warnings regarding container verification, which can be safely ignored in most cases.

### Usage

1. Run the startup script to set up the container environment:
```sh
./start.sh
```

2. Start the Code-Server environment to work on your projects using a browser-based VS Code:
```sh
code-server
```

You will see output similar to:
```
[2024-11-08T08:06:57.639Z] info  HTTP server listening on http://0.0.0.0:8080/
[2024-11-08T08:06:57.639Z] info    - Authentication is enabled
[2024-11-08T08:06:57.639Z] info      - Using password from /opt/code-server/config/config.yaml
```
Open your browser and navigate to `http://0.0.0.0:8080/` to access the VS Code environment.

### Customizing Start Script

To bind specific directories as needed, edit the `start.sh` script. For example, to bind `/mnt/storage_2/` to make it available inside the container:

```sh
--bind /mnt/storage_2/:/mnt/storage_2/  \
```

Ensure the required bindings remain unchanged:
```sh
singularity run \
  --no-home \
  --bind /mnt/storage_2/:/mnt/storage_2/  \
  --bind "$SINIMAGE_DIR:$SINIMAGE_DIR:rw" \
  --bind ./code-server:$SINIMAGE_DIR/.local/etc/code-server:rw \
  --bind ./code-server/settings.json:$SINIMAGE_DIR/.local/share/code-server/User/settings.json \
  --home "$SINIMAGE_DIR" \
  amuenv_latest.sif
```

### GPU Support

To enable GPU access within the container, execute the following commands:

```sh
modprobe nvidia_uvm         # ENABLE GPU
```

Then, run the container with the necessary GPU bindings:

```sh
# Run the Singularity container with GPU support
singularity run \
  --nv \                    # ENABLE GPU
  ...
```


## Recommended Practice for PCSS

Due to frequent issues with read/write speeds on the PCSS storage, it is recommended to clone the repository to a local disk on the Proxima nodes:

🚀 **Executing command:**

```sh
srun -n 1 -c 20 --mem=100G -t 24:00:00 --partition=proxima --gres gpu:1 --pty /bin/bash
```

Then, create a local directory to work in:

```sh
mkdir -p /mnt/local/$(whoami)/env/
```

After creating the directory, execute the container installation instructions (git clone and Singularity commands) within this folder.

Ensure that only the necessary storage directories with data are mounted to minimize potential bottlenecks.


---
## Project Roadmap

- [X] **`Task 1`**: <strike>Initial setup of Amumax, Pyzfn, and Python environment.</strike>
- [X] **`Task 2`**: Implement GPU support verification and testing.
- [X] **`Task 3`**: Integrate additional Python libraries for enhanced data processing.
- [ ] **`Task 4`**: Add [Boris Computational Spintronics](https://www.boris-spintronics.uk/) support. 
- [ ] **`Task 4`**: Add [Neuralmag](https://gitlab.com/neuralmag/neuralmag) support. 
---

## Contributing

- **💬 [Join the Discussions](https://github.com/kkingstoun/amu_container_env/discussions)**: Share your insights, provide feedback, or ask questions.
- **🐛 [Report Issues](https://github.com/kkingstoun/amu_container_env/issues)**: Submit bugs found or log feature requests for the `amu_container_env` project.
- **💡 [Submit Pull Requests](https://github.com/kkingstoun/amu_container_env/blob/main/CONTRIBUTING.md)**: Review open PRs, and submit your own PRs.

<details closed>
<summary>Contributing Guidelines</summary>

1. **Fork the Repository**: Start by forking the project repository to your github account.
2. **Clone Locally**: Clone the forked repository to your local machine using a git client.
   ```sh
   git clone https://github.com/kkingstoun/amu_container_env
   ```
3. **Create a New Branch**: Always work on a new branch, giving it a descriptive name.
   ```sh
   git checkout -b new-feature-x
   ```
4. **Make Your Changes**: Develop and test your changes locally.
5. **Commit Your Changes**: Commit with a clear message describing your updates.
   ```sh
   git commit -m 'Implemented new feature x.'
   ```
6. **Push to github**: Push the changes to your forked repository.
   ```sh
   git push origin new-feature-x
   ```
7. **Submit a Pull Request**: Create a PR against the original project repository. Clearly describe the changes and their motivations.
8. **Review**: Once your PR is reviewed and approved, it will be merged into the main branch. Congratulations on your contribution!
</details>

<details closed>
<summary>Contributor Graph</summary>
<br>
<p align="left">
   <a href="https://github.com/kkingstoun/amu_container_env/graphs/contributors">
      <img src="https://contrib.rocks/image?repo=kkingstoun/amu_container_env">
   </a>
</p>
</details>

---

## License

This project is protected under the [MIT License](https://choosealicense.com/licenses/mit/). For more details, refer to the [LICENSE](https://choosealicense.com/licenses/mit/) file.

---

## Acknowledgments

- Special thanks to the PCSS community for their support.
- Built using [Amumax](https://github.com/MathieuMoalic/amumax), [Pyzfn](https://github.com/MathieuMoalic/pyzfn), and [Code-Server](https://github.com/coder/code-server).

---

## Authors

- [Mateusz Zelent](https://github.com/kkingstoun/) & [Mathieu Moalic](https://github.com/MathieuMoalic)
---
