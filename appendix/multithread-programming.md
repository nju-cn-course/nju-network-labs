# Multithread programming

Multithread programming pattern is commonly used in network applications. As you may notice in the labs, a network device usually have to handle different events on different interfaces concurrently. Multithread programmming is a proper way to address this need.

### Multithread in Python

You need to use Python standard module `threading` to implement multithread Python program. You can refer to [official documentation](https://docs.python.org/3.6/library/threading.html) or [RealPython tutorial](https://realpython.com/intro-to-python-threading/) which is an easy-to-understand tutorial to learn about this module.

### Switch branch of Switchyard

Switchyard itself is thread-safe so that you can run your program in Mininet just as the same. However, the test framework of Switchyard you are using does not totally support multithread programming pattern. You may failed the test script even though your program is correct.

To address this issue, we developed a branch of Switchyard named `dev` which supports multithread program testing. To use this feature, you need to switch to this branch. First go to your Switchyard repository directory.

1. Check that remote branch `dev` exists. List all the branches:

   ```bash
   (syenv) $ git branch -a
   ```

2. Switch to `dev`:

   ```bash
   (syenv) $ git checkout dev
   ```

   > If you have multiple remotes, refer to this [answer](https://stackoverflow.com/a/1783426).

3. Pull the latest commit:

   ```bash
   (syenv) $ git pull
   ```

4. Create a new Python virtual environment and install the new Switchyard:

   Exit syenv:

   ```bash
   (syenv) $ deactivate
   ```

   Create a new virtual environment `swmtenv` \(or another name if you prefer\):

   ```bash
   $ python3 -m venv swmtenv
   ```

   Install Switchyard:

   ```bash
   (swmtenv) $ python3 -m pip install .
   ```

   \(Optional\) If you’d like to develop the switchyard framework, you can install in development mode \(you don’t need to reinstall every time you modify the code\):

   ```bash
   (swmtenv) $ python3 -m pip intall -e .
   ```

Now you have a new environment for this new Switchyard that supports multithread. You should see the first "INFO" ending with "for multithread":

```text
01:23:45 2021/01/01     INFO Starting test scenario testscenario.srpy for multithread
```

### Contribution

This test framework is still in experimental stage which is not stable. At most of the time it should work fine but you may also find issues when using this framework such as not logging as usual or stuck because one thread dies.

Though this kind of issue is annoying sometimes, it is also a good chance to improve your skills and become more experienced. We encourage you to figure out the problem yourself and fix it. If you’d like to contribute to Switchyard, fork our [GitHub switchyard repository](https://github.com/pavinberg/switchyard) and raise pull request. We believe you can find the details about it on your own.

### Score

If you complete the lab in multithread programming pattern, note this in your report and we will give you extra points.

If you find a bug, or solved the issue by yourself, you will get more extra points as appropriate.

