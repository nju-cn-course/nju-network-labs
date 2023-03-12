# Environment Setup

If you see here then you must be a high-end player, so the instructions here will be sketchy.

{% hint style="warning" %}
Since the Open vSwitch module is currently missing from the kernel in WSL/WSL2, we strongly advise you to use a VM.
{% endhint %}

{% hint style="info" %}
Some essential packages may be absent when you follow these steps, e.g. `wheel`. Please refer to the error message for specific information and install them accordingly.
{% endhint %}

## Install Switchyard

### Source Code

You can find instructions [here](https://pavinberg.gitee.io/switchyard), the repository of switchyard on Gitee. For Ubuntu, you can try

```sh
$ git clone https://gitee.com/pavinberg/switchyard.git
$ sudo apt-get install libffi-dev libpcap-dev python3-dev python3-pip
```

{% hint style="info" %}
We have done modifications to Switchyard over the years, so please use the above repository rather than the original Github source. You are free to look up the original documentation though.
{% endhint %}

### Virtual Environment

You can install Switchyard and related packages in an isolated Python virtual environment ("venv"), which is strongly recommended; or system-wide, which is often less desirable. The venv route is highly suggested since it makes all installation "local" and can easily be destroyed, cleaned up, and recreated.

To create a new virtual environment, you could do something like the following under your workspace folder

```sh
$ python3 -m venv syenv
```

You can change the name `syenv` to whatever you'd like to name your virtual environment. Next, you need to activate the environment. The instructions vary depending on the shell you're using. On `bash`, the command is

```sh
$ source ./syenv/bin/activate
```

You'll need to replace `syenv` with whatever you named the virtual environment. If you're using a different shell than bash, refer to Python documentation on the `venv` module.

Finally, go to the Switchyard directory and perform installation. All the required additional libraries should be automatically installed, too.

```sh
$ python3 -m pip install .
```

If you choose to create `syenv` in your repository, you should exclude the folder out of git tracking. Append this line to `.gitignore`

```
syenv/
```

### Conda

Conda is specialized at managing environments with easy-to-use commands. For those who have issues with Python versions, conda act as an helpful alternative. There are two choices of conda though: Anaconda (the full version) and Miniconda (the minimal version). For our experiments, Miniconda is enough. The installation script can be downloaded [here](https://docs.conda.io/en/latest/miniconda.html#linux-installers).

#### Installation

Since conda environment needs to be activated both under _user privilege_ as well as _superuser privilege_, we need to change the default installation location. To begin with, we create a directory for installation and grant system-wide permission:

```sh
$ sudo mkdir /opt miniconda3 && sudo chmod a+w /opt/miniconda3
```

Then we initiate the script:

```sh
$ bash Miniconda3-latest-Linux-x86_64.sh -u
```

When prompted for installation location, enter

```
/opt/miniconda3
```

Finish installation with default options. One last thing is to modify the shell scripts. For the user, you can run:

```clike
$ eval "$(/opt/miniconda3/bin/conda shell.bash hook)"
$ conda init
$ conda config --set auto_activate_base false
```

The commands are similar for the root:

```clike
$ sudo bash
# eval "$(/opt/miniconda3/bin/conda shell.bash hook)"
# conda init
# conda config --set auto_activate_base false
```

#### Usage

Currently, our experiments require Python version to be lower than 3.8. Such environment can be easily set up with a simple command:

```sh
$ conda create --name syenv python=3.7
```

Now that we have the desired environment ready, we can activate/deactivate it as follows:

```sh
$ conda activate syenv
$ conda deactivate
```

Unlike `venv`, conda manages`syenv` in its own folder, so there is no need to modify `.gitignore`. With activated conda environment, you can now go to the Switchyard directory and perform installation just like using `venv`:

```sh
$ pip install .
```

Besides, conda integrates well with PyCharm as well as VS Code. For VS Code, open _Command Pallette_ and type "Python: Select Interpreter", `syenv` should be listed below. After selection, `syenv` will be automatically activated every time you run the terminal.

## Install Mininet

Depending on your needs, you may want to install Mininet for Python 2 or Python 3. If you want both, execute

```
$ sudo apt install mininet
```

Otherwise, you can install Mininet for Python 3 only, with

```sh
$ git clone https://github.com/mininet/mininet
$ sudo PYTHON=python3 mininet/util/install.sh -n   # install Python 3 Mininet
```

For more details on building Mininet yourself, the official guide is [here](http://mininet.org/download/).

## Install Xterm

Xterm provides CLI to control your network components in Mininet.

```
$ sudo apt install xterm
```

## Install Wireshark

```
$ sudo apt install wireshark
```

You need to configure wireshark during installation. For non-superusers capturing packets, choose _Yes_ here.

![configure-wireshark](../.gitbook/assets/configure-wireshark.png)

Then add your user to `wireshark` user group to allow you to capture packets.

```
$ sudo usermod -a -G wireshark $USER
```

## Other Software

You may need editors like Vim, Emacs, Visual Studio Code, Sublime, etc. for better experience. We do not make specific recommendations here, so just choose your favorite one.
