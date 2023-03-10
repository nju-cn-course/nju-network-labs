# VS Code Configuration

This page is dedicated to those who want the most out of VS Code. Since the editor is constantly updating, some configurations here may not apply to a newer version. As a result, you may need to adjust your settings accordingly.

## Configure Test Procedure

*   First you need to create a `tasks.json` in your workspace:

    Press `Shift+Ctrl+P` on Windows/Linux or `Shift+Cmd+P` on macOS to open _Command Pallette_ and type "Tasks: Configure Task". Choose "Create tasks.json from template", choose "Others". Now you have a `.vscode/tasks.json` file for tasks running configuration.
*   Edit `.vscode/tasks.json` as follows:

    ```javascript
    {
        // See https://go.microsoft.com/fwlink/?LinkId=733558
        // for the documentation about the tasks.json format
        "version": "2.0.0",
        "tasks": [
            {
                "label": "run switchyard test",
                "type": "shell",
                "command": "${config:python.pythonPath}",
                "args": [
                    "-m",
                    "switchyard.swyard",
                    "-t",
                    "${fileDirname}/${fileBasenameNoExtension}_testscenario.py",
                    "${file}"
                ],
                "group": {
                    "kind": "build",
                    "isDefault": true
                }
            }
        ]
    }
    ```
* Open `examples/myhub.py` file. Press \`Shift+Ctrl+B" on Windows/Linux or "Shift+Cmd+B" on macOS to run build task.

Now let's see what is configured in the `tasks.json` . Recall that we normally run a Switchyard test in terminal with command below:

```bash
$ swyard -t examples/myhub_testscenario.py examples/myhub.py
```

Line 9 of `tasks.json` indicates that we want to run the exact Python we configured in `settings.json` as `python.pythonPath` . module `switchyard.swyard` (short for `python -m switchyard.swyard` ), which is the exactly `swyard` command we run when we want to start Switchyard.

Line 10-16 store the arguments for `swyard` command to run.

*   `{fileDirname}/${fileBasenameNoExtension}_testscenario.py` in `launch.json` uses some _predefined variables_ (that starts with '$") in VS Code:

    * `${fileDirname}` : the directory name of current opened file. So when we opened `examples/myhub.py` in the editor, the `${fileDirname}` matches the directory of `examples`&#x20;
    * `${fileBasenameNoExtension}` : the base name of current file with no extension which means when we opened `examples/myhub.py` , this variable matches `myhub`&#x20;

    So when we join the variables above, we will have

    `${fileDirname}/${fileBasenameNoExtension}_testscenario.py`

    corresponding to something like:

    `"examples" + "/" + "myhub" "_testscenario.py"`

    which results in

    `examples/myhub_testscenario.py`
* `${file}` is the current file name which in this case is `examples/myhub.py` .

So the `tasks.json` is configured to run a command:

```bash
$ /home/njucs/switchyard/syenv/bin/python3 -m switchyard.swyard -t \ 
examples/myhub_testscenario.py examples/myhub.py
```

You may wonder why this command is more complicated. In this case, when we have activated the `syenv` environment and run `swyard` command, it is actually short for `/home/njucs/switchyard/syenv/bin/python3 -m switchyard.swyard` . The activation resolves the full path for you, so you can run the command simply. But in VS Code you should configure the full path.

## Configure Debug Procedure

You can replace pdb with VSC debugger. This will work in the test environment. For VSC, you need to create debugging configuration.

For example, if you want to debug `examples/myhub.py` with `examples/myhub_testscenario.py`, you need to create a `launch.json` for configuration.

1. Click the "Run and debug" icon in Activity Bar on the left (which is usually a little beetle), and click "create a launch.json file". Then choose "Python file". This will create a file named `.vscode/launch.json` in your opened directory which stores the configuration of the debugger.

![](../.gitbook/assets/vsc-launch-json.png)

1.  Edit `.vscode/launch.json` as follows:

    ```javascript
    {
        // Use IntelliSense to learn about possible attributes.
        // Hover to view descriptions of existing attributes.
        // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
        "version": "0.2.0",
        "configurations": [
            {
                "name": "Python: Switchyard Test",
                "type": "python",
                "request": "launch",
                "module": "switchyard.swyard",
                "console": "integratedTerminal",
                "args": [
                    "-t",
                    "${fileDirname}/${fileBasenameNoExtension}_testscenario.py",
                    "${file}",
                    "--nohandle"
                ]
            }
        ]
    }
    ```

    The configuration is similar to `tasks.json` described above. The `--nohandle` is a flag for Switchyard not to trap exception which is described in its [documentation](https://pavinberg.gitee.io/switchyard/test\_execution.html#if-you-don-t-like-pdb).
2. Open `examples/myhub.py` file. Set a breakpoint in your code and click the configuration to debug. Or just simply click "Run -- Start Debugging" or press "F5" to start debugging `examples/myhub.py` .

You can try this in your configuration to easily debug your Switchyard program for later experiments.
