# Python
## Enhanced Performance in Python 3.6 vs 3.11
- Create a SLES 15 server which it will have python 3.6 and 3.11 
  Deploy this VM. This will automatically create a VM.
  
  [![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjonathanbrenes%2Fmentorship%2Frefs%2Fheads%2Fmain%2Fpython001.json)
  
- Demonstrating Enhanced Performance in Python 3.11 vs 3.6 Using an N-body Simulation Script
  
  This will showcase the improved performance of Python 3.11 over Python 3.6 by running an extended N-body simulation. The script calculates the gravitational interactions between celestial bodies, providing a computationally intensive benchmark to highlight the efficiency gains in Python 3.11.
- Execute the python script using different versions
  Use python 3.6 and 3.11 available on SLES 15

  ```bash
  python3.6 /usr/local/bin/nbody.py
  python3.11 /usr/local/bin/nbody.py
  ```
 > **Note:** Result of the script will be faster with 3.11. That's one of reasons everybody is moving to newest python version

## Python Virtual Environment
- Set a [Python Virtual Environment](https://docs.python.org/3/library/venv.html)
  Create an environment for python 3.6 and another one for python 3.11
  ```bash
  python3.6 -m venv p36env # Create python virtual environment for version 3.6 inside p36env directory
  python3.11 -m venv p36env # Create python virtual environment for version 3.11 inside p311env directory
  source p36env/bin/activate # Activate virtual environment for 3.6
  python3 --version # This will return version 3.6
  source p311env/bin/activate # Activate virtual environment for 3.11
  python3 --version # This will return version 3.11
  ```
- Using pip inside each python virtual environment
  The management of each python package through pip is individual for each environment. So it is possible to install different packages and version over different environments
  ```bash
  source p36env/bin/activate # Activate the virtualment environment for 3.6
  pip list # List installed packages
  pip install azure-identity # Install azure-identity package and its required modules
  source p311env/bin/activate # Activate the virtualment environment for 3.11
  pip list # List installed packages. Notice the packages from azure-identity are not installed
  pip install azure-identity # Install azure-identity package and its required modules
  pip list # List installed packages. Compare the list of packages and the versions
  ```

> Using python virtual environments is the correct way to implement different versions of python without messing with the default OS python installed
