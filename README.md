# Arch on WSL 2

These are the basic instructions to build Arch on WSL 2.

## Table of Contents

* [Installing Arch Linux for WSL2](#installing-arch-linux-for-wsl-2)
* [Install Yay AUR Helper and Pacman Wrapper](#install-yay-aur-helper-and-pacman-wrapper)
* [Install and Configure Windows Terminal](#install-and-configure-windows-terminal)
* [Powerline Fonts](#powerline-fonts)
* [Launch X11 apps from the shell to Windows display](#launch-x11-apps-from-the-shell-to-windows-display)

***

### Installing Arch Linux for WSL 2

1. Install/upgrade to the `Windows Subsystem for Linux 2`. [Find it on the Microsoft Store](https://apps.microsoft.com/store/detail/windows-subsystem-for-linux/9P9TQF7MRM4R).

2. Download `ArchWSL` from [here](https://github.com/yuk7/ArchWSL/releases/latest). Find the `.appx` and it's associated `.cer`.

3. Open an elevated Powershell prompt, then import the certificate.

    ```pwsh
    Import-Certificate -FilePath ~\Downloads\ArchWSL-AppX_*.cer -CertStoreLocation Cert:\LocalMachine\TrustedPeople\
    ```

4. Add the package.

    ```pwsh
    Add-AppxPackage ~\Downloads\ArchWSL-AppX_*
    ```

5. Then run `arch.exe`
<br><sub><sup>PS. You don't need Powershell anymore. Just invoke this from Windows search.</sup></sub>

6. Uncomment some servers in the pacman mirrorlist.

    `vim /etc/pacman.d/mirrorlist`

7. Become root and refresh Arch keyring.

    ```sh
    rm -R /etc/pacman.d/gnupg/
    rm -R /root/.gnupg/ 
    gpg --refresh-keys
    pacman-key --init && pacman-key --populate archlinux
    pacman -S archlinux-keyring
    pacman -Syu
    ```

8. Install base.

    ```sh
    pacman -Syyu base base-devel git neovim aria2 fish
    ```

9. Set `root` user password.

    `passwd`

10. Create new user.

    ```sh
    export USER=<username> && useradd -m -G wheel -s /bin/fish -d /home/$USER $USER
    ```

11. Set password on user.

    `passwd $USER`

12. Enable `wheel` group.

    ```sh
    sed -i 's/# %wheel ALL=(ALL:ALL) ALL/%wheel ALL=(ALL:ALL) ALL/' /etc/sudoers
    ```

13. Edit Arch locale and regenerate.

    ```sh
    sed -i 's:#en_US.UTF-8 UTF-8:en_US.UTF-8 UTF-8:g' /etc/locale.gen
    locale-gen
    echo LANG=en_US.UTF-8 >> /etc/locale.conf
    echo LANGUAGE=en_US.UTF-8 >> /etc/locale.conf
    echo LC_ALL=en_US.UTF-8 >> /etc/locale.confS
    ```

***

### Install Yay AUR Helper and Pacman Wrapper

1. Create a directory for the yay PKGBUILD files and enter it.

   ```mkdir ~/yay && cd ~/yay```

2. Download yay PKGBUILD from AUR.

   `aria2c "https://aur.archlinux.org/cgit/aur.git/plain/PKGBUILD?h=yay"`

3. Run `makepkg` to build and install yay.

   `makepkg -si`

<sub><sup>Alternatively, you could use the instructions from the official [Yay Github repo](https://github.com/Jguer/yay).</sup></sub>

***

### Install and Configure Windows Terminal

1. From the Windows Store, install [Windows Terminal](https://apps.microsoft.com/store/detail/windows-subsystem-for-linux/9P9TQF7MRM4R).

2. Edit the `settings.json` file for `Windows Terminal` and add the following line to the list item:

   `"icon" : "C:\Program Files\WindowsApps\yuk7.archwsl_22.10.16.0_x64__35zwpb4sx6e50\images\StoreLogo.png",`

***

### Powerline Fonts

Download and install fonts for Powerline. [Download here.](https://github.com/powerline/fonts/)

***

#### I don't use VcXsrv so I can NOT guarantee the accuracy of the info below

<sub><sup>Though you can comment on [this issue](https://github.com/scyilk/arch-wsl/issues/1) if you would like a section for another X server.</sup></sub>

### Launch X11 apps from the shell to Windows display

1. Download, install and then launch [VcXsrv](https://sourceforge.net/projects/vcxsrv/).

   * `Select display settings` - Take default
   * `Select how to start clients` - Take default
   * `Extra settings` - Be sure `Disable access control` is checked.
   * `Configuration complete` - Click `Finish`

2. Create a firewall rule in Windows to allow communication from WSL 2 to host OS.

   `New-NetFirewallRule -DisplayName "X Server - WSL 2" -Direction Inbound -Program "C:\Program Files\VcXsrv\vcxsrv.exe" -Action Allow`

3. Get the IP of your local computer of the `vEthernet (WSL)` interface from CMD or PowerShell:

   `ipconfig`

4. Export output to display using IP address collected in step 3.

   For `bash` and `zsh`:
   `export DISPLAY=192.168.1.100:0`

   For `fish`:
   `set -x DISPLAY 192.168.1.100:0`

   Note: Add to `~/.bashrc`, `~/.zshrc`, or `~/.config/fish/fish.config` and you won't need to type it again on the next WSL launch.

5. Install an xorg app for testing (We will use the Xorg Calculator).

   `sudo pacman -S xorg-xcalc`

6. Launch `xcalc` to test.

   `xcalc`

7. If `VcXsrv` is working properly, Xorg Calculator should popup as a new window.
<br><br><sub><sup>8. Uninstall if you want with `sudo pacman -Rns xorg-xcalc`</sup><sub>

# I use archwsl btw!

Share this with your Arch buddies that want to use Arch on their PCs as well. [Link to this repo](https://github.com/scyilk/arch-wsl)

* [Share on 𝕏](https://twitter.com/intent/tweet?url=https%3A%2F%2Fgithub.com%2Fscyilk%2Farch-wsl&text=Arch%20Linux%20on%20your%20PC%2C%20amazing!&hashtags=archlinux,archbtw,archwslbtw)
* [Share on Facebook](https://www.facebook.com/sharer.php?u=https%3A%2F%2Fgithub.com%2Fscyilk%2Farch-wsl)
* [Share on LinkedIn](https://www.linkedin.com/sharing/share-offsite/&url=https%3A%2F%2Fgithub.com%2Fscyilk%2Farch-wsl)
