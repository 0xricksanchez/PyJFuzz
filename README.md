**PyJFuzz** is a small, extensible and ready-to-use framework used to **fuzz JSON inputs**, such as mobile endpoint REST API, JSON implementation, Browsers, cli executable and much more.

### Note:
 
This fork of PyJFuzz was made python3 compatible with minimal effort



Installation
============

You can install PyJFuzz with the following command
```{r, engine='bash', count_lines}
sudo -EH pip3 install gramfuzz bottle netifaces gitpython
git clone https://github.com/0xricksanchez/PyJFuzz.git && cd PyJFuzz && sudo -EH python3 setup.py install
```

Usage:
============

**Classes**

The available object/class are the following:

- ***PJFServer*** - User to start and stop built-in HTTP and HTTPS servers
- ***PJFProcessMonitor*** - Used to monitor process crash, it will automatically restart proccess each time it crash
- ***PJFTestcaseServer*** - The testcase server is used in conjunction with PJFProcessMonitor, whenever a process crash the testcase server will register and store the JSON which cause the crash
- ***PJFFactory*** - It's the main object used to do the real fuzz of JSON objects
- ***PJFConfiguration*** - It's the configuration file for each of the available objects
- ***PJFExternalFuzzer*** - Used by PJFactory is a auxiliary class which provide an interface to other command line fuzzer such as *radamsa*
- ***PJFMutation*** - Used by PJFFactory provide all the mutation used during fuzzing session
- ***PJFExecutor*** - Provides an interface to interact with external process


**Examples**

```python
# simple_fuzzer

from pyjfuzz.core.pjf_configuration import PJFConfiguration
from pyjfuzz.core.pjf_factory import PJFFactory
from argparse import Namespace

config = PJFConfiguration(argparse.Namespace(json={"test": ["1", 2, True]}, nologo=True, level=6, techniques="P"))
fuzzer = PJFFactory(config)
# print("Techniques IDs: {0}".format(str(config.techniques)))
while True:
    print(fuzzer.fuzzed)
```




**Examples**

Below some trivial example of how-to implement PyJFuzz powered program

*simple_fuzzer.py*
```python
from argparse import Namespace
from pyjfuzz.lib import *

config = PJFConfiguration(Namespace(json={"test": ["1", 2, True]}, nologo=True, level=6))
#print("Techniques IDs: {0}".format(str(config.techniques)))
#print("Techniques IDs: {0}".format(str(config.techniques)))
# you can eventually modify them!
#config.techniques = [2]
# This way only attack number 2 (LFI Attack) will be performed!
fuzzer = PJFFactory(config)
while True:
    print fuzzer.fuzzed
```


**Configuration table**

<table>
  <tr>
    <th>Name</th>
    <th>Type</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>json</td>
    <td><b>dict</b></td>
    <td>JSON object to fuzz</td>
  </tr>
  <tr>
    <td>json_file</td>
    <td><b>str</b></td>
    <td>Path to a JSON file</td>
  </tr>
  <tr>
    <td>parameters</td>
    <td><b>list</b>&lt;str&gt;</td>
    <td>List of parameters to fuzz (taken from JSON object)</td>
  </tr>
  <tr>
    <td>techniques</td>
    <td><b>str</b>&lt;int&gt;</td>
    <td>String of enable attacks, used to generate fuzzed JSON, such as XSS, LFI etc. ie "CHPTRSX" (Look <b>techniques table</b>)</td>
  </tr>
  <tr>
    <td>level</td>
    <td><b>int</b></td>
    <td>Fuzzing level in the range 0-6</td>
  </tr>
  <tr>
    <td>utf8</td>
    <td><b>bool</b></td>
    <td>If true switch from unicode encode to pure byte representation</td>
  </tr>
 <tr>
    <td>indent</td>
    <td><b>bool</b></td>
    <td>Set whenever to indent the result object</td>
  </tr>
  <tr>
    <td>url_encode</td>
    <td><b>bool</b></td>
    <td>Set whenever to URLEncode the result object</td>
  </tr>
  <tr>
    <td>strong_fuzz</td>
    <td><b>bool</b></td>
    <td>Set whenever to use <i>strong fuzzing</i> (strong fuzzing will not maintain JSON structure, usefull for parser fuzzing)</td>
  </tr>
  <tr>
    <td>debug</td>
    <td><b>bool</b></td>
    <td>Set whenever to enable debug prints</td>
  </tr>
  <tr>
    <td>exclude</td>
    <td><b>bool</b></td>
    <td>Exclude from fuzzing parameters selected by parameters option</td>
  </tr>
  <tr>
    <td>notify</td>
    <td><b>bool</b></td>
    <td>Set whenever to notify process monitor when a crash occurs only used with PJFServer</td>
  </tr>
  <tr>
    <td>html</td>
    <td><b>str</b></td>
    <td>Path to an HTML directory to serve within PJFServer</td>
  </tr>
  <tr>
    <td>ext_fuzz</td>
    <td><b>bool</b></td>
    <td>Set whenever to use binary from "command" as an externale fuzzer</td>
  </tr>
    <tr>
    <td>cmd_fuzz</td>
    <td><b>bool</b></td>
    <td>Set whenever to use binary from "command" as fuzzer target</td>
  </tr>
    <tr>
    <td>content_type</td>
    <td><b>str</b></td>
    <td>Set the content type result of PJFServer (default <b>application/json</b>)</td>
  </tr>
  <tr>
    <td>command</td>
    <td><b>list</b>&lt;str&gt;</td>
    <td>Command to execute each paramester is a list element, you could use <b>shlex.split</b> from python</td>
  </tr>
</table>

**Techniques table**

<table>
  <tr>
    <th>Index</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>0</td>
    <td>XSS injection (Polyglot)</td>
  </tr>
  <tr>
    <td>1</td>
    <td>SQL injection (Polyglot)</td>
  </tr>
  <tr>
    <td>2</td>
    <td>LFI attack</td>
  </tr>
  <tr>
    <td>3</td>
    <td>SQL injection polyglot (2)</td>
  </tr>
  <tr>
    <td>4</td>
    <td>XSS injection (Polyglot) (2)</td>
  </tr>
  <tr>
    <td>5</td>
    <td>RCE injection (Polyglot)</td>
  </tr>
  <tr>
    <td>6</td>
    <td>LFI attack (2)</td>
  </tr>
  <tr>
    <td>7</td>
    <td>Data URI attack</td>
  </tr>
  <tr>
    <td>8</td>
    <td>LFI and HREF attack</td>
  </tr>
  <tr>
    <td>9</td>
    <td>Header injection</td>
  </tr>
  <tr>
    <td>10</td>
    <td>RCE injection (Polyglot) (2)</td>
  </tr>
  <tr>
    <td>11</td>
    <td>Generic templace injection</td>
  </tr>
  <tr>
    <td>12</td>
    <td>Flask template injection</td>
  </tr>
  <tr>
    <td>13</td>
    <td>Random character attack</td>
  </tr>
</table>

Issues
=====

Please send any issue here via GitHub I'll provide a fix as soon as possible.

Result
======
*Below a list of know issue found by PyJFuzz, the list will be updated weekly*

- Double free in cJSON (https://github.com/DaveGamble/cJSON/issues/105)
- Unhandled exception in picojson (https://github.com/kazuho/picojson/issues/94)
- Memory leak in simpleJSON (https://github.com/nbsdx/SimpleJSON/issues/8)
- Stack base buffer overflow in frozen (https://github.com/cesanta/frozen/issues/14)
- Memory corruption with custom EIP (https://github.com/cesanta/frozen/issues/15)

