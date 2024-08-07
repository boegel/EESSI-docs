# Available software

`available_software.py` is a script that generates an overview of all software modules that are available on an HPC system.
It also generates a detailed overview per software that includes specific software versions.
To do this, it generates 3 things:
1. `json_data.json`: This JSON file is used to populate the global overview table.
2. `json_data_detail.json`: This JSON file is used to automatically generate all the detailed markdown pages.
3. A lot of MarkDown files: These are the detailed overview pages per software.

The generated files will be placed in the [available_software](/docs/available_software) directory,
more specifically the [data](/docs/available_software/data) 
and [detail](/docs/available_software/detail) subdirectories.

## Requirements
- Required Python packages are listed in `requirements.txt` and `requirements_tests.txt`
- [Lmod](https://github.com/TACC/Lmod) must be available, and `$LMOD_CMD` must specify the path to the `lmod` binary.


### Creating a virtual environment (optional)

If the required Python packages are not available in your Python setup,
you can easily create a dedicated virtual environment as follows:

```shell
python -m venv module_overview_venv
source module_overview_venv/bin/activate
pip install -r requirements.txt
pip install -r requirements_tests.txt
# to exit the virtual environment, run 'deactivate'
```

## Usage
You can run the script with following command:

```shell
python available_software.py
```

## Testing
You can run the tests by running the `test.sh` script.
```shell
./test.sh
```

The tests make use of a mocked `$LMOD_CMD` script, which you can find [here](tests/data/lmod_mock.sh).

### Write tests
If you want to write additional tests and use the script effectively, follow these guidelines:


1. **Setting up mocked Lmod:**

   Before each test, ensure that you set the path to the script that mocks the `lmod` binary.
   This can be done within the setup_class function.
   ```python
   path = os.path.dirname(os.path.realpath(__file__))
   
   @classmethod
   def setup_class(cls):
       os.environ["LMOD_CMD"] = cls.path + "/data/lmod_mock.sh"
   ``` 

### Example 
An example of a possible `setup_class` function is given below.
```python
import os

@classmethod
def setup_class(cls):
    os.environ["TESTS_PATH"] = cls.path
    os.environ["LMOD_CMD"] = cls.path + "/data/lmod_mock.sh"
    os.environ["SHELL"] = cls.path + "/data/bash_mock.sh"
    os.environ["MOCK_FILE_SWAP"] = cls.path + "/data/data_swap_TARGET.txt"
    os.environ["MOCK_FILE_AVAIL_TARGET"] = cls.path + "/data/data_avail_target_simple.txt"
    os.environ["MOCK_FILE_AVAIL_TARGET_AMD_INTEL"] = cls.path + "/data/data_avail_target_amd_intel.txt"
```

This does multiple things:
1. Set the path of the tests folder in `$TESTS_PATH`
2. Set the path to the `lmod_mock.sh` script in the environment variable `$LMOD_CMD`
3. set the path to the `bash_mock.sh` script in the environment variable `$SHELL`
4. Set the output file for the `module swap` to the `MOCK_FILE_SWAP` variable.
   For example, `data/data_swap_generic.txt` could be a possible file.
3. Set the output file for the `module avail` to the `MOCK_FILE_AVAIL_TARGET` variable.
   The actual output can be found in the `data/data_avail_target_simple.txt` file or `/data/data_avail_target_amd_intel.txt` file.
