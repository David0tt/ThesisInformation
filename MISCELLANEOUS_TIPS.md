# Miscellaneous Tips

## Linting / Language Server Support

### Python
For Python linting / language-server features, you may need to configure extra import paths.

Add (or edit) a workspace-local `.vscode/settings.json` and set:

```jsonc
{
    "python.analysis.extraPaths": [
        "./openpi/packages/openpi-client/src",
        "./deoxys_control/deoxys/"
    ]
}
```

### C++
For C++ IntelliSense, you can configure `.vscode/c_cpp_properties.json`.

You can manually set `includePath`, but the most reliable setup is using a `compile_commands.json` so IntelliSense matches the real compiler flags. To generate `compile_commands.json` add the `--cmake-args -DCMAKE_EXPORT_COMPILE_COMMANDS=ON` flag (example with `colcon`):

```bash
colcon build --mixin ninja --cmake-args -DCMAKE_EXPORT_COMPILE_COMMANDS=ON
```

Example `.vscode/c_cpp_properties.json`:

```jsonc
{
    "configurations": [
        {
            "name": "ROS2",
            "compileCommands": "${workspaceFolder}/build/compile_commands.json",
            "includePath": [
                "${workspaceFolder}/**",
                "/opt/ros/${ROS_DISTRO}/include/**"
            ],
            "defines": [],
            "intelliSenseMode": "gcc-x64",
            "cppStandard": "c++17"
        }
    ],
    "version": 4
}
```

## Setting up GitHub/GitLab and SSH Credentials
To connect to GitHub (or GitLab) securely, use SSH keys.

SSH authentication works via a public/private keypair:
- **Public key**: can be shared and added to GitHub/GitLab for authentication.
- **Private key**: must never be shared.

On Linux, SSH keys are typically stored under `~/.ssh/`, e.g.:
- `~/.ssh/id_ed25519` (private key)
- `~/.ssh/id_ed25519.pub` (public key)

Create a keypair (if you do not have one yet):

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```

This should create `id_ed25519` and `id_ed25519.pub` in `~/.ssh`. You can show the public key using

```bash
cat id_ed25519.pub
```


Then add it to your account by pasting the output of the above `cat id_ed25519.pub` command:
- GitHub: https://github.com/settings/keys (click “New SSH Key”)
- GitLab: https://gitlab.cs.uni-tuebingen.de/-/user_settings/ssh_keys

Afterwards you should be able to clone a repository via SSH, e.g.:

```bash
git clone git@github.com:SOMEUSER/SOME_REPOSITORY.git
```

### SSH Login to Servers (Passwordless)
Add your public key to a server:

```bash
ssh-copy-id USER@SERVER
```

Running this command will prompt you for the password. After that, login should work without a password prompt: 

```bash
ssh USER@SERVER
```

Sources:
- https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent
- https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account

## Setting Up LaTeX
I recommend editing LaTeX in VS Code using the “LaTeX Workshop” extension.

Workflow:
1. Install a LaTeX distribution (recommended: TeX Live): https://www.tug.org/texlive/

        sudo apt install texlive-full

2. Install the VS Code “LaTeX Workshop” extension.
3. Open the LaTeX project; on save (or via the extension’s “Run” button) it should build a PDF.


## VS Code Keybindings
For some programs that I find very useful, you can refer to [.linux_autosetup/install_script.sh](https://github.com/David0tt/.linux_autosetup/blob/main/install_script.sh). In particular, I highly recommend using [fzf](https://github.com/junegunn/fzf.git) for efficient search in the terminal.

For keyboard shortcuts I find useful you can refer to [Hotkeys.md](https://github.com/David0tt/.linux_autosetup/blob/main/Hotkeys.md) or [vscode_linux_keybindings.json](https://github.com/David0tt/.linux_autosetup/blob/main/config_files/VSCode/vscode_linux_keybindings.json)


## Compute Clusters
At our chair we have access to multiple compute clusters.


### [TCML](https://uni-tuebingen.de/fakultaeten/mathematisch-naturwissenschaftliche-fakultaet/fachbereiche/informatik/lehrstuehle/kognitive-systeme/projects/tcml-cluster/)
This is a shared compute cluster administrated by our chair. It allows efficient training of medium-sized models (e.g. ResNet-152).

### [ML Cloud](https://portal.mlcloud.uni-tuebingen.de/mlcloud-pages/news)
This is the shared ML Cloud cluster of the University of Tuebingen. Here, large amounts of compute are available, in particular nodes with H100 GPUs with up to 80GB VRAM, allowing training of very large models. However access times might not be ideal due to queueing.


### Avalon (TODO)
These are two PCs with 8xRTX3090 and 8xA5000 respectively for direct compute access. 

General FAQ:
- Environments: `/data/messmer/conda_envs`. In general, `/data/<username>` is where large things should go.
- Repositories can typically live under `/home`. But if you create large checkpoints, you may want them on `/data`.
  - Note: Hugging Face caches often go to `$HOME/.cache` regardless of repo location. A potential solution is to symlink `~/.cache` to `/data/<username>` (just a first idea).
