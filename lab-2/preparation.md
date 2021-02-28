# Task 1: Preparation

1. Clone the [Lab-2 assignment](https://classroom.github.com/a/HimykJzS) template codes.
2. `myswitch.py` is a template code. Make 4 copies of `myswitch.py` to: 1. `myswitch.py`: Your basic learning switch. 2. `myswitch_to.py`: Your learning switch with timeout based entry removal. 3. `myswitch_lru.py`: Your learning switch with LRU based entry removal. 4. `myswitch_traffic.py`: Your learning switch with traffic volume based entry removal.
3. \(Optional\) Create your test files. 1. `mytests.py`: Your test file of `myswitch.py`. 2. `mytests_to.py`: Your test file of `myswitch_to.py`. 3. `mytests_lru.py`: Your test file of `myswitch_lru.py`. 4. `mytests_traffic.py`: Your test file of `myswitch_traffic.py`.

Though we will provide the test files, they are incomprehensible. So you should still write test scenarios that test all aspects of your code. You can find our test files in `testcases/`.

Finally, your project will look like:

```text
.
├── myswitch.py
├── myswitch_lru.py
├── myswitch_to.py
├── myswitch_traffic.py
├── start_mininet.py
├── ...
└── testcases
    ├── switchtests_lru.srpy
    ├── switchtests_to.srpy
    └── switchtests_traffic.srpy
```

