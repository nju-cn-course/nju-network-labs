# VS Code

Visual Studio Code (VSC) is a convenient tool to develop your projects. As one of the most popular editor, it is open source with a large number of extensions. We will briefly introduce it as well as some helpful plugins. This tutorial is prepared for those who want to take full control of Switchyard in one editor. We assume Ubuntu 18.04 here.

We expect that you will spend several hours on this.

## Install VSC

The VM we provided comes with VS Code. In case you have a custom environment, you can either try the graphical software center if available, or download the installation package. Detailed instructions can be found [here](https://code.visualstudio.com/docs/setup/linux).

## Develop with VSC

Open the folder of Switchyard in VSC. You can open files in the explorer column and edit them.

![VSC](../../.gitbook/assets/vscode.png)

There are some plugins you may want to install. For Python, search for this extension and install it.

![VSC-python](<../../.gitbook/assets/vscode-python (1).png>)

Next, open any Python files and you will see a pop-up message asking you whether you want to install a linter. Pylint is enough so install it.

![VSC-pylint](../../.gitbook/assets/vscode-pylint.png)

You have got almost everything you need here. But you may want to format your document by right click on your editor and choose "Format Document". VSC will tell you that you need to install a formatter. Yapf is one option but you are free to use another one.

![VSC-format](<../../.gitbook/assets/vscode-format (1).gif>)

## Switch Python Environment

We recommand using a isolated environment of Python for Switchyard module. We introduce this in Switchyard section that you need to activate the environment for Switchyard to be used. However, this activation **ONLY** takes effect in that exactly one terminal session, which means that the VS Code has no idea where is your Switchyard so that it cannot support features like code completion.

To solve this, tell VS Code to switch to the virtual environment you created.

Press `Shift+Ctrl+P` on Windows/Linux or `Shift+Cmd+P` on macOS to open _Command Pallette_ and type "Python: Select interpreter". Then choose the Python with the path corresponding to the `syenv` you created.

![VS Code Python: Select Interpreter](../../.gitbook/assets/vsc-python-select-interpreter.png)

![Choose virtual environment](../../.gitbook/assets/vsc-python-venv.png)

{% hint style="info" %}
For `venv`, VS Code can only detect interpreter in current path. If the environment is not listed, try:

1. Open the parent folder,
2. Set the path to the interpreter manually in the dialog box,
3. Use the method below, or
4. Use other IDE/editor instead, like [PyCharm](https://www.jetbrains.com/pycharm/).
{% endhint %}

You can also set this in your workspace settings for running tasks below.

Press `Shift+Ctrl+P` on Windows/Linux or `Shift+Cmd+P` on macOS to open _Command Pallette_ and type "Preferences: Open Workspace Settings". This will create a `.vscode/settings.json` file in your workspace.

Edit `.vscode/settings.json` like this:

```bash
{
    "python.pythonPath": "/home/njucs/workspace/syenv/bin/python3"
}
```

Notice that you should change the path to the one on your machine.

## Run Switchyard

You can simply open the terminal either in VS Code or in Linux to run switchyard. But VS Code provides an easy way to run a command with shortcuts. You can use this feature to run Switchyard test. However, as the configurations are too complicated, we omit the details here. For those who want to fine-tune their VS Code, you can find instructions in the [appendix](../../appendix/vs-code-configuration.md#configure-test-procedure).

## Debug with VSC

[Switchyard document about debug](https://jsommers.github.io/switchyard/test\_execution.html#if-you-don-t-like-pdb) shows that you are free to choose other debuggers, which means you can use VS Code debugger to test your code. This step is optional, but if you are really curious, you can try the configurations [here](../../appendix/vs-code-configuration.md#configure-debug-procedure).

## Other editor/IDE

You are free to choose other editors or IDEs you prefer. Some powerful tools are:

* IDE: PyCharm, Visual Studio
* Editor: Vim, Emacs

However, if you have always been using an IDE, we recommend you to try out an editor.
