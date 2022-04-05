# SingleStore Cluster-in-a-box installation in Ubuntu WSL on Windows 10/11

The purpose of this documentation is to simplify local SingleStore Cluster in a box setup in Windows using WSL2, Ubuntu for WSL2 and Docker Desktop for WSL backend and KinD (Kubernetes in Docker). Read on to know more.

## What is the Windows Subsystem for Linux?

The Windows Subsystem for Linux lets developers run a GNU/Linux environment -- including most command-line tools, utilities, and applications -- directly on Windows, unmodified, without the overhead of a traditional virtual machine or dualboot setup.

You can:

- Choose your favorite GNU/Linux distributions [from the Microsoft Store](https://aka.ms/wslstore).
- Run common command-line tools such as `grep`, `sed`, `awk`, or other ELF-64 binaries.
- Run Bash shell scripts and GNU/Linux command-line applications including:  
  - Tools: vim, emacs, tmux
  - Languages: [NodeJS](/windows/nodejs/setup-on-wsl2), Javascript, [Python](/windows/python/web-frameworks), Ruby, C/C++, C# & F#, Rust, Go, etc.
  - Services: SSHD, [MySQL](./tutorials/wsl-database.md), Apache, lighttpd, [MongoDB](./tutorials/wsl-database.md), [PostgreSQL](./tutorials/wsl-database.md).
- Install additional software using your own GNU/Linux distribution package manager.
- Invoke Windows applications using a Unix-like command-line shell.
- Invoke GNU/Linux applications on Windows.

> [!div class="nextstepaction"]
> [Install WSL](install.md)

<br>

> [!VIDEO https://www.youtube.com/embed/48k317kOxqg]

## What is WSL 2?

WSL 2 is a new version of the Windows Subsystem for Linux architecture that powers the Windows Subsystem for Linux to run ELF64 Linux binaries on Windows. Its primary goals are to **increase file system performance**, as well as adding **full system call compatibility**.

This new architecture changes how these Linux binaries interact with Windows and your computer's hardware, but still provides the same user experience as in WSL 1 (the current widely available version).

Individual Linux distributions can be run with either the WSL 1 or WSL 2 architecture. Each distribution can be upgraded or downgraded at any time and you can run WSL 1 and WSL 2 distributions side by side. WSL 2 uses an entirely new architecture that benefits from running a real Linux kernel.

<br>

> [!VIDEO https://www.youtube.com/embed/MrZolfGm8Zk]

# Manual installation steps for older versions of WSL

For simplicity, we generally recommend using the [`wsl --install`](./install.md) to install Windows Subsystem for Linux, but if you're running an older build of Windows, that may not be supported. We have included the manual installation steps below. If you run into an issue during the install process, check the [installation section of the troubleshooting guide](./troubleshooting.md#installation-issues).

## Step 1 - Enable the Windows Subsystem for Linux

You must first enable the "Windows Subsystem for Linux" optional feature before installing any Linux distributions on Windows.

Open PowerShell **as Administrator (Start menu > PowerShell > right-click > Run as Administrator)** and enter this command:

```powershell
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
```

We recommend now moving on to step #2, updating to WSL 2, but if you wish to only install WSL 1, you can now **restart** your machine and move on to [Step 6 - Install your Linux distribution of choice](#step-6---install-your-linux-distribution-of-choice). To update to WSL 2, **wait to restart** your machine and move on to the next step.

## Step 2 - Check requirements for running WSL 2

To update to WSL 2, you must be running Windows 10.

- For x64 systems: **Version 1903** or higher, with **Build 18362** or higher.
- For ARM64 systems: **Version 2004** or higher, with **Build 19041** or higher.
- Builds lower than 18362 do not support WSL 2. Use the [Windows Update Assistant](https://www.microsoft.com/software-download/windows10) to update your version of Windows.

To check your version and build number, select **Windows logo key + R**, type **winver**, select **OK**. [Update to the latest Windows version](ms-settings:windowsupdate) in the Settings menu.

> [!NOTE]
> If you are running Windows 10 version 1903 or 1909, open "Settings" from your Windows menu, navigate to "Update & Security" and select "Check for Updates". Your Build number must be 18362.1049+ or 18363.1049+, with the minor build # over .1049. Read more: [WSL 2 Support is coming to Windows 10 Versions 1903 and 1909](https://devblogs.microsoft.com/commandline/wsl-2-support-is-coming-to-windows-10-versions-1903-and-1909/).

## Step 3 - Enable Virtual Machine feature

Before installing WSL 2, you must enable the **Virtual Machine Platform** optional feature. Your machine will require [virtualization capabilities](./troubleshooting.md#error-0x80370102-the-virtual-machine-could-not-be-started-because-a-required-feature-is-not-installed) to use this feature.

Open PowerShell as Administrator and run:

```powershell
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```

**Restart** your machine to complete the WSL install and update to WSL 2.

## Step 4 - Download the Linux kernel update package

1. Download the latest package:
    - [WSL2 Linux kernel update package for x64 machines](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi)

    > [!NOTE]
    > If you're using an ARM64 machine, please download the [ARM64 package](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_arm64.msi) instead. If you're not sure what kind of machine you have, open Command Prompt or PowerShell and enter: `systeminfo | find "System Type"`. **Caveat:** On non-English Windows versions, you might have to modify the search text, translating the "System Type" string. You may also need to escape the quotations for the find command. For example, in German `systeminfo | find '"Systemtyp"'`.

2. Run the update package downloaded in the previous step. (Double-click to run - you will be prompted for elevated permissions, select ‘yes’ to approve this installation.)

Once the installation is complete, move on to the next step - setting WSL 2 as your default version when installing new Linux distributions. (Skip this step if you want your new Linux installs to be set to WSL 1).

> [!NOTE]
> For more information, read the article [changes to updating the WSL2 Linux kernel](https://devblogs.microsoft.com/commandline/wsl2-will-be-generally-available-in-windows-10-version-2004), available on the [Windows Command Line Blog](https://aka.ms/cliblog).

## Step 5 - Set WSL 2 as your default version

Open PowerShell and run this command to set WSL 2 as the default version when installing a new Linux distribution:

```powershell
wsl --set-default-version 2
```

## Step 6 - Install your Linux distribution of choice

1. Open the [Microsoft Store](https://aka.ms/wslstore) and select your favorite Linux distribution.

    ![View of Linux distributions in the Microsoft Store](https://github.com/MicrosoftDocs/WSL/blob/main/WSL/media/store.png)

    The following links will open the Microsoft store page for each distribution:

    - [Ubuntu 18.04 LTS](https://www.microsoft.com/store/apps/9N9TNGVNDL3Q)
    - [Ubuntu 20.04 LTS](https://www.microsoft.com/store/apps/9n6svws3rx71)
    - [openSUSE Leap 15.1](https://www.microsoft.com/store/apps/9NJFZK00FGKV)
    - [SUSE Linux Enterprise Server 12 SP5](https://www.microsoft.com/store/apps/9MZ3D1TRP8T1)
    - [SUSE Linux Enterprise Server 15 SP1](https://www.microsoft.com/store/apps/9PN498VPMF3Z)
    - [Kali Linux](https://www.microsoft.com/store/apps/9PKR34TNCV07)
    - [Debian GNU/Linux](https://www.microsoft.com/store/apps/9MSVKQC78PK6)
    - [Fedora Remix for WSL](https://www.microsoft.com/store/apps/9n6gdm4k2hnc)
    - [Pengwin](https://www.microsoft.com/store/apps/9NV1GV1PXZ6P)
    - [Pengwin Enterprise](https://www.microsoft.com/store/apps/9N8LP0X93VCP)
    - [Alpine WSL](https://www.microsoft.com/store/apps/9p804crf0395)
    - [Raft(Free Trial)](https://www.microsoft.com/store/apps/9msmjqd017x7)

2. From the distribution's page, select "Get".

    ![Linux distributions in the Microsoft store](https://github.com/MicrosoftDocs/WSL/blob/main/WSL/media/UbuntuStore.png)

The first time you launch a newly installed Linux distribution, a console window will open and you'll be asked to wait for a minute or two for files to de-compress and be stored on your PC. All future launches should take less than a second.

You will then need to [create a user account and password for your new Linux distribution](./setup/environment.md#set-up-your-linux-username-and-password).

![Ubuntu unpacking in the Windows console](https://github.com/MicrosoftDocs/WSL/blob/main/WSL/media/UbuntuInstall.png)

**CONGRATULATIONS! You've successfully installed and set up a Linux distribution that is completely integrated with your Windows operating system!**

## Troubleshooting installation

If you run into an issue during the install process, check the [installation section of the troubleshooting guide](./troubleshooting.md#installation-issues).

## Downloading distributions

There are some scenarios in which you may not be able (or want) to, install WSL Linux distributions using the Microsoft Store. You may be running a Windows Server or Long-Term Servicing (LTSC) desktop OS SKU that doesn't support Microsoft Store, or your corporate network policies and/or admins do not permit Microsoft Store usage in your environment. In these cases, while WSL itself is available, you may need to download Linux distributions directly.

If the Microsoft Store app is not available, you can download and manually install Linux distributions using these links:

- [Ubuntu](https://aka.ms/wslubuntu)
- [Ubuntu 20.04](https://aka.ms/wslubuntu2004)
- [Ubuntu 20.04 ARM](https://aka.ms/wslubuntu2004arm)
- [Ubuntu 18.04](https://aka.ms/wsl-ubuntu-1804)
- [Ubuntu 18.04 ARM](https://aka.ms/wsl-ubuntu-1804-arm)
- [Ubuntu 16.04](https://aka.ms/wsl-ubuntu-1604)
- [Debian GNU/Linux](https://aka.ms/wsl-debian-gnulinux)
- [Kali Linux](https://aka.ms/wsl-kali-linux-new)
- [SUSE Linux Enterprise Server 12](https://aka.ms/wsl-sles-12)
- [SUSE Linux Enterprise Server 15 SP2](https://aka.ms/wsl-SUSELinuxEnterpriseServer15SP2)
- [SUSE Linux Enterprise Server 15 SP3](https://aka.ms/wsl-SUSELinuxEnterpriseServer15SP3)
- [openSUSE Tumbleweed](https://aka.ms/wsl-opensuse-tumbleweed)
- [openSUSE Leap 15.3](https://aka.ms/wsl-opensuseleap15-3)
- [openSUSE Leap 15.2](https://aka.ms/wsl-opensuseleap15-2)
- [Oracle Linux 8.5](https://aka.ms/wsl-oraclelinux-8-5)
- [Oracle Linux 7.9](https://aka.ms/wsl-oraclelinux-7-9)
- [Fedora Remix for WSL](https://github.com/WhitewaterFoundry/WSLFedoraRemix/releases/)

This will cause the `<distro>.appx` packages to download to a folder of your choosing.

If you prefer, you can also download your preferred distribution(s) via the command line, you can use PowerShell with the [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) cmdlet. For example, to download Ubuntu 20.04:

```powershell
Invoke-WebRequest -Uri https://aka.ms/wslubuntu2004 -OutFile Ubuntu.appx -UseBasicParsing
```

> [!TIP]
> If the download is taking a long time, turn off the progress bar by setting `$ProgressPreference = 'SilentlyContinue'`

You also have the option to use the [curl command-line utility](https://curl.se/) for downloading. To download Ubuntu 20.04 with curl:

```console
curl.exe -L -o ubuntu-2004.appx https://aka.ms/wslubuntu2004
```

In this example, `curl.exe` is executed (not just `curl`) to ensure that, in PowerShell, the real curl executable is invoked, not the PowerShell curl alias for [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest).

Once the distribution has been downloaded, navigate to the folder containing the download and run the following command in that directory, where `app-name` is the name of the Linux distribution .appx file.

```Powershell
Add-AppxPackage .\app_name.appx
```

Once the Appx package has finished downloading, you can start running the new distribution by double-clicking the appx file. (The command `wsl -l` will not show that the distribution is installed until this step is complete). 

If you are using Windows server, or run into problems running the command above you can find the alternate install instructions on the [Windows Server](install-on-server.md) documentation page to install the `.appx` file by changing it to a zip file.

Once your distribution is installed, follow the instructions to [create a user account and password for your new Linux distribution](./setup/environment.md#set-up-your-linux-username-and-password).

## Install Windows Terminal (optional)

Using Windows Terminal enables you to open multiple tabs or window panes to display and quickly switch between multiple Linux distributions or other command lines (PowerShell, Command Prompt, Azure CLI, etc). You can fully customize your terminal with unique color schemes, font styles, sizes, background images, and custom keyboard shortcuts. [Learn more.](/windows/terminal)

[Install Windows Terminal](/windows/terminal/get-started).

  ![Windows Terminal](https://github.com/MicrosoftDocs/WSL/blob/main/WSL/media/terminal.png)
  
---
## Once WSL 2 installed, we could proceed to install Docker Desktop for Windows and enable WSL2 backend.
---

> **Update to the Docker Desktop terms**
>
> Commercial use of Docker Desktop in larger enterprises (more than 250
> employees OR more than $10 million USD in annual revenue) now requires a paid
> subscription. The grace period for those that will require a paid subscription
> ends on January 31, 2022. [Learn more](https://www.docker.com/blog/the-grace-period-for-the-docker-subscription-service-agreement-ends-soon-heres-what-you-need-to-know/){:
 target="_blank" rel="noopener" class="_" id="dkr_docs_cta"}.
{: .important}

Windows Subsystem for Linux (WSL) 2 introduces a significant architectural change as it is a full Linux kernel built by Microsoft, allowing Linux containers to run natively without emulation. With Docker Desktop running on WSL 2, users can leverage Linux workspaces and avoid having to maintain both Linux and Windows build scripts. In addition, WSL 2 provides improvements to file system sharing, boot time, and allows access to some cool new features for Docker Desktop users.

Docker Desktop uses the dynamic memory allocation feature in WSL 2 to greatly improve the resource consumption. This means, Docker Desktop only uses the required amount of CPU and memory resources it needs, while enabling CPU and memory-intensive tasks such as building a container to run much faster.

Additionally, with WSL 2, the time required to start a Docker daemon after a cold start is significantly faster. It takes less than 10 seconds to start the Docker daemon when compared to almost a minute in the previous version of Docker Desktop.

## Prerequisites

Before you install the Docker Desktop WSL 2 backend, you must complete the following steps:

1. Install Windows 10, version 1903 or higher or Windows 11.
2. Enable WSL 2 feature on Windows. For detailed instructions, refer to the [Microsoft documentation](https://docs.microsoft.com/en-us/windows/wsl/install-win10){:target="_blank" rel="noopener" class="_"}.
3. Download and install the [Linux kernel update package](https://docs.microsoft.com/windows/wsl/wsl2-kernel){:target="_blank" rel="noopener" class="_"}.

## Best practices

- To get the best out of the file system performance when bind-mounting files, we recommend storing source code and other data that is bind-mounted into Linux containers (i.e., with `docker run -v <host-path>:<container-path>`) in the Linux file system, rather than the Windows file system. You can also refer to the [recommendation](https://docs.microsoft.com/en-us/windows/wsl/compare-versions){:target="_blank" rel="noopener" class="_"} from Microsoft.

  - Linux containers only receive file change events ("inotify events") if the
      original files are stored in the Linux filesystem. For example, some web development workflows rely on inotify events for automatic reloading when files have changed.
  - Performance is much higher when files are bind-mounted from the Linux
      filesystem, rather than remoted from the Windows host. Therefore avoid
      `docker run -v /mnt/c/users:/users` (where `/mnt/c` is mounted from Windows).
  - Instead, from a Linux shell use a command like `docker run -v ~/my-project:/sources <my-image>`
      where `~` is expanded by the Linux shell to `$HOME`.
- If you have concerns about the size of the docker-desktop-data VHDX, or need to change it, take a look at the [WSL tooling built into Windows](https://docs.microsoft.com/en-us/windows/wsl/vhd-size){:target="_blank" rel="noopener" class="_"}.
- If you have concerns about CPU or memory usage, you can configure limits on the memory, CPU, Swap size allocated to the [WSL 2 utility VM](https://docs.microsoft.com/en-us/windows/wsl/wsl-config#global-configuration-options-with-wslconfig){:target="_blank" rel="noopener" class="_"}.
- To avoid any potential conflicts with using WSL 2 on Docker Desktop, you must [uninstall any previous versions of Docker Engine](../../engine/install/ubuntu.md#uninstall-docker-engine) and CLI installed directly through Linux distributions before installing Docker Desktop.

## Download

Download [Docker Desktop 2.3.0.2](https://desktop.docker.com/win/main/amd64/Docker%20Desktop%20Installer.exe) or a later release.

## Install

Ensure you have completed the steps described in the Prerequisites section **before** installing the Docker Desktop Stable 2.3.0.2 release.

1. Follow the usual installation instructions to install Docker Desktop. If you are running a supported system, Docker Desktop prompts you to enable WSL 2 during installation. Read the information displayed on the screen and enable WSL 2 to continue.
2. Start Docker Desktop from the Windows Start menu.
3. From the Docker menu, select **Settings** > **General**.

    ![Enable WSL 2](images/https://github.com/docker/docker.github.io/blob/master/desktop/windows/images/wsl2-enable.png)

4. Select the **Use WSL 2 based engine** check box.

    If you have installed Docker Desktop on a system that supports WSL 2, this option will be enabled by default.
5. Click **Apply & Restart**.
6. Ensure the distribution runs in WSL 2 mode. WSL can run distributions in both v1 or v2 mode.

    To check the WSL mode, run:

     `wsl.exe -l -v`

    To upgrade your existing Linux distro to v2, run:

    `wsl.exe --set-version (distro name) 2`

    To set v2 as the default version for future installations, run:

    `wsl.exe --set-default-version 2`

7. When Docker Desktop restarts, go to **Settings** > **Resources** > **WSL Integration**.

    The Docker-WSL integration will be enabled on your default WSL distribution. To change your default WSL distro, run `wsl --set-default <distro name>`.

    For example, to set Ubuntu as your default WSL distro, run `wsl --set-default ubuntu`.

    Optionally, select any additional distributions you would like to enable the Docker-WSL integration on.

    > **Note**
    >
    > The Docker-WSL integration components running in your distro depend on glibc. This can cause issues when running musl-based distros such as Alpine Linux. Alpine users can use the [alpine-pkg-glibc](https://github.com/sgerrand/alpine-pkg-glibc){:target="_blank" rel="noopener" class="_"} package to deploy glibc alongside musl to run the integration.

    ![WSL 2 Choose Linux distro](https://github.com/docker/docker.github.io/blob/master/desktop/windows/images/wsl2-choose-distro.png)

8. Click **Apply & Restart**.

## Develop with Docker and WSL 2

The following section describes how to start developing your applications using Docker and WSL 2. We recommend that you have your code in your default Linux distribution for the best development experience using Docker and WSL 2. After you have enabled WSL 2 on Docker Desktop, you can start working with your code inside the Linux distro and ideally with your IDE still in Windows. This workflow can be pretty straightforward if you are using [VSCode](https://code.visualstudio.com/download).

1. Open VSCode and install the [Remote - WSL](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-wsl) extension. This extension allows you to work with a remote server in the Linux distro and your IDE client still on Windows.
2. Now, you can start working in VSCode remotely. To do this, open your terminal and type:

    `wsl`

    `code .`

    This opens a new VSCode connected remotely to your default Linux distro which you can check in the bottom corner of the screen.

    Alternatively, you can type the name of your default Linux distro in your Start menu, open it, and then run `code` .
3. When you are in VSCode, you can use the terminal in VSCode to pull your code and start working natively from your Windows machine.

## GPU support

Starting with Docker Desktop 3.1.0, Docker Desktop supports WSL 2 GPU Paravirtualization (GPU-PV) on NVIDIA GPUs. To enable WSL 2 GPU Paravirtualization, you need:

- A machine with an NVIDIA GPU
- The latest Windows Insider version from the Dev Preview ring
- [Beta drivers](https://developer.nvidia.com/cuda/wsl){:target="_blank" rel="noopener" class="_"} from NVIDIA supporting WSL 2 GPU Paravirtualization
- Update WSL 2 Linux kernel to the latest version using `wsl --update` from an elevated command prompt
- Make sure the WSL 2 backend is enabled in Docker Desktop

To validate that everything works as expected, run the following command to run a short benchmark on your GPU:

```console
$ docker run --rm -it --gpus=all nvcr.io/nvidia/k8s/cuda-sample:nbody nbody -gpu -benchmark
Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
        -fullscreen       (run n-body simulation in fullscreen mode)
        -fp64             (use double precision floating point values for simulation)
        -hostmem          (stores simulation data in host memory)
        -benchmark        (run benchmark to measure performance)
        -numbodies=<N>    (number of bodies (>= 1) to run in simulation)
        -device=<d>       (where d=0,1,2.... for the CUDA device to use)
        -numdevices=<i>   (where i=(number of CUDA devices > 0) to use for simulation)
        -compare          (compares simulation results running once on the default GPU and once on the CPU)
        -cpu              (run n-body simulation on the CPU)
        -tipsy=<file.bin> (load a tipsy model file for simulation)
        
> NOTE: The CUDA Samples are not meant for performance measurements. Results may vary when GPU Boost is enabled.

> Windowed mode
> Simulation data stored in video memory
> Single precision floating point simulation
> 1 Devices used for simulation
MapSMtoCores for SM 7.5 is undefined.  Default to use 64 Cores/SM
GPU Device 0: "GeForce RTX 2060 with Max-Q Design" with compute capability 7.5

> Compute 7.5 CUDA device: [GeForce RTX 2060 with Max-Q Design]
30720 bodies, total time for 10 iterations: 69.280 ms
= 136.219 billion interactions per second
= 2724.379 single-precision GFLOP/s at 20 flops per interaction
```


Now that we have WSL2 and Docker Destop installed, you could enable KinD (Kubernetes in Docker)

Docker Desktop includes a standalone Kubernetes server and client,
as well as Docker CLI integration that runs on your machine. The Kubernetes server runs locally within your Docker instance, is not configurable, and is a single-node cluster.

The Kubernetes server runs within a Docker container on your local system, and
is only for local testing. Enabling Kubernetes allows you to deploy
your workloads in parallel, on Kubernetes, Swarm, and as standalone containers. Enabling or disabling the Kubernetes server does not affect your other
workloads.

## Prerequisites

The Kubernetes client command `kubectl` is included and configured to connect
to the local Kubernetes server. If you have already installed `kubectl` and
pointing to some other environment, such as `minikube` or a GKE cluster, ensure you change the context so that `kubectl` is pointing to `docker-desktop`:

```console
$ kubectl config get-contexts
$ kubectl config use-context docker-desktop
```

If you installed `kubectl` using Homebrew, or by some other method, and
experience conflicts, remove `/usr/local/bin/kubectl`.

## Enable Kubernetes

To enable Kubernetes support and install a standalone instance of Kubernetes
running as a Docker container, go to **Preferences** > **Kubernetes** and then click **Enable Kubernetes**.

By default, Kubernetes containers are hidden from commands like `docker
service ls`, because managing them manually is not supported. To see these internal containers, select **Show system containers (advanced)**. Most users do not need this option.

Click **Apply & Restart** to save the settings and then click **Install** to confirm. This instantiates images required to run the Kubernetes server as containers, and installs the `/usr/local/bin/kubectl` command on your machine.

![Enable Kubernetes](https://docs.docker.com/desktop/images/kube-enable.png){:width="750px"}

When Kubernetes is enabled and running, an additional status bar item displays
  at the bottom right of the Docker Desktop Settings dialog.

The status of Kubernetes shows in the Docker menu and the context points to
  `docker-desktop`.

![Docker Menu with Kubernetes](https://docs.docker.com/desktop/images/kube-context.png){: width="400px"}

> Upgrade Kubernetes
>
> Docker Desktop does not upgrade your Kubernetes cluster automatically after a new update. To upgrade your Kubernetes cluster to the latest version, select **Reset Kubernetes Cluster**.

## Use the kubectl command

Kubernetes integration provides the Kubernetes CLI command
at `/usr/local/bin/kubectl` on Mac and at `C:\>Program Files\Docker\Docker\Resources\bin\kubectl.exe` on Windows. This location may not be in your shell's `PATH`
variable, so you may need to type the full path of the command or add it to
the `PATH`.

You can test the command by listing the available nodes:

```console
$ kubectl get nodes

NAME                 STATUS    ROLES     AGE       VERSION
docker-desktop       Ready     master    3h        v1.19.7
```

For more information about `kubectl`, see the
[`kubectl` documentation](https://kubernetes.io/docs/reference/kubectl/overview/){:target="_blank" rel="noopener" class="_"}.

## Disable Kubernetes

To disable Kubernetes support at any time, clear the **Enable Kubernetes** check box. This stops and removes Kubernetes containers, and also removes the `/usr/local/bin/kubectl` command.

## Once we have Kubernetes up and running with KinD, we can now deploy our Single Store Cluster-In-A-Box

Even though SingleStore is a distributed system, we could run a minimal version of SingleStore on your laptop in Kubernetes. The combination of free access, and being able to run SingleStore on your laptop, can be extremely convenient for demos, software testing, developer productivity. Read on to find out how.

A machine with at least 8GB RAM and four CPUs is recommended for this setup. This is ideal for quickly provisioning a system to understand the capabilities of the SQL engine. Kubernetes deployments are self sustained. Once deployed, except for customization, there would no further overhead of managing or configuring SingleStore to get it running.

![SingleStore Cluster-In-A-Box Architecture](![Docker Menu with Kubernetes](https://docs.docker.com/desktop/images/kube-context.png){: width="400px"}){: width="400px"}

## Sign Up For SingleStore

To get a free license for SingleStore, register at singlestore.com/software-standard/ and click the link in the confirmation email. Then go to the SingleStore customer portal and login. Click “Licenses” and you’ll see your license for running SingleStore for free. This license never expires, and is good for clusters up to four machines and up to 128GB of combined RAM. This is not the license you’ll want for a production cluster, but it’s great for these “kick the tires” scenarios. Note this license key. We’ll need to copy/paste it into place next.

## Kubernetes Configuration Files
Kubernetes stores configuration details in yaml files. (A yaml file is a text file that’s great for capturing our architecture setup.) Typically each yaml file contains a single resource. For simplicity, we’ll create one yaml file that includes both a deployment and a service. We’ll connect to the service, the service will proxy to the pod, and the pod will route the request into the container. We’ll use the memsql/cluster-in-a-box image built by SingleStore and available on Docker Hub. This image comes with the SingleStore database engine and SingleStore Studio preinstalled. The minimum system requirements are disabled in this “cluster-in-a-box” configuration. Create an empty directory, and create a file named kubernetes-memsql.yaml inside. Open this file in your favorite code editor and paste in this content. As with Python source code, white space is significant. Yaml uses two spaces, not tabs. Double-check the yaml file to ensure each section is indented with exactly two spaces. If you have more or fewer spaces, or if you’re using tabs, you’ll get an error on startup.









## What a successful deployment of Single Store Cluster-In-A-Box looks like in KinD

![image](https://user-images.githubusercontent.com/62608538/161215838-20e6a472-f03a-4d62-9e16-757d9585b502.png)

![image](https://user-images.githubusercontent.com/62608538/161216559-aa47a0f5-7efd-45b4-b7ff-1a42d8b3d0a8.png)

![image](https://user-images.githubusercontent.com/62608538/161216733-6678ec10-807b-4c61-b7a2-23e7dd68a8ee.png)
